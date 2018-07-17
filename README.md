# Apache-Spark-on-Google-Colaboratory
## Components for distributed execution in Spark

![Components for distributed execution in Spark](https://spark.apache.org/docs/latest/img/cluster-overview.png)

* Two types of RDD operations
  * Transformations: retrun a new RDD
    * element-wise
    
      filter(), map()，flatmap(),distinct(), union(),intersection(), substract(),cartsian()
  * Actions: return others
    * spark is *lazy Evaluation*, which means it is only executed until it sees an action.
    
       count(),countByValue(),first(), take(n),top(),saveAsTextFile(),aggregation(),fold(),collect(),reduce(),takeSample(),foreach()

## Persistance
  Avoid computing an RDD mutiple times. With 5 different storage level(they have different space use,cpu time):
   * MEMORY_ONLY
   * MEMORY_ONLY_SER
   * MEMORY_AND_DISK
   * MEMORY_AND_DISK_SER
   * DISK_ONLY

## Pair RDD
  * Use map() to transfer regular RDD to pair RDD
  ```python
  pairs = lines.map(lambda x: (x.split(” “)[0], x))
  ```
  * Transformations on tuples
    * reduceByKey(), groupByKey(),combineByKey(),mapValues()[apply fun to values without changing keys],flatMapValues(),keys(),values(),sortByKey(),substractByKey(),join()[perform inner join to tuples],rightOuterJoin,leftOuterJoin(),cogroup()
  
    * Aggregations
      ```python
      # word account
      rdd = sc.textFile(“s3://…”)
      words = rdd.flatMap(lambda x: x.split(” “))
      result = words.map(lambda x: (x, 1)).reduceByKey(lambda x, y: x + y)
      # equal to 
      result = words.map(lambda x: (x, 1)).countByValue() 
      ```
