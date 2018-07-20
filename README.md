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
      combineByKey Method:
      ```python
      rdd.combineByKey(createCombiner, mergeValue, mergeCombiners) 
      ```
       * combineByKey method requires three functions:

          * createCombiner
          ```python
          lambda value: (value, 1)
          ```
          The first required argument in the combineByKey method is a function to be used as the very first aggregation step for each key. The argument of this function corresponds to the value in a key-value pair. If we want to compute the sum and count using combineByKey, then we can create this "combiner" to be a tuple in the form of (sum, count). Note that (sum, count) is the combine data structure C (in tha API). The very first step in this aggregation is then (value, 1), where value is the first RDD value that combineByKey comes across and 1 initializes the count.
          * mergeValue
          ```python
          lambda x, value: (x[0] + value, x[1] + 1)
          ```
          The next required function tells combineByKey what to do when a combiner is given a new value. The arguments to this function are a combiner and a new value. The structure of the combiner is defined above as a tuple in the form of (sum, count) so we merge the new value by adding it to the first element of the tuple while incrementing 1 to the second element of the tuple.
          
          * mergeCombiner
          ```python
          lambda x, y: (x[0] + y[0], x[1] + y[1])
          ```
          The final required function tells combineByKey how to merge two combiners. In this example with tuples as combiners in the form of (sum, count), all we need to do is add the first and last elements together.
     * Group by
       * groupby()
       * cogroup() # mulltiable RDD
     * Join
       * leftOutJoin()
       * rightOutJoin()
       * join()[innter join]
     * Sort
       ```python
       rdd.sortByKey(ascending=True, numPartitions=None, keyfunc = lambda x: str(x))
       ```
      * use partitionBy() to reduce network communication
