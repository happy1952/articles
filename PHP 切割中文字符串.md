## 中文字符串拆分成数组

1. 使用 mb 系列函数

```php
mb_internal_encoding('UTF-8');
$str = "中国龍";
$len = mb_strlen($str);
$arr = [];
for ($i = 0; $i < $len; $i++) {
    array_push($arr, mb_substr($str, $i, 1));
}
var_dump($arr);

```
