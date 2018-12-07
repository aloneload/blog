---
title: oracle笔记
date: 2018-07-19 10:51:27
tags: 笔记
categories: 笔记
---
## 实用sql
```sql
/* --输出表中行 */

declare
cl_loan_row cl_loan%rowtype;
    begin
   select * into cl_loan_row from cl_loan where loan_key= '31'
                                        and fdate =
                                        '20121103';
dbms_output.put_line(cl_loan_row.loan_no);
end;
/
/* ----复制表的结构 */
create table cl_loan_bak
        as select * from cl_loan
        where rownum < 1
select replace(fdate,'20121103','20121102') from cl_loan where fdate = '20121103';
/*四舍五入 */
select round(23.45434,2),round(23.355,2) from dual;
集合运算
intersect
union all
union
minus
--动态sql的语法
execute immediate sqlstr
[into 变量列表】
【using 参数列表]
建表
str_sql := 'create table '||table_name||'('||c1||' '||datatype||')';
execute immediate str_sql;
str_sql := 'insert into table_name valus(:1,:2)';
execute immediate str_sql using (1,'hello');
查找列存在重复值得记录

SELECT   *
  FROM   TP_IND_CR_01_01_MID
 WHERE   col_0 IN (  SELECT   col_0
                       FROM   TP_IND_CR_01_01_MID
                   GROUP BY   col_0
                     HAVING   COUNT ( * ) >= 2)
--/ 告诉数据库可以执行语句（脚本文件中不要用；代替/)
--truncate table table_name
```
## 游标
```sql
/* Formatted on 2015-11-8 19:21:12 (QP5 v5.114.809.3010) */
declare
c_no stu.sno%type;
c_name stu.stu.name%type;
cursor cur_stu
is
select * from stu;
begin
open cur_stu;
loop
    fetch cur_stu into c_no,c_name;
    exit when cur_stu%notfound;
    c_sum := cur_stu%rowcount;
    dbms_output.put_line('学生的学号'||c_no||'姓名'||c_name);
    end loop;
     close cur_stu;
end;
/
--for
declare
stu_row stu%rowtype;
cursor cur_stu
is
select * from stu;
begin
for stu_row in cur_stu
loop
    dbms_output.put_line('学生的学号'||stu_row.c_no||'姓名'||stu_row.c_name);
    end loop;
end;
/
```
## 触发器的使用
```sql
create or replace trigger tri_yj
before[after][instead] insert or update or delete on stu
begain
if updating or inserting or daleting then
dbms_output_line('触发语句触发器');
end if;
end;
create or replace trigger tri_yj
before[after][instead] insert or update or delete on stu
for each row
begain
if updating or inserting or daleting then
dbms_output_line('行触发器');
end if;
end;
```
## 自定义异常
```sql
declare 
myexp exception;
begin
if sum=0 then
raise myexp;
end if;
exception when no_data_found then
DBMS_OUTPUT.PUT_LINE('无数据');
when myexp then 
DBMS_OUTPUT.PUT_LINE('自定义异常');
when others then
end;
/
```