# 实战
## SQL优化
### explain
> 一般看explain哪些字段
- select_type: simple——没有union或子查询,primary——最外层查询,subquery——子查询中第一个select，dependent-subquery——子查询中第一个select依赖了外面的查询
- type: (联接类型)
  - system（表只有一行，是const的特例）
  - const（最多只返回一行，针对主键或唯一索引的等值查询）
  - eq_ref（使用了索引的全部组成成分，主键or唯一not null时）
  - ref（满足最左匹配，非主键索引非唯一索引时）
  - range（有限制的索引扫描）
  - index（全索引扫描）
  - all（全表扫描）
- key_len: 
- ref: (索引的那一列被引用)
- rows: 评估要扫描的行（非精确值）
- filtered：符合查询条件的数据百分比（例如rows=1000，filtered=50%，则查询结果预计=1000 * 50%）
- extra: 
  - using filesort：有order by且无法使用索引排序
  - using index：覆盖索引
  - using where：不是扫全表，不是覆盖索引

### group by和distinct区别
- 在语义相同，有索引的情况下：group by和distinct都能使用索引，效率相同。因为group by和distinct近乎等价，distinct可以被看做是特殊的group by。
- 在语义相同，无索引的情况下：distinct效率高于group by。原因是distinct 和 group by都会进行分组操作，但group by在Mysql8.0之前会进行隐式排序，导致触发filesort，sql执行效率低下。但从Mysql8.0开始，Mysql就删除了隐式排序，所以，此时在语义相同，无索引的情况下，group by和distinct的执行效率也是近乎等价的。
- 但group by可对数据进行更为复杂的一些处理，相比于distinct来说，group by的语义明确。且由于distinct关键字会对所有字段生效，在进行复合业务处理时，group by的使用灵活性更高，group by能根据分组情况，对数据进行更为复杂的处理，例如通过having对数据进行过滤，或通过聚合函数对数据进行运算。