# athena-hive-presto-sqlcase
The memo about hive sql


## Case 1: Key names of json are variable value

* JSONサンプル：
```JSON
{
  "class" : "apple",
  "number" : "12",
  "students" : {
    "jack":{
      "gender" : "male",
      "age" : "20"},
      "rose":{
        "gender" : "female",
        "age" : "22"
      }
    }
  }
```

* create table sql

```sql
CREATE EXTERNAL TABLE sample4 (
  class string,
  number string,
  students struct <
            string:struct<gender:string,age:string>,rose:struct<gender:string,age:string>
  >
  )           
ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
LOCATION 's3://yuhu-test-athena/serjson/'
```

* search result  

** search table by common sql  
```sql
SELECT * FROM "sampledb"."sample7"

## result
1	apple	12	{rose={gender=female, age=22}, jack={gender=male, age=20}}

```
** search and extract nested json items as multiple rows

```sql

select class,number,name ,others.gender,others.age
from sample7
cross join unnest (students) as t (name, others);

1	apple	12	rose	female	22
2	apple	12	jack	male	20

```
