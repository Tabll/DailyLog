---
export_on_save:
  html: true
---

> 转载，有修改：
> 参考：[生成 GitLab EE 许可证](https://blog.starudream.cn/2020/01/19/6-crack-gitlab/)
> 参考：[Gitlab::License](https://www.rubydoc.info/gems/gitlab-license/1.0.0/file/README.md)

不以盈利为目的，仅供学习 ruby 相关的签名技术

免费广告非常好用的Gitlab：https://about.gitlab.com/pricing/

建议购买，不过普通版也完全够用，高级版的大功能基本上用不到，小功能也不都是必要的

### 安装 `Ruby`

Gitlab 70% 的代码都是 `ruby` 写的

下载并安装 `Ruby` ：https://rubyinstaller.org/downloads/
安装选项都是默认即可

### 安装 `gitlab-license`

```shell
gem install gitlab-license
```

新建 `license.rb` 文件：

```ruby
require "openssl"
require "gitlab/license"

key_pair = OpenSSL::PKey::RSA.generate(2048)
File.open("license_key", "w") { |f| f.write(key_pair.to_pem) }

public_key = key_pair.public_key
File.open("license_key.pub", "w") { |f| f.write(public_key.to_pem) }

private_key = OpenSSL::PKey::RSA.new File.read("license_key")
Gitlab::License.encryption_key = private_key

license = Gitlab::License.new
license.licensee = {
  "Name" => "none",
  "Company" => "none",
  "Email" => "example@test.com",
}
license.starts_at = Date.new(2020, 1, 1) # 开始时间
license.expires_at = Date.new(2050, 1, 1) # 结束时间
license.notify_admins_at = Date.new(2049, 12, 1)
license.notify_users_at = Date.new(2049, 12, 1)
license.block_changes_at = Date.new(2050, 1, 1)
license.restrictions = {
  active_user_count: 10000,
}

puts "License:"
puts license

data = license.export
puts "Exported license:"
puts data
File.open("GitLabBV.gitlab-license", "w") { |f| f.write(data) }

public_key = OpenSSL::PKey::RSA.new File.read("license_key.pub")
Gitlab::License.encryption_key = public_key

data = File.read("GitLabBV.gitlab-license")
$license = Gitlab::License.import(data)

puts "Imported license:"
puts $license

unless $license
  raise "The license is invalid."
end

if $license.restricted?(:active_user_count)
  active_user_count = 10000
  if active_user_count > $license.restrictions[:active_user_count]
    raise "The active user count exceeds the allowed amount!"
  end
end

if $license.notify_admins?
  puts "The license is due to expire on #{$license.expires_at}."
end

if $license.notify_users?
  puts "The license is due to expire on #{$license.expires_at}."
end

module Gitlab
  class GitAccess
    def check(cmd, changes = nil)
      if $license.block_changes?
        return build_status_object(false, "License expired")
      end
    end
  end
end

puts "This instance of GitLab Enterprise Edition is licensed to:"
$license.licensee.each do |key, value|
  puts "#{key}: #{value}"
end

if $license.expired?
  puts "The license expired on #{$license.expires_at}"
elsif $license.will_expire?
  puts "The license will expire on #{$license.expires_at}"
else
  puts "The license will never expire."
end
```

双击执行该文件，会生成三个文件

![](https://tabll-1252262977.cos.ap-shanghai.myqcloud.com/others/gitlab-ee-license1.png)

### 替换公私密钥

`/opt/gitlab/embedded/service/gitlab-rails# vi .license_encryption_key.pub`

原有的 `KEY`

```php
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA0Hxv3MkkZbMrKtIs6np9
ccP4OwGBkNhIvhPjcQP48hbbascv5RqsOquQGrYSD2ZrE/kbkRdkIcoHEeTZLif+
bDKFZFI7o5x0H92o9/GSvxHJhQ8mkmvwxD7lssGShwZEm8WG+U7BZqUV/gGmCDqe
9W8H8Fq2B0ck8IXjbQ4Zz+JlyV/NHZTZcs69plFiLKh4N6GYVftOVwSomh0bbypP
OB9WnLC7RC9a2LRrhtf8sqa2rRFmtyMMfgFFzLMzS+w+1K4+QLnWP1gKQVzaFnzk
pnwKPrqbGFYbRztIVEWbs8jPYlLkGb8ME4C84YVtQgbQcbyisU/VW3wUGkhT+J0k
xwIDAQAB
-----END PUBLIC KEY-----
```

现有的 `KEY`

```php
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA0JUeDeEuZ0SIGwGJ459f
4rPDdW+xT0XNW/wMdDN8qvEcvHgD74FvtIODl9sFwPDECB3RdV1o47FNHZalxDVy
xAYisWs+TD5pqp3CUhFLyFtYxN7+Uof7ekIAJD2Ng7htQ11/2rGSeuMKEr1Shedl
PWAH4Q7lf4FU4JU0tDNubXCZNPDMRInc6Aq8fKjPLVwA47lhT827Q8or01a/r7vw
/XlrwMfjQU9osXeTCNjXz2lpUuUfqVksHLPeDQHGrb9Q4NrEeP9qAL0pFSad1XN0
P63YyWG9lVpeBpzZwh4OTWexEls/OVpmU7xkZZWBUnNW2L7bSA0o8XMhowtTiLjx
4wIDAQAB
-----END PUBLIC KEY-----
```

### 上传 License

修改完上传后缀为 `.gitlab-license` 的文件

源代码：https://gitlab.com/gitlab-org/gitlab/-/blob/v13.1.4-ee/ee/app/models/license.rb#L397

### 修改完整功能

`/opt/gitlab/embedded/service/gitlab-rails/ee/app/models/license.rb`

```git
  def plan
-    restricted_attr(:plan).presence || STARTER_PLAN
+    restricted_attr(:plan).presence || ULTIMATE_PLAN
  end
```

重启即可
