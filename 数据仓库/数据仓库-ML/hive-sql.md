#常用hive sql及错误汇总
##

##注意事项
* hive 支持full join, 但实际过程中需要full join还是比较少的，用的时候慎重思考，full join将会导致join的t1.key 和 t2.key这两个字段均有可能为NULL，
结果集多出另外的不匹配记录
* hive 的left join， 用 ON 时，尽量只将join key 放入ON 中。其他过来条件放入where中。
left join ON 中右边过滤，为先筛选右边，然后join，不是结果集最终过滤。放入后面的where条件的过滤是对最终结果的过滤。