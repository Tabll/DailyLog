```php
class Solution {

    /**
     * @param Integer[] $nums
     * @param Integer $target
     * @return Integer[]
     */
    function twoSum($nums, $target) {

        $numsMirror = $nums;
        $newNums = array_flip($nums);
        foreach($numsMirror as $key => $num1) {
            $value = $target - $num1;
            if (array_key_exists($value, $newNums)) {
                if ($key != $newNums[$value]) {
                    return [$key, $newNums[$value]];
                }
            }
        }
    }
}

```