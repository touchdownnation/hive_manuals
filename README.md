# hive_manuals


 ## Hive DATA PROCESSING
 
  - 1.SOURCING       RAW           TEXT,CSV   "aggregate,Cleansing"
  
  - 2.LANDING        STG           TEXT,CSV   >> "Convert to parq,orc"
  
  - 3.DEFINED        Warehouse     Parq,ORC    >> Completed
   
   
   ## 1 - Unload data to HDFS home path via Hue
   
   - HUE Web interface
   - HDFS cmd from local / NFS
 
    hdfs dfs -ls /user/admin/demo/emp/
    ---rw-r--r--   3 admin admin      39169 2021-01-17 17:42 /user/admin/demo/emp/employee_metadata.csv

   ## 2 - Create databases;

    CREATE DATABASE demo LOCATION '/data/warehouse/demo.db';
    
   ## 3 - Create tables;

    CREATE TABLE `demo.employee_metadata`(`factory_id` string,
                                    `employee_id` string,
                                    `gender` string,
                                    `email` string,
                                    `occupation` string,
                                    `birthdate` string,
                                    `salary` DOUBLE) 
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe' 
    WITH SERDEPROPERTIES ('colelction.delim'='','field.delim'=',','mapkey.delim'='','serialization.format'=',') 
    STORED AS INPUTFORMAT 'org.apache.hadoop.mapred.TextInputFormat' 
    OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat' 
    LOCATION 'hdfs://cdh-master-01.poc.yits.com:8020/data/warehouse/demo.db/employee_metadata' 
    TBLPROPERTIES ('skip.header.line.count'='1');

   ## 4 - Load data ;
   
    LOAD DATA INPATH '/user/admin/demo/emp/*' OVERWRITE INTO TABLE demo.employee_metadata;
    LOAD DATA INPATH '/user/admin/demo/emp/*' INTO TABLE demo.employee_metadata;

   ## 5 - Checking data
   
    SELECT * FROM demo.employee_metadata where factory_id = 9 LIMIT 100;
    SELECT COUNT(*) from demo.employee_metadata;

   ## 6  - create  ORC table :: Defined zone
   
    CREATE TABLE `demo.employee_metadata_orc1`(`factory_id` string,
                                    `employee_id` string,
                                    `gender` string,
                                    `email` string,
                                    `occupation` string,
                                    `birthdate` string,
                                    `salary` DOUBLE) 
    STORED AS ORC
    LOCATION 'hdfs://cdh-master-01.poc.yits.com:8020/data/warehouse/demo.db/employee_metadata_orc1';
    
   
   ## 7 - Convert to ORC. (TEXT TO ORC)
   
   
    INSERT OVERWRITE TABLE demo.employee_metadata_orc1 SELECT * FROM demo.employee_metadata;
