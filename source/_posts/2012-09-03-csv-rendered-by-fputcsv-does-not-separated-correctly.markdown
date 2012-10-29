---
layout: post
title: "CSV rendered by fputcsv does not separated correctly"
date: 2012-09-03 17:57
comments: true
categories: php,csv,yii
---

Recently I am working on a feature which exports the data presented on the web page to a csv file. That's pretty easy, huh? The only trouble is the web site was built with **Yii Framework**...

Luckily, I found there's already an light-weight extention called [csvexport](http://www.yiiframework.com/extension/csvexport) and you can find it here http://www.yiiframework.com/extension/csvexport

The usage example is pretty clear:
```php
     Yii::import('ext.ECSVExport');
     // CDbCommand
     $cmd = Yii::app()->db->createCommand("SELECT * FROM track_test LIMIT 10");    
     $csv = new ECSVExport($cmd);       
     $exclude = array('id', 'deleted', 'prefix');
     $csv->setExclude($exclude);
     $csv->setOutputFile($outputFile);
     $content = $csv->toCSV();
```

if you would like to download csv from browser instead of writing to a file, add this line:
```php
     Yii::app()->getRequest()->sendFile('export.csv', $content, "text/csv", false);
     exit;
```

Everything goes well so far. But when I open the new csv file with Excel 2008 (Mac), I found there's a problem for human reading, because all data (more than 10 columns) are crowded in the first column, not separated by comma at all!!!

I checked the csv file carefully by opening it with textmate and found it's rendering correctly, although not all data have the double quote enclosure. It does not effect the result.

The "csvexport" lib is using [fputcsv](http://cn.php.net/manual/en/function.fputcsv.php) to render csv.
```php
     fputcsv($this->_filePointer, $headers, $this->_delimiter, $this->_enclosure);
```

The default delimiter is comma - "," how about changing it to another one? Let's try semicolon, Oops!!! It's working great now.
```php
     fputcsv($this->_filePointer, $headers, ";", $this->_enclosure);
```

Is it a Microsoft Office bug or php bug? Let me know your idea!
