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
