## 保存为csv、parquet
### 1、保存为csv文件
保存
```
df.coalesce(1).write.option("header", "true").csv("xxx.csv")
```
加载
```
df=spark.read.format("com.databricks.spark.csv").option("header","true").load('xxx.csv')
```
### 2、保存为parquet文件
```
# 保存
df.write.parquet("output_file_path.parquet")

# 加载
df = spark.read.parquet("input_file_path.parquet")
```
如果指定的输出文件存在默认会报错，也可以指定为其他模式，支持的模式在org.apache.spark.sql.SaveMode下，分为以下模式：
```
Append：追加模式。要注意追加表和原表的结构和数据类型。不匹配会报错，尤其是数据类型，甚至可能造成文件破坏。
ErrorIfExists：存在则报错，默认。
Ignore：若存在什么都不做，也不报错。
Overwrite：若存在则覆盖。
```
```
df.write.mode(SaveMode.Append).parquet("output_file_path.parquet")
```

&nbsp;
## 保存为hive表
查看spark加载的永久表：
```
spark.catalog.listTables.show
```

可以直接用spark的table方法加载已经保存的永久表：
```
val df = spark.table("ymn")
```
保存为永久表：
```
df.write.saveAsTable("table_name")
```
如果没有配置hive支持，此表的数据默认是存储在当前目录下的spark-warehouse文件夹下，spark-warehouse文件夹下的每个子文件夹是一张表，子文件夹名为表名，里面有parquet文件是表的数据文件。

同时会在当前目录生成一个metastore_db文件夹和一个derby.log日志文件，metastore_db文件夹就是一个数据库名为metastore_db的derby（一个纯java的apache的开源数据库管理系统）数据库，里面存放着spark永久表的元数据信息。

其实就跟hive一样：derby数据库存放元数据信息，spark-warehouse存放数据。

只不过这里的数据不是hive默认的文本文件，而是指定了SerDe（序列化反序列化）的parquet文件。


&nbsp;
## References
[Spark：DataFrame保存为parquet文件和永久表](https://blog.csdn.net/xuejianbest/article/details/85775442)
