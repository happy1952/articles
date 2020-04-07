# PhpSpreadsheet 使用详解  

今天是2020年2月26日！

最近因为要做一些报表类的开发，需要将数据导出为 Excel 。中间使用到了 PhpSpreadsheet ，于是就将使用过程中的一些笔记记录在此，方便自己查阅。

## 目录

- 简介
- 安装
- 使用
- 示例及常见操作

### 简介

> PhpSpreadsheet 是一个纯PHP编写的库，提供了一组类，可以用来读取和写入不同的电子表格文件格式，例如：Excel 和 LibreOffice Calc。

### 安装

使用 [composer](https://getcomposer.org/)进行安装：

```bash
composer require phpoffice/phpspreadsheet
```

### 使用

```php
<?php
    require 'vendor/autoload.php';

    use PhpOffice\PhpSpreadsheet\Spreadsheet;
    use PhpOffice\PhpSpreadsheet\Writer\Xlsx;

    $spreadsheet = new Spreadsheet();
    $sheet = $spreadsheet->getActiveSheet();
    $sheet->setCellValue('A1', 'Hello World !');

    $writer = new Xlsx($spreadsheet);
    $writer->save('hello world.xlsx');
?>
```

### 示例及常见操作

#### 查看示例

Phpspreadsheet 本身的文档已经很详细了，而且里面提供了非常详尽的示例，可以通过下面的命令查看：

```bash
php -S localhost:8000 -t vendor/phpoffice/phpspreadsheet/samples
```

然后在浏览器打开链接：

[http://localhost:8000](http://localhost:8000)

#### 访问单元

```php
// 通过坐标设置单元格的值
// 在单元各种设置字符
$spreadsheet->getActiveSheet()->setCellValue('A1', 'PhpSpreadsheet');

// 在单元各种设置数值
$spreadsheet->getActiveSheet()->setCellValue('A2', 12345.6789);

// 在单元格中设置布尔值
$spreadsheet->getActiveSheet()->setCellValue('A3', TRUE);

// 在单元格中设置公式
$spreadsheet->getActiveSheet()->setCellValue(
    'A4',
    '=IF(A3, CONCATENATE(A1, " ", A2), CONCATENATE(A2, " ", A1))'
);

// 在单元格中设置日期
$dateTimeNow = time();
$excelDateValue = \PhpOffice\PhpSpreadsheet\Shared\Date::PHPToExcel( $dateTimeNow );
$spreadsheet->getActiveSheet()->setCellValue(
    'A6',
    $excelDateValue
);
// Set the number format mask so that the excel timestamp will be displayed as a human-readable date/time
$spreadsheet->getActiveSheet()->getStyle('A6')
    ->getNumberFormat()
    ->setFormatCode(
        \PhpOffice\PhpSpreadsheet\Style\NumberFormat::FORMAT_DATE_DATETIME
    );

// 在单元格中使用前导零设置数字
// 方式一：将数字转换为字符串
$spreadsheet->getActiveSheet()->setCellValueExplicit(
    'A8',
    "01513789642",
    \PhpOffice\PhpSpreadsheet\Cell\DataType::TYPE_STRING
);
// 方式二：
$spreadsheet->getActiveSheet()->setCellValue('A9', 1513789642);
// Set a number format mask to display the value as 11 digits with leading zeroes
$spreadsheet->getActiveSheet()->getStyle('A9')
    ->getNumberFormat()
    ->setFormatCode(
        '00000000000'
    );
// 方式三：
$spreadsheet->getActiveSheet()->setCellValue('A10', 1513789642);
// Set a number format mask to display the value as 11 digits with leading zeroes
$spreadsheet->getActiveSheet()->getStyle('A10')
    ->getNumberFormat()
    ->setFormatCode(
        '029-8888-8888'
    );

// 设置数组中的单元格范围
$arrayData = [
    [NULL, 2010, 2011, 2012],
    ['Q1',   12,   15,   21],
    ['Q2',   56,   73,   86],
    ['Q3',   52,   61,   69],
    ['Q4',   30,   32,    0],
];
$spreadsheet->getActiveSheet()
    ->fromArray(
        $arrayData,  // The data to set
        NULL,        // Array values with this value will not be set
        'C3'         // Top left coordinate of the worksheet range where
                     // we want to set these values (default is A1)
    );

// 将一维数组写成一列
$rowArray = ['Value1', 'Value2', 'Value3', 'Value4'];
$columnArray = array_chunk($rowArray, 1);
$spreadsheet->getActiveSheet()
    ->fromArray(
        $columnArray,   // The data to set
        NULL,           // Array values with this value will not be set
        'C3'            // Top left coordinate of the worksheet range where
                        // we want to set these values (default is A1)
    );

// 通过坐标获取单元格的值
$cellValue = $spreadsheet->getActiveSheet()->getCell('A1')->getValue();

// 如果单元格包含公式，我们需要获取值而不是公式本身时，可以使用 getCalculatedValue
$cellValue = $spreadsheet->getActiveSheet()->getCell('A4')->getCalculatedValue();

// 获取单元格的值，以人类可读的方式
$cellValue = $spreadsheet->getActiveSheet()->getCell('A6')->getFormattedValue();

// 按列和行设置单元格值
$spreadsheet->getActiveSheet()->setCellValueByColumnAndRow(1, 5, 'PhpSpreadsheet');

// 按列和行检索单元格值
$cellValue = $spreadsheet->getActiveSheet()->getCellByColumnAndRow(2, 5)->getValue();
// 获取计算后的值
$cellValue = $spreadsheet->getActiveSheet()->getCellByColumnAndRow(1, 4)->getCalculatedValue();

// 检索单元格值范围到数组
$dataArray = $spreadsheet->getActiveSheet()
    ->rangeToArray(
        'C3:E5',     // 要检索的工作表的范围
        NULL,        // 单元格为空时返回 NULL
        TRUE,        // Should formulas be calculated (the equivalent of getCalculatedValue() for each cell)
        TRUE,        // Should values be formatted (the equivalent of getFormattedValue() for each cell)
        TRUE         // 数组按照单元格的列和行作为键值
    );

// 使用迭代器遍历单元格
$reader = \PhpOffice\PhpSpreadsheet\IOFactory::createReader('Xlsx');
$reader->setReadDataOnly(TRUE);
$spreadsheet = $reader->load("test.xlsx");

$worksheet = $spreadsheet->getActiveSheet();

echo '<table>' . PHP_EOL;
foreach ($worksheet->getRowIterator() as $row) {
    echo '<tr>' . PHP_EOL;
    $cellIterator = $row->getCellIterator();
    $cellIterator->setIterateOnlyExistingCells(FALSE);
    // This loops through all cells,
    // even if a cell value is not set.
    // By default, only cells that have a value
    // set will be iterated.
    foreach ($cellIterator as $cell) {
        echo '<td>' . $cell->getValue() . '</td>' . PHP_EOL;
    }
    echo '</tr>' . PHP_EOL;
}
echo '</table>' . PHP_EOL;
```
