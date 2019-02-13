#常用hive sql高级用法及使用场景笔记

##通用维度表记录 

###时间维度
  时间维度是在数据分析报表中常用的一个维度，通常我们需要根据时间维度进行按周、月、年等进行上卷或下钻，所以生成基础时间维度表基本上对任何项目都是非常必要的。
  
1. 建表
```
create table if not exists dim_calendar
(
  this_date         String COMMENT '存2017-10-11格式时间',
  this_date_2       String COMMENT '存如20171011格式时间',
  this_year         String COMMENT '年',
  quarter_of_year   String COMMENT '季度',
  month_of_year     String COMMENT '月',
  year_month        String COMMENT '年-月',
  year_month_2      String COMMENT '年月',
  week_of_year      String COMMENT '周',
  year_week         String COMMENT '年-周',
  day_of_week       String COMMENT '当周第几天',
  is_weekend        String COMMENT '是否是周末',
  is_holiday        String COMMENT  '是否是节假日',
  fiscal_year       String COMMENT  '属于财年'
)
COMMENT '时间维度'
PARTITIONED BY (calendar_year String)
ROW FORMAT SERDE 'org.apache.hadoop.hive.ql.io.orc.OrcSerde'
STORED AS INPUTFORMAT 'org.apache.hadoop.hive.ql.io.orc.OrcInputFormat'
OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.orc.OrcOutputFormat'
;
```

2. 数据插入脚本
```
INSERT OVERWRITE TABLE dim_calendar partition(calendar_year='yyyy')
SELECT  this_date as this_date,
        this_date as this_date,
         year(this_date) as canlendar_year,
         quarter(this_date) as quarter_of_year,
         month(this_date) as month_of_year,
         substr(this_date,1,7) as  year_month_1,
         concat(substr(this_date,1,4),substr(this_date,6,2)) as year_month_2,
         weekofyear(this_date) as week_of_year,
         case when weekofyear(this_date)=1 and month(this_date)=12 then concat(year(this_date)+1,'-', weekofyear(this_date))
              else concat(year(this_date),'-', weekofyear(this_date))
         end as year_week,
         date_format(this_date, 'u') as day_of_week,
         case when date_format(this_date, 'u') in (6,7) then 1
              else 0
         end as is_weekend,
         case when this_date in #holidays then 1
              else 0
         end as is_holiday
FROM
(
    SELECT explode(split("#days",',')) as this_date
    FROM dtip.fake
) t;
```

3. shell脚本生成数据： 一次性生成20年的基本也够用了。
```
   #!/bin/bash
   holidays="('1970-01-01')"
   working_weekend="('')"
   days="2017-01-01"
   curentDay="2017-01-01"
   lastDay="2017-12-31"
   year="2017"
   i=1
   while (( $i<365 ))
   do
       nextDay="${curentDay} +1 days"
       #date -d"2018-01-01 +365 days" +"%Y-%m-%d"
       cmd='date -d"'${nextDay}'" +"%Y-%m-%d"'
       #echo $cmd
       curentDay=`eval $cmd`
       days=$days,$curentDay
       if [ $curentDay = $lastDay ]
           then break
       fi
       let i++
       #echo days
   done
   days="$days"
   echo "$days"
   cat dim_calendar.sql | sed "s/#days/${days}/g" | sed "s/#holidays/${holidays}/g" | sed "s/yyyy/${year}/g"> tmp_dim_calendar.sql
   #hive -hiveconf thisday=20170617 -hiveconf lastday=20170618 -f tmp.sql
   hive -f tmp_dim_calendar.sql
``` 
###地点维度 TODO

###Hive Sql
###分组统计
一般情况下，分组统计功能通常需要携带key之外其他字段信息，一般情况下用group by之后，还得根据key join 其他字段，才能在最终报表中获取更多的信息。
此时可以尝试使用hive 窗口函数 partition by功能。
```
INSERT OVERWRITE TABLE fact_candidate_stage  PARTITION(create_date='yyyymmdd')
SELECT candidate_id, apply_id, jd_id, stage,
        first_create_time as start_ts,
        last_update_time as end_ts,
        --(unix_timestamp(last_update_time)-unix_timestamp(first_create_time)) as duration
        abs(datediff(last_update_time, first_create_time)) as duration
FROM (
    SELECT id, apply_id, jd_id, stage, candidate_id,
     first_value(id) over (partition by apply_id,stage order by id ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING)  first_id,
     first_value(create_time) over (partition by apply_id,stage order by id ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING)  first_create_time,
     last_value(id) over (partition by apply_id,stage order by id ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) last_id,
     last_value(update_time) over (partition by apply_id,stage order by id ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) last_update_time,
     row_number() over (partition by apply_id,stage order by id ) as rn
    FROM ehr_data.candidate_stage_trace
    WHERE create_date='yyyymmdd') a
WHERE a.rn=1
```
注意此时外层的where rn=1, 只获取第一行的结果，否则会有很多的重复结果。


##注意事项
* hive 支持full join, 但实际过程中需要full join还是比较少的，用的时候慎重思考，full join将会导致join的t1.key 和 t2.key这两个字段均有可能为NULL，
结果集多出另外的不匹配记录
* hive 的left join， 用 ON 时，尽量只将join key 放入ON 中。其他过来条件放入where中。
left join ON 中右边过滤，为先筛选右边，然后join，不是结果集最终过滤。放入后面的where条件的过滤是对最终结果的过滤。

###字符串处理
###时间处理
###一行变成多列
###多列合并成一列