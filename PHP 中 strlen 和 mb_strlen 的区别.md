# PHP 中  strlen 和 mb_strlen 的区别

## strlen

获取字符串的长度，以字节为单位。

```php
echo strlen("中国"); // 6
```

## mb_strlen

获取字符串的长度，以字符为单位。可以指定字符编码，如果省略或者为 NULL，将使用内部字符编码。

```php
echo mb_strlen('中国'); // 2

echo mb_strlen('中国', 'UTF-8'); // 2
```

我们可以通过 mb_internal_encoding() 函数设置或获取内部字符编码。

```php
echo mb_internal_encoding(); // UTF-8 获取内部字符编码

mb_internal_encoding('GBK'); // 设置内部字符编码
```

## 常见问题

注意，当 PHP 源码文件的编码和 mb_strlen() 函数指定的编码不统一时，便会出现一些不一致的结果。如下面例子所示：

```php
// 文件的编码格式为 UTF-8
echo mb_internal_encoding() . PHP_EOL; // 默认内部编码(UTF-8)
$str = '中国';
echo mb_strlen($str) . PHP_EOL; // 2
echo mb_strlen($str, 'UTF-8') . PHP_EOL; // 2
echo mb_strlen($str, 'GBK') . PHP_EOL; // 3 ???
echo mb_strlen($str, 'GB2312') . PHP_EOL; // 4 ???
```

同样是“中国”两个字，为什么在 GBK、GB2312 编码下，字符长度却是 3、4？

这是因为我的 PHP 源码文件的编码格式是 UTF-8，所以“中国”两个字在 $str 中就是以 UTF-8 编码格式进行保存的。所以当我们以 GBK 或者 GB2312 编码格式去解析 UTF-8 编码格式时便会出现结果不合预期的情况。

通过查询相应的字符编码表我们可以发现，汉字“中国”的对应的 UTF-8 编码为 E4B8AD E59BBD，当我们以 GBK 编码格式进行解析时会得到一个乱码“涓浗”，所以 mb_strlen() 函数返回的长度是这串乱码的长度 3，同理我们以 GB2312 编码格式进行解析时同样也会得到一个乱码“涓�鍥�”，可以看到这串乱码的长度是 4。这样也就不难解释我们上面遇到的那种情况了。

其实在我们获取字符串长度之前，应该先进行一次转码，保证字符编码的统一就可以解决上面的问题了，再看下面这个例子：

```php
// 文件的编码格式为 UTF-8
$str = '中国';
// 将 UTF-8 编码的字符串转换为 GBK 编码
$str = iconv('UTF-8', 'GBK', $str);
echo mb_strlen($str, 'GBK') . PHP_EOL; // 2

$str = '中国';
// 将 UTF-8 编码的字符串转换为 GB2312 编码
$str = iconv('UTF-8', 'GB2312', $str);
echo mb_strlen($str, 'GB2312') . PHP_EOL; // 2
```

为了方便理解上面的现象，其实我们可以建立几个不同编码格式的文件，并写入“中国”两个字，然后通过 PHP 的文件操作函数去读取它。如下所示：

```php
// 打开 UTF-8 编码格式的文件，并获取其内容长度
$fp = fopen('./UTF8.txt', 'r');
$str = fgets($fp);
echo mb_strlen($str) . PHP_EOL; // 2
echo mb_strlen($str, 'UTF-8') . PHP_EOL; // 2
fclose($fp);

// 打开 GBK 编码格式的文件，并获取其内容长度
$fp = fopen('./GBK.txt', 'r');
$str = fgets($fp);
echo mb_strlen($str, 'GBK') . PHP_EOL; // 2
fclose($fp);

// 打开 GB2312 编码格式的文件，并获取其内容长度
$fp = fopen('./GB2312.txt', 'r');
$str = fgets($fp);
echo mb_strlen($str, 'GB2312') . PHP_EOL; // 2
fclose($fp);
```

* 小技巧：

我们可以使用 VSCode 编辑器新建一个文件，写入“中国”两个字并以 UTF-8 编码格式保存。然后再次打开该文件，在编辑器右下脚点击 "UTF-8"(Select Encoding)，在弹出的对话框中选择 "Reopen with Encoding"，之后再选择想要以哪种编码格式重新加载文件，这样就可以看到不同字符编码去解析 UTF-8 编码格式后的数据了，其实大部分情况下我们都会看到一串乱码。

## 检查字符的编码

1. 示例一

```php
// 文件的编码格式为 GB2312
$str = "中国";
var_dump(mb_detect_encoding($str, 'UTF-8')); // string(5) "UTF-8"
var_dump(mb_detect_encoding($str, 'UTF-8', true)); // bool(false) 想要检查字符串的编码为否是有效的 UTF-8，一定要使用严格模式(strict)，否则它的结果非常不准确，如第一行代码所示。
var_dump(mb_detect_encoding($str, 'GBK', true)); // string(5) "CP936"
var_dump(mb_detect_encoding($str, 'GB2312', true)); // string(6) "EUC-CN"
var_dump(mb_detect_encoding($str, ['UTF-8', 'GB2312', 'GBK'], true)); // string(6) "EUC-CN" 参数中编码顺序不同，会影响最终检测的结果
var_dump(mb_detect_encoding($str, ['UTF-8', 'GBK', 'GB2312'], true)); // string(5) "CP936" 参数中编码顺序不同，会影响最终检测的结果
```

2. 示例二

```php
// 文件的编码格式为 UTF-8
$str = "中国";
var_dump(mb_detect_encoding($str, 'UTF-8', true)); // string(5) "UTF-8"
var_dump(mb_detect_encoding($str, 'GBK', true)); // string(5) "CP936"
var_dump(mb_detect_encoding($str, 'GB2312', true)); // bool(false)
var_dump(mb_detect_encoding($str, ['GB2312', 'GBK', 'UTF-8'], true)); // string(5) "CP936"
var_dump(mb_detect_encoding($str, ['UTF-8', 'GB2312', 'GBK'], true)); // string(5) "UTF-8"
```

3. 示例三

```php
// 文件的编码格式为 UTF-8
$str = "中国龍";
var_dump(mb_detect_encoding($str)); // string(5) "UTF-8"
var_dump(mb_detect_encoding($str, 'UTF-8', true)); // string(5) "UTF-8"
var_dump(mb_detect_encoding($str, 'GBK', true)); // bool(false)
var_dump(mb_detect_encoding($str, 'GB2312', true)); // bool(false)
var_dump(mb_detect_encoding($str, ['GB2312', 'GBK', 'UTF-8'], true)); // string(5) "UTF-8"
var_dump(mb_detect_encoding($str, ['UTF-8', 'GB2312', 'GBK'], true)); // string(5) "UTF-8"
```

4. 示例四

```php
// 文件的编码格式为 GBK
$str = "中国龍";
var_dump(mb_detect_encoding($str)); // string(5) "UTF-8"
var_dump(mb_detect_encoding($str, "auto", true)); // bool(false) // "auto" 将根据 mbstring.language 来扩展
var_dump(mb_detect_encoding($str, mb_detect_order(), true)); // bool(false) // 使用当前的 detect_order 来检测字符编码
var_dump(mb_detect_encoding($str, 'UTF-8', true)); // bool(false)
var_dump(mb_detect_encoding($str, 'GBK', true)); // string(5) "CP936"
var_dump(mb_detect_encoding($str, 'GB2312', true)); // bool(false)
var_dump(mb_detect_encoding($str, ['GB2312', 'GBK', 'UTF-8'], true)); // string(5) "CP936"
var_dump(mb_detect_encoding($str, ['UTF-8', 'GB2312', 'GBK'], true)); // string(5) "CP936"
```

**注意：以上四个示例中对字符串编码的获取竟然能得到多种编码格式，这个我也没有搞明白为什么会这样？希望有知道的朋友不吝赐教，感谢感谢！**
