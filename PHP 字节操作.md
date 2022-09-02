# PHP 字节操作

### 获取字符串的编码值

```php
# 以 UTF-8 编码格式保存 PHP 文件时
echo bin2hex('h') . PHP_EOL; // 68
echo bin2hex('hello') . PHP_EOL; // 68656c6c6f
echo bin2hex('你') . PHP_EOL; // e4bda0
echo bin2hex('你好') . PHP_EOL; // e4bda0e5a5bd

# 以 GBK 编码格式保存 PHP 文件时
echo bin2hex('h') . PHP_EOL; // 68
echo bin2hex('hello') . PHP_EOL; // 68656c6c6f
echo bin2hex('你') . PHP_EOL; // c4e3
echo bin2hex('你好') . PHP_EOL; // c4e3bac3
```

### 按字节处理字符串

```php
$str = '你好';
for ($i = 0; $i < strlen($str); $i++) { 
    echo '十六进制：' . bin2hex($str[$i]) . '，十进制：' . ord($str[$i]) . PHP_EOL;
}
```

```php
$arr = ['你', '好', '世', '界'];
for ($i = 0; $i < count($arr); $i++) { 
    $tmp = mb_ord($arr[$i]) ^ ord('t');
    // 按 Unicode 码位值返回字符
    echo mb_chr($tmp, 'UTF-8') . PHP_EOL;
}
```

### 获取字符的编码格式

```php
$str = '你好';
// 使用当前的 detect_order 来检测字符编码
echo mb_detect_encoding($str) . PHP_EOL;
// "auto" 将根据 mbstring.language 来扩展
echo mb_detect_encoding($str, 'auto') . PHP_EOL;
// 通过数组来指定编码列表 encoding_list
echo mb_detect_encoding($str, ['ASCII', 'UTF-8', 'GBK', 'GB2312', 'BIG5']) . PHP_EOL; // CP936 就是 GBK 编码
```

### 转换字符的编码

```php
// 推荐使用
$str = '你好';
echo bin2hex($str) . PHP_EOL; // c4e3bac3
$str = mb_convert_encoding($str, 'UTF-8', 'CP936');
echo bin2hex($str) . PHP_EOL; // e4bda0e5a5bd

// 注意：在某些系统上 iconv 函数可能无法以你预期的那样工作，需要系统上安装 GNU libiconv 库
$str = '你好';
echo bin2hex($str) . PHP_EOL; // c4e3bac3
$str = iconv('GBK', 'UTF-8', $str);
echo bin2hex($str) . PHP_EOL; // e4bda0e5a5bd
```

### 获取字符的 Unicode 点值

```php
# 文件编码 UTF-8
echo mb_ord('你') . PHP_EOL; // 20320
echo dechex(mb_ord('你')) . PHP_EOL; // 4F60 (Unicode 编码值)
echo mb_ord('A', 'UTF-8') . PHP_EOL; // 65
echo bin2hex(mb_convert_encoding('你', 'UTF-16', 'UTF-8')) . PHP_EOL; // 4f60

# 文件编码 GBK
echo mb_ord('你', 'GBK') . PHP_EOL; // 20320
echo dechex(mb_ord('你')) . PHP_EOL; // 4F60 (Unicode 编码值)
```

### 数字间的进制转换

```php
// 二进制转十进制
echo bindec('01000001') . PHP_EOL; // 65
// 十进制转二进制
echo decbin(65) . PHP_EOL; // 1000001
// 二进制转十六进制
echo bin2hex('A') . PHP_EOL; // 41 (十六进制)
// 十六进制转二进制
echo hex2bin('41') . PHP_EOL; // A
// 八进制转十进制
echo octdec('101') . PHP_EOL; // 65
// 十进制转八进制
echo decoct('65') . PHP_EOL; // 101 (八进制)
// 十进制转十六进制
echo dechex('65') . PHP_EOL; // 41 (十六进制)
// 十六进制转十进制
echo hexdec('41') . PHP_EOL; // 65
// 任意进制之间的转换
echo base_convert('65', 10, 2) . PHP_EOL; // 1000001
echo base_convert('65', 10, 8) . PHP_EOL; // 101
echo base_convert('65', 10, 16) . PHP_EOL; // 41
echo base_convert('1000001', 2, 8) . PHP_EOL; // 101
echo base_convert('1000001', 2, 10) . PHP_EOL; // 65
echo base_convert('1000001', 2, 16) . PHP_EOL; // 41
```

### 字符和数字相互转换

```php
// 转换字符串第一个字节为 0-255 之间的值
echo ord('A') . PHP_EOL; // 65
// 返回数字相对应于 ASCII 所指定的单个字符
echo chr(65) . PHP_EOL; // A
```

### 查询指定汉字的字符编码

```php
$str = '你';

// CP936 就是 GBK 编码
$charCode = mb_detect_encoding($str, ['ASCII', 'UTF-8', 'CP936', 'GB2312', 'BIG5']);
echo 'GBK: ' . bin2hex(mb_convert_encoding($str, 'GBK', $charCode)) . PHP_EOL; // GBK: c4e3
echo 'GB2312: ' . bin2hex(mb_convert_encoding($str, 'GB2312', $charCode)) . PHP_EOL; // GB2312: c4e3
echo 'BIG5: ' . bin2hex(mb_convert_encoding($str, 'BIG5', $charCode)) . PHP_EOL; // BIG5: a741
echo 'UTF-8: ' . bin2hex(mb_convert_encoding($str, 'UTF-8', $charCode)) . PHP_EOL; // UTF-8: e4bda0
echo 'UTF-16BE: ' . bin2hex(mb_convert_encoding($str, 'UTF-16BE', $charCode)) . PHP_EOL; // UTF-16BE: 4f60
echo 'UTF-16LE: ' . bin2hex(mb_convert_encoding($str, 'UTF-16LE', $charCode)) . PHP_EOL; // UTF-16LE: 604f
echo 'Unicode: ' . dechex(mb_ord(mb_convert_encoding($str, 'UTF-8', $charCode))) . PHP_EOL; // Unicode: 4f60
```