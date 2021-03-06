---
layout: post
title: "Merge Into"
categories: database
---

####用途 ####
merge 命令可以用来用一个表中的数据来修改或者插入到另一个表。插入或者修改的操作取决于on子句的条件。 

该语句可以在同一语句中执行两步操作，可以减少执行多条insert 和update语句。 

merge是一个确定性的语句，即不会在同一条merge语句中去对同一条记录多次做修改操作。 


    
关键字、参数 

###into 子句 ###

在into子句中指定所要修改或者插入数据的目标表 

###using 子句 ###
在using子句中指定用来修改或者插入的数据源。数据源可以是表、视图或者一个子查询语句。 

###on 子句 ###
在on子句中指定执行插入或者修改的满足条件。在目标表中符合条件的每一行，oracle用数据源中的相应数据修改这些行。对于不满足条件的那些行，oracle则插入数据源中相应数据。 

when matched | not matched 
用该子句通知oracle如何对满足或不满足条件的结果做出相应的操作。可以使用以下的两类子句。 

###merge_update子句 ###
merge_update子句执行对目标表中的字段值修改。当在符合on子句条件的情况下执行。如果修改子句执行，则目标表上的修改触发器将被触发。 

限制：当修改一个视图时，不能指定一个default值 

###merge_insert 子句###
merge_insert子句执行当不符合on子句条件时，往目标表中插入数据。如果插入子句执行，则目标表上插入触发器将被触发。 

限制：当修改一个视图时，不能指定一个default值 

范例 
merge 范例 
下面的例子在oe模式下创建一个bonuses表，bonus的缺省值为100。然后插入bonuses表所有有成绩的销售人员（基于oe_orders 表的sqles_rep_id字段）。最后，人力资源管理人员决定，所有的人员将得到分红。没有销售成绩的员工得到工资的1%的分红，而那些有销售成绩的员工将得到缺省分红以及工资的1%。可以用一条merge语句将完成上述修改： 
```sql
create table bonuses (employee_id number, bonus number default 100); 

insert into bonuses(employee_id) 

(select e.employee_id from employees e, orders o 

where e.employee_id = o.sales_rep_id 

group by e.employee_id); 


select * from bonuses; 
```

employee_id bonus 

----------- ---------- 

153 100 

154 100 

155 100 

156 100 

158 100 

159 100 

160 100 

161 100 

163 100 

```sql
merge into bonuses d 

using (select employee_id, salary, department_id from employees 

where department_id = 80) s 

on (d.employee_id = s.employee_id) 

when matched then update set d.bonus = d.bonus + s.salary*.01 

when not matched then insert (d.employee_id, d.bonus) 

values (s.employee_id, s.salary*0.01); 
```

employee_id bonus 

----------- ---------- 

153 180 

154 175 

155 170 

156 200 

158 190 

159 180 

160 175 

161 170 

163 195 

157 95 

145 14 

170 96 

179 62 

152 90 

169 100
 
在ibatis应用中的一个例子:
```sql
<update id="mergeAdoptDept" parameterClass="adoptDept">
    MERGE INTO ADMIN_J230_DEPT DEPT
   USING (    
      SELECT #deptCd# AS DEPT_CD , #deptNm# AS DEPT_NM , #visibleYn# AS VISIBLE_YN
      FROM DUAL
   )DT
   ON ( DEPT.DEPT_CD = DT.DEPT_CD )
   WHEN MATCHED THEN 
        UPDATE SET DEPT.DEPT_NM = DT.DEPT_NM,  DEPT.VISIBLE_YN = DT.VISIBLE_YN
   WHEN NOT MATCHED THEN      
       INSERT ( DEPT_CD, DEPT_NM, VISIBLE_YN )
       VALUES ( DT.DEPT_CD, DT.DEPT_NM, DT.VISIBLE_YN )
  </update>
```
