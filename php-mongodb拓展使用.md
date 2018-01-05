Title: php-mongodb拓展使用
Date: 2018-01-05 11:17:07
Category: php

> php-mongo 其实是一个老的拓展，可能会被废弃，建议大家使用新的拓展

```php
        $dsn = "mongodb://localhost:27017";
        $m = new MongoDB\Driver\Manager($dsn); // 连接
		$start_date = new MongoDB\BSON\UTCDateTime(new DateTime('-1 hour'));
		$end_date = new MongoDB\BSON\UTCDateTime(new DateTime('-1 seconds'));
		$filter = ['ctime' => ['$gt' => $start_date, '$lte' => $end_date]];
		$options = [
			'projection' => ['_id' => 0],
			'sort' => ['ctime' => 1],
		];
		$query = new MongoDB\Driver\Query($filter, $options);
		$cursor = $m->executeQuery('data_test.memory_log', $query);
		$points = [];
		foreach ($cursor as $document) {
			$points[] = $document;
		}
```

```php
   echo 'hello,world';
```