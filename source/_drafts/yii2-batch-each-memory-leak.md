title: "redis 命令纪实"
date: 2015-07-31 22:44:42
tags: [Yii2]
category: [Yii2]
---

```
Yii::$app->db->open();
\Yii::$app->db->pdo->setAttribute(\PDO::MYSQL_ATTR_USE_BUFFERED_QUERY, false);
$query = (new Query)->from('hot_course_majors');

foreach ($query->each(10) as $item) {
    echo $item['name'], PHP_EOL;
}
```