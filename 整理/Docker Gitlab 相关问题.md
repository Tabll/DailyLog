
每次重启 Gitlab 的 Docker 后都需要
```sh
cd /opt/gitlab/embedded/service/gitlab-rails/config
vi gitlab.yml
gitlab-ctl restart
```