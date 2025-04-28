本周阅读Database-System-Concepts-7th-Edition:PART TWO DATABASE DESIGN,知识点总结如下：  
Chapter 7 Relational Database Design(p303-360)  
**7.1.1 Decomposition（分解）**  
分解目的：避免模式（如 in_dep 模式 ）中信息重复问题，可将其分解为更小模式（如 instructor 和 department 模式）。但并非所有分解都有益，如将模式分解到仅含一个属性，会无法表达有趣关系。  
示例分析：以 employee 模式（employee (ID, name, street, city, salary) ）为例，将其分解为 employee1 (ID, name) 和 employee2 (name, street, city, salary) 。若企业存在同名员工（如两个叫 Kim 的员工 ），这种分解会在自然连接时产生错误混合数据的新元组，导致无法区分某些数据对应哪个员工，虽元组增多但实际信息减少，此为有损分解（lossy decomposition） 。  
7.1.2 Lossless Decomposition（无损分解）定义：设 R 为关系模式，$\(R_1\) $和 \(R_2\) 构成 R 的分解（即$R = R_1 \cup R_2$ ）。若用 \(R_1\) 和 \(R_2\) 替换 R 时无信息丢失，则该分解为无损分解。具体而言，对于所有合法数据库实例，关系 r 与通过 “select * from (select \(R_1\) from r) natural join (select \(R_2\) from r)” 查询结果包含相同元组集，用关系代数表示为 \(\Pi_{R_1}(r) \bowtie \Pi_{R_2}(r) = r\)。有损分解判断：若计算投影结果的自然连接得到的是原始关系的真超集，则为有损分解，用关系代数表示为 \(r \subset \Pi_{R_1}(r) \bowtie \Pi_{R_2}(r)\)。例如前面 employee 模式分解为 employee1 和 employee2 的例子，连接结果是原始关系超集且丢失员工标识符对应地址和薪资信息，属于有损分解。
