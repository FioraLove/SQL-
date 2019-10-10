### ```基于Oracle的存储过程```<br>
[PL/SQL存储过程一](https://blog.csdn.net/zezezuiaiya/article/details/79557621)  [PL/SQL存储过程二](https://www.cnblogs.com/enjoyjava/p/9131169.html)<br>
<img src="http://pic1.win4000.com/wallpaper/2019-01-11/5c382f33cf13e.jpg" height=70% width=70%/><br>
### SQL三部分：声明部分 可执行部分 异常处理部分

       declare 
          -- Local variables here
          i integer;
       begin
          -- Test statements here
            dbms_output.put_line('hello world hello python'); --打印hello world
       end; 
>总结：不区分大小写；以begin与end为核心体;每条语句以";"结尾

>知识点：

    1. || 表示拼接符号
    2. 声明变量的方式为：变量名 变量类型(长度) eg: name varchar2(length);
    3. 变量赋值方式：
        3.1 直接赋值语句  :=(这是赋值符号，区别于其他语言)  eg：name := 'chd';
        3.2 语句赋值 使用 select 值 into 变量 from dual；
## 2.变量类型：

    2.1普通变量类型（前提你要了解数据库字段的类型和大小）
        2.1.1 声明变量的方式为：变量名 变量类型(长度) eg: name varchar2(length);
        2.1.2 变量赋值方式：
                3.1 直接赋值语句  :=(这是赋值符号，区别于其他语言)  eg：name := 'chd';
                3.2 语句赋值 使用 select 值 into 变量 from dual；
        普通变量类型的SQL查询
            -- Created on 2019/10/9 by 1900201 
            declare
              v_name number;
              age    number;
            begin
              -- Test statements here
            
              select mny, rate into v_name, age from TW_DATAS where ly_rate = 12;  
			  --核心为：select 数据库字段 into 自定义的字段 form 表名 where 条件
            
              dbms_output.put_line('hello world hello python' || ' hello java' || 'MNY： ' ||' '||
                   v_name || 'RATE： ' || age); --打印hello world
            end;
            输出结果：hello world hello python hello javaMNY：  1415436RATE： 11.76
         

    2.2引用型变量： 变量的类型和长度决定表中字段的类型和长度：
        通过 表名.列名%TYPE 来指定变量的类型和长度  eg:v_name 表名.列名%TYPE (推荐使用，不必了解数据库架构) 
        引用性变量的SQL查询：
            declare
                v_name TW_DATAS.mny%TYPE;
                age tw_datas.rate%TYPE;

            begin
              -- Test statements here
            
              select mny, rate into v_name, age from TW_DATAS where ly_rate = 12;
            
              dbms_output.put_line('hello world hello python' || ' hello java' || 'MNY： ' ||' '||
                                   v_name || 'RATE： ' || age); --打印hello world
            end;
            输出结果：hello world hello python hello javaMNY：  1415436RATE： 11.76

    2.3 记录型变量：接受表中的一整行数据，类似于Java中的一个对象(慎用，比较废数据库内存)
        语法：  变量名称 表名%ROWTYPE； 获取一整行数据，通过"."来调用具体列，且必须写"*"号
        declare
            v_emp TW_DATAS%ROWTYPE;
        begin
            -- Test statements here
        
            select * into v_emp from TW_DATAS where ly_rate = 12;
        
            dbms_output.put_line('hello world hello python' || ' hello java' ||
                           '  MNY： ' || ' ' || v_emp.mny || '  RATE： ' ||
                           v_emp.rate); --打印hello world
        end;

## 3.流程控制
### 判断：if-elsif-...-else-end if
    BEGIN 
        IF 条件 THEN 执行一;
        ELSIF  条件二 THEN 执行二;
        ELSE 执行否则;
        END IF ;
    END
    eg:
    declare
      v_emp   TW_DATAS%ROWTYPE;
      v_count number;
    begin
           select count(distinct item_name) into v_count from Tw_Datas;
          dbms_output.put_line(v_count);
          if v_count >= 300 then
            dbms_output.put_line(v_count + 1);
          elsif 200 < v_count then
            dbms_output.put_line(v_count - 100);
          else
            dbms_output.put_line(v_count);
          
          end if;
    end;
### 循环LOOP
       sum1 number :=1;
       LOOP
         exit when  sum1 > 10;
         dbms_output.put_line(sum1);
         sum1 :=sum1+1;
       
       end loop;
## 4.游标：用于临时存储一个查询返回的多行数据
> 游标使用方式：声明--打开游标--读取--关闭

1.游标声明：cursor 游标名[(参数列表)] is 查询语句;
>1.1无参传递

    --声明游标
    cursor cemp is select mny,rate from tw_datas;
    -- 声明变量来接受游标中的元素
    mny1 = tw_datas.mny%TYPE;
    rate1 = tw_datas.rate%TYPE;
    
    BRGIN 
        --打开游标
        open cemp;
        --遍历游标中的值
        loop
        --通过fetch语句获取游标中的值并赋值给变量
        fetch cemp into mny1,rate1;
        --通过%NOTFOUND判断是否有值，有值打印，没有则退出循环
        eixt when cemp%notfound;
        dbms_output.put_line('  MNY： ' || ' ' || mny1 || '  RATE： ' ||rate1); --打印values
        
        end loop；
        closecemp；
    END
   
>1.2 带参传递

    --声明游标
    cursor cemp (v_canshu tw_datas.item_name%TYPE) is select mny,rate from tw_datas where item_name=v_canshu;   --不同点一
    -- 声明变量来接受游标中的元素
    mny1 = tw_datas.mny%TYPE;
    rate1 = tw_datas.rate%TYPE;
    
    BRGIN 
        --打开游标
        open cemp('存货');  --不同点二：打开游标，并传递参数
        --遍历游标中的值
        loop
        --通过fetch语句获取游标中的值并赋值给变量
        fetch cemp into mny1,rate1;
        --通过%NOTFOUND判断是否有值，有值打印，没有则退出循环
        eixt when cemp%notfound;
        dbms_output.put_line('  MNY： ' || ' ' || mny1 || '  RATE： ' ||rate1); --打印values
        
        end loop；
        closecemp；
    END
    
## 5.存储过程
    语法：
    create or replace procedure 过程名称[(参数列表)] is(as,两者互用，功能一样)
    begin
        ...
    end 过程名称;
    创建的过程：
        1.点击PL/SQL的文件-->新建--> 程序窗口--> Procedure--> ...--> 
            编写完进行编译
        2.调用Procedure文件：新的函数窗口下
            begin
                直接调用已编译好的Procedure文件
            end 
            
>带参数的传递：-- 带参数查询：比如在tw_datas表中查询某个item_name对应的mny，rate的存储过程

    create or replace procedure p_firstTest(varname1 in tw_datas.begin_rate%type) is --注意传入参数写in
    mny1 tw_datas.mny%type;
    rate1 tw_datas.rate%type;
    begin
      select mny,rate into mny1,rate1 from tw_datas where begin_rate=varname1;--注意参数传递
      
      dbms_output.put_line('hello world'||' '||mny1||'  '||rate1);
    end p_firstTest;
>带入参及返回值的存储过程:传入参数并带有一个返回值的存储过程

	--输入员工号查询某个员工(7839)信息，要求讲薪水作为返回值输出，给调用的程序使用
	create or replace procedure p_empInfo2(i_empno IN emp.empno%TYPE, o_sal out emp.Sal%TYPE) as

	begin
	  select sal into o_sal from emp where empno = i_empno;

	end p_empInfo2;
>带入参数以及返回值的调用

	在测试窗口中调用该存储过程，需要事先定义一个变量作为 存储过程返回值的接收参数，在打印语句中打印该参数的值，执行该语句， 输出结果5000.00 
	-- Created on 2018/6/3 by ADMINISTRATOR 
	declare 

	  v_sal emp.sal%TYPE;
	begin
	  p_empInfo2(7839,v_sal);
	  dbms_output.put_line(v_sal);

	end;



### SQL最常用的数据类型

	数据类型				描述
	integer(size)			仅容纳整数。在括号内规定数字的最大位数。
	int(size)				
	smallint(size)			
	tinyint(size)			
	
	decimal(size,d)			容纳带有小数的数字。"size" 规定数字的最大位数。"d" 规定小数点右侧的最大位数。
	numeric(size,d)
	
	char(size)				在括号中规定字符串的长度，容纳固定长度的字符串（可容纳字母、数字以及特殊字符）。
	varchar(size)			容纳可变长度的字符串（可容纳字母、数字以及特殊的字符）。在括号中规定字符串的最大长度。

	date(yyyymmdd)			容纳日期。
	
### SQL的几种约束：

	NOT NULL
	UNIQUE
	PRIMARY KEY
	FOREIGN KEY
	CHECK
	DEFAULT


### 1.SQL

      SQL 是用于访问和处理数据库的标准的计算机语言
      SQL 能做什么？
      SQL 面向数据库执行查询
      SQL 可从数据库取回数据
      SQL 可在数据库中插入新的记录
      SQL 可更新数据库中的数据
      SQL 可从数据库删除记录
      SQL 可创建新数据库
      SQL 可在数据库中创建新表
      SQL 可在数据库中创建存储过程
      SQL 可在数据库中创建视图
      SQL 可以设置表、存储过程和视图的权限

### 2.要创建发布数据库中数据的网站，您需要以下要素：

    RDBMS 数据库程序（比如 MS Access, SQL Server, MySQL）
    服务器端脚本语言（比如 PHP 或 ASP）
    SQL
    HTML / CSS\
    
### 3.SQL的左连接，右连接，内连接
    CREATE TABLE table_a
    (
      PK    INT(10)     NOT NULL
        PRIMARY KEY,
      Value VARCHAR(25) NULL
    )

    表B：

    -- auto-generated definition
    CREATE TABLE table_b
    (
      PK    INT(10)     NOT NULL
        PRIMARY KEY,
      Value VARCHAR(50) NULL
    )

#### 3.1内连接inner join
    内连接是一种一一映射关系，就是两张表都有的才能显示出来 
    用韦恩图表示是两个集合的交集

    SELECT  A.PK AS A_PK,A.Value AS A_Value,B.PK AS B_PK,B.Value AS B_Value
    FROM table_a A
    INNER JOIN table_b B
    ON A.PK = B.PK;
    
#### 3.2 left join左连接
    左连接是左边表的所有数据都有显示出来，右边的表数据只显示共同有的那部分，没有对应的部分只能补空显示，
    所谓的左边表其实就是指放在left join的左边的表 
    SELECT  A.PK AS A_PK,A.Value AS A_Value,B.PK AS B_PK,B.Value AS B_Value
    FROM table_a A
    LEFT JOIN  table_b B
    ON A.PK = B.PK
    
#### 3.3 right join 右连接
    右连接正好是和左连接相反的，这里的右边也是相对right join来说的，在这个右边的表就是右表 
    SELECT  A.PK AS A_PK,A.Value AS A_Value,B.PK AS B_PK,B.Value AS B_Value
    FROM table_a A
    RIGHT JOIN   table_b B
    ON A.PK = B.PK;
    
### 4.SQL的limit用法（LeetCode 176）
    select * from limit tableName limit i,n
    参数：
    tableName : 为数据表；
    i : 为查询结果的索引值（默认从0开始）；
    n : 为查询结果返回的数量
    
    >>>example:查询第二高的工资
    >>>select distinct salary from Employee order by salary desc limit 1,1
    ps：此方法虽然能够查询第二高的工资，但是无法查询null集合
    >>>改进：select (select distinct salary from Employee order by salary desc limit 1,1) as SecondHighestSalary ;
    
### 5.SQL的limit用法拓展（LeetCode 177 第n高的薪水）
    将 Salary 去重并从低到高排序，要求返回第 n 高，用 limit 语法返回 limit n-1,1 即可，可能存在 null，用临时表查询即可

    CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
    BEGIN
      DECLARE P INT;
      SET P = N-1;
      RETURN (
          select(
            SELECT DISTINCT Salary FROM Employee ORDER BY Salary DESC LIMIT P,1
          )
      );
    END

### 6、分数排名问题（LeetCode 178：相同分数的人的名次相同）
    SELECT S1.Score,COUNT(DISTINCT S2.Score)+1 AS Rank
    FROM Scores S1 LEFT JOIN Scores S2
    ON S1.Score<S2.Score
    GROUP BY S1.Id
    ORDER BY Rank;
    
### 7、超过经理收入的名单（LeetCode 181）
    select 
    a.Name as Employee 
    from Employee a join Employee b  on a.ManagerId = b.Id and a.Salary > b.Salary
    
### 8.从不订购的客户（LeetCode 183）
    select A.Name as Customers
    from Customers A left join Orders B
    on A.Id = B.CustomerId
    where B.Id is null
    某网站包含两个表，Customers 表和 Orders 表。编写一个 SQL 查询，找出所有从不订购任何东西的客户。

    Customers 表：

    +----+-------+    
    | Id | Name  |
    +----+-------+
    | 1  | Joe   |
    | 2  | Henry |
    | 3  | Sam   |
    | 4  | Max   |
    +----+-------+
    Orders 表：

    +----+------------+
    | Id | CustomerId |
    +----+------------+
    | 1  | 3          |
    | 2  | 1          |
    +----+------------+
    例如给定上述表格，你的查询应返回：

    +-----------+
    | Customers |
    +-----------+
    | Henry     |
    | Max       |
    +-----------+
### 9.部门工资最高的员工（LeetCode 184）
    Employee 表包含所有员工信息，每个员工有其对应的 Id, salary 和 department Id。

    +----+-------+--------+--------------+
    | Id | Name  | Salary | DepartmentId |
    +----+-------+--------+--------------+
    | 1  | Joe   | 70000  | 1            |
    | 2  | Henry | 80000  | 2            |
    | 3  | Sam   | 60000  | 2            |
    | 4  | Max   | 90000  | 1            |
    +----+-------+--------+--------------+
    Department 表包含公司所有部门的信息。

    +----+----------+
    | Id | Name     |
    +----+----------+
    | 1  | IT       |
    | 2  | Sales    |
    +----+----------+
    编写一个 SQL 查询，找出每个部门工资最高的员工。例如，根据上述给定的表格，Max 在 IT 部门有最高工资，Henry 在 Sales 部门有最高工资。

    +------------+----------+--------+
    | Department | Employee | Salary |
    +------------+----------+--------+
    | IT         | Max      | 90000  |
    | Sales      | Henry    | 80000  |
    +------------+----------+--------+
    
    SELECT
	Department.NAME AS Department,
	Employee.NAME AS Employee,
    Salary 
    FROM
        Employee,
        Department 
    WHERE
        Employee.DepartmentId = Department.Id 
        AND ( Employee.DepartmentId, Salary ) 
        IN (SELECT DepartmentId, max( Salary ) 
            FROM Employee 
            GROUP BY DepartmentId )
