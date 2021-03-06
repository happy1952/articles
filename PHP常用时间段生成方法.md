# PHP 常用时间段生成方法

``` php 
// ------------ 日 ------------
// d	月份中的第几天，有前导零的 2 位数字，如：01 到 31
// D	星期中的第几天，文本表示，3 个字母	Mon 到 Sun
// j	月份中的第几天，没有前导零	1 到 31
// l	"L"的小写字母，星期几，完整的文本格式，如：Sunday 到 Saturday
// N	ISO-8601 格式数字表示的星期中的第几天（PHP 5.1.0 新加）1（表示星期一）到 7（表示星期天）
// S	每月天数后面的英文后缀，2 个字符 st，nd，rd 或者 th。可以和 j 一起用
// w	星期中的第几天，数字表示 0（表示星期天）到 6（表示星期六）
// z	年份中的第几天 0 到 365
// ------------ 星期 ------------
// W	ISO-8601 格式年份中的第几周，每周从星期一开始（PHP 4.1.0 新加的）例如：42（当年的第 42 周）
// ------------ 月 ------------
// F	月份，完整的文本格式，例如 January 或者 March	January 到 December
// m	数字表示的月份，有前导零 01 到 12
// M	三个字母缩写表示的月份 Jan 到 Dec
// n	数字表示的月份，没有前导零 1 到 12
// t	指定的月份有几天 28 到 31
// ------------ 年 ------------
// L	是否为闰年 如果是闰年为 1，否则为 0
// o	ISO-8601 格式年份数字。这和 Y 的值相同，只除了如果 ISO 的星期数（W）属于前一年或下一年，则用那一年。（PHP 5.1.0 新加）例如： 1999 or 2003
// Y	4 位数字完整表示的年份 例如：1999 或 2003
// y	2 位数字表示的年份 例如：99 或 03
// ------------ 时间 ------------
// a	小写的上午和下午值 am 或 pm
// A	大写的上午和下午值 AM 或 PM
// B	Swatch Internet 标准时 000 到 999
// g	小时，12 小时格式，没有前导零 1 到 12
// G	小时，24 小时格式，没有前导零 0 到 23
// h	小时，12 小时格式，有前导零 01 到 12
// H	小时，24 小时格式，有前导零 00 到 23
// i	有前导零的分钟数 00 到 59>
// s	秒数，有前导零 00 到 59>
// u	毫秒（PHP 5.2.2 新加）。需要注意的是 date() 函数总是返回 000000 因为它只接受 integer 参数，而 DateTime::format() 才支持毫秒。例如: 654321
// ------------ 时区 ------------
// e	时区标识（PHP 5.1.0 新加）例如：UTC，GMT，Atlantic/Azores
// I	是否为夏令时 如果是夏令时为 1，否则为 0
// O	与格林威治时间相差的小时数	例如：+0200
// P	与格林威治时间（GMT）的差别，小时和分钟之间有冒号分隔（PHP 5.1.3 新加）例如：+02:00
// T	本机所在的时区 例如：EST，MDT（【译者注】在 Windows 下为完整文本格式，例如“Eastern Standard Time”，中文版会显示“中国标准时间”）。
// Z	时差偏移量的秒数。UTC 西边的时区偏移量总是负的，UTC 东边的时区偏移量总是正的。例如：-43200 到 43200
// ------------ 完整的日期／时间 ------------
// c	ISO 8601 格式的日期（PHP 5 新加）	2004-02-12T15:19:21+00:00
// r	RFC 822 格式的日期 例如：Thu, 21 Dec 2000 16:01:07 +0200
// U	从 Unix 纪元（January 1 1970 00:00:00 GMT）开始至今的秒数	参见 time()

// 昨天的起止时间
echo "昨天的起止时间：\n";
echo date('Y-m-d H:i:s', mktime(0, 0, 0, date('m'), date('d') - 1, date('Y'))) . "\n";
echo date('Y-m-d H:i:s', mktime(23, 59, 59, date('m'), date('d') - 1, date('Y'))) . "\n";

// 今天的起止时间
echo "今天的起止时间：\n";
echo date('Y-m-d H:i:s', mktime(0, 0, 0, date('m'), date('d'), date('Y'))) . "\n";
echo date('Y-m-d H:i:s', mktime(23, 59, 59, date('m'), date('d'), date('Y'))) . "\n";

// 明天的起止时间
echo "明天的起止时间：\n";
echo date('Y-m-d H:i:s', mktime(0, 0, 0, date('m'), date('d') + 1, date('Y'))) . "\n";
echo date('Y-m-d H:i:s', mktime(23, 59, 59, date('m'), date('d') + 1, date('Y'))) . "\n";

// 上周的起止时间
echo "上周的起止时间：\n";
echo date('Y-m-d H:i:s', mktime(0, 0, 0, date('m'), date('d') - date('w') + 1 - 7, date('Y'))) . "\n";
echo date('Y-m-d H:i:s', mktime(23, 59, 59, date('m'), date('d') - date('w'), date('Y'))) . "\n";

// 本周的起止时间
echo "本周的起止时间：\n";
echo date('Y-m-d H:i:s', mktime(0, 0, 0, date('m'), date('d') - date('w') + 1, date('Y'))) . "\n";
echo date('Y-m-d H:i:s', mktime(23, 59, 59, date('m'), date('d') - date('w') + 7, date('Y'))) . "\n";

// 下周的起止时间
echo "下周的起止时间：\n";
echo date('Y-m-d H:i:s', mktime(0, 0, 0, date('m'), date('d') - date('w') + 1 + 7, date('Y'))) . "\n";
echo date('Y-m-d H:i:s', mktime(23, 59, 59, date('m'), date('d') - date('w') + 7 + 7, date('Y'))) . "\n";

// 上月的起止时间
echo "上月的起止时间：\n";
echo date('Y-m-d H:i:s', mktime(0, 0, 0, date('m') - 1, 1, date('Y'))) . "\n";
echo date('Y-m-d H:i:s', mktime(23, 59, 59, date('m'), 0, date('Y'))) . "\n";

// 本月的起止时间
echo "本月的起止时间：\n";
echo date('Y-m-d H:i:s', mktime(0, 0, 0, date('m'), 1, date('Y'))) . "\n";
echo date('Y-m-d H:i:s', mktime(23, 59, 59, date('m'), date('t'), date('Y'))) . "\n";

// 下月的起止时间
echo "下月的起止时间：\n";
echo date('Y-m-d H:i:s', mktime(0, 0, 0, date('m') + 1, 1, date('Y'))) . "\n";
echo date('Y-m-d H:i:s', mktime(23, 59, 59, date('m') + 2, 0, date('Y'))) . "\n";

// 上季度起止时间
echo "上季度的起止时间：\n";
$season = ceil((date('n')) / 3) - 1;
echo date('Y-m-d H:i:s', mktime(0, 0, 0, $season * 3 - 3 + 1, 1, date('Y'))) . "\n";
echo date('Y-m-d H:i:s', mktime(23, 59, 59, $season * 3, date('t', mktime(0, 0 , 0, $season * 3, 1, date("Y"))), date('Y'))) . "\n";

// 本季度起止时间
echo "本季度的起止时间：\n";
$season = ceil((date('n')) / 3);
echo date('Y-m-d H:i:s', mktime(0, 0, 0, $season * 3 - 3 + 1, 1, date('Y'))) . "\n";
echo date('Y-m-d H:i:s', mktime(23, 59, 59, $season * 3, date('t', mktime(0, 0, 0, $season * 3, 1, date("Y"))), date('Y'))) . "\n";

// 下季度起止时间
echo "下季度的起止时间：\n";
$season = ceil((date('n')) / 3) + 1;
echo date('Y-m-d H:i:s', mktime(0, 0, 0, $season * 3 - 3 + 1, 1, date('Y'))) . "\n";
echo date('Y-m-d H:i:s', mktime(23, 59, 59, $season * 3, date('t', mktime(0, 0, 0, $season * 3, 1, date("Y"))), date('Y'))) . "\n";
```
