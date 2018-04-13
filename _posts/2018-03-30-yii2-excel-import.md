---
title: Yii2 导入Excel
categories: php
tags:
 - yii
 - excel
 - php
---

## 引入phpoffice/phpexcel

[github地址](https://github.com/PHPOffice/PHPExcel)

在controller中引用

```php
use PHPExcel_IOFactory;
```

<!-- more -->

## controller部分

{% highlight php linenos %}
    // 批量添加用户数据(备注:)
    public function actionImport()
    {
        if ($_FILES) {
            //文件名
            $excelFile = '';
            //文件保存地址，提前创建好并给权限
            $filepath = "/upload/padm-excel";
            //切割文件名
            $arr = explode(".", $_FILES["file"]["name"]);
            //获取文件后缀
            $type = strtolower($arr[count($arr) - 1]);
            if ($type != 'xlsx') {
                return ['code' => 602, 'message' => '只允许上传xlsx文件', 'data' => ''];
            }
            //转存文件名
            $randname = date("Y") . date("m") . date("d") . date("H") . date("i") . date("s") . rand(1000, 9999) . "." . $type;
            //将临时位置的文件移动到指定的目录
            if (is_uploaded_file($_FILES["file"]["tmp_name"])) {
                if (move_uploaded_file($_FILES["file"]["tmp_name"], $filepath . '/' . $randname)) {
                    $excelFile = $filepath . '/' . $randname;
                }
            }
            if (!$excelFile) {
                //文件不存在
                return ['code' => 601, 'message' => '文件上传失败', 'data' => ''];
            } else {
                //读取Excel，xlsx后缀文件用Excel2007，xls后缀用Excel5
                $excelReader = PHPExcel_IOFactory::createReader('Excel2007');
                //载入文件并获取第一个sheet
                $sheet = $excelReader->load($excelFile)->getSheet(0);
                //获取表头
                $tableHeader = [];
                for ($column = 'A'; $column <= 'K'; $column++) {
                    $tableHeader[] = trim($sheet->getCell($column . '1')->getValue());
                }
                if (
                !($tableHeader[0] == '公司'
                    && $tableHeader[1] == '姓名'
                    && $tableHeader[2] == '标签'
                    && $tableHeader[3] == '职位'
                    && $tableHeader[4] == '行业'
                    && $tableHeader[5] == '地区'
                    && $tableHeader[6] == '电话'
                    && $tableHeader[7] == '邮箱'
                    && $tableHeader[8] == '地址'
                    && $tableHeader[9] == '公司座机'
                    && $tableHeader[10] == '备注')
                ) {
                    return ['code' => 603, 'message' => '请使用文件模板上传', 'data' => ''];
                }
                //多少行
                $total_line = $sheet->getHighestRow();
                if ($total_line <= 1) {
                    return ['code' => 604, 'message' => '不可上传空文件', 'data' => ''];
                }
                //插入缓冲区
                $rows = [];
                //插入成功条数
                $totalNum = 0;
                //跳过表头，从第二行开始读取数据
                for ($row = 2; $row <= $total_line; $row++) {
                    //读取一行中每列的数据
                    $excelData = [];
                    for ($column = 'A'; $column <= 'K'; $column++) {
                        $excelData[] = trim($sheet->getCell($column . $row)->getValue());
                    }
                    $dataModel = new Data();
                    $dataModel->company = $excelData[0];
                    $dataModel->name = $excelData[1];
                    $dataModel->tag = $excelData[2];
                    $dataModel->job = $excelData[3];
                    $dataModel->industry = $excelData[4];
                    $dataModel->area = $excelData[5];
                    $dataModel->telephone = $excelData[6];
                    $dataModel->email = $excelData[7];
                    $dataModel->addr = $excelData[8];
                    $dataModel->landline = $excelData[9];
                    $dataModel->remark = $excelData[10];
                    $dataModel->created_at = time();
                    $dataModel->updated_at = time();
                    //验证数据
                    $dataModel->validate();
                    $rows[] = $dataModel->attributes;
                    //每50条插入一次，最后一次不满50条也插入
                    $limit = 50;
                    if (count($rows) == $limit || $row == $total_line) {
                        $num = Yii::$app->db
                            ->createCommand()
                            ->batchInsert(
                                'v_data',
                                ['id', 'company', 'name', 'tag', 'job', 'industry', 'area', 'telephone', 'email', 'addr', 'landline', 'remark', 'created_at', 'updated_at'],
                                $rows
                            )->execute();
                        //插入成功条数
                        $totalNum += $num;
                        //清空缓冲
                        unset($rows);
                    }
                }
                return ['code' => 200, 'message' => '文件上传成功，共'.($total_line-1).'条，录入成功' . $totalNum . '条', 'data' => ''];
            }
        }
        return ['code' => 601, 'message' => '文件上传失败', 'data' => ''];
    }
{% endhighlight %}



> ***转载使用注明出处。原文链接 [https://heimo-he.github.io/2018/03/30/yii2-excel-import/](https://heimo-he.github.io/2018/03/30/yii2-excel-import/)***