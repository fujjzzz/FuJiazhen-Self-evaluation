**3.23**  
阅读**Database-System-Concepts-7th-Edition**:PART ONE-RELATIONAL LANGUAGES,知识点总结如下：  
**关系数据模型**：以表集合为基础，用户可对表进行查询、插入、删除和更新元组操作。   
**关系模式与实例**：关系模式是逻辑设计，包含属性、属性类型及约束（如主码和外码键约束）；关系实例是某时刻关系中的内容。数据库模式和实例的定义与之类似。例如定义一个教师（instructor）关系模式：instructor（ID，name，dept_name，salary），其中 ID、name 等是属性。  
**码的相关概念**：  
**1.超码**：一个或多个属性的集合，其值能唯一标识关系中的元组。  
**2.候选码**：最小超码，即构成超键的属性集合中，任意子集都不是超码。  
**3.主码**：从候选码中选择的用于唯一标识元组的码。  
**外码约束**：设关系\(r_1\)的属性A到关系\(r_2\)的主码B存在外码约束，那么\(r_1\)中每个元组的A值，必须是\(r_2\)中某个元组的B值。其中，\(r_1\)是引用关系，\(r_2\)是被引用关系。例如，在 instructor 关系中的 dept_name 属性引用 department 关系中的 dept_name 主码 。  
**关系代数**：提供了一组以一个或多个关系为输入，返回一个关系作为输出的操作。这些操作能组合成关系代数表达式来表达查询需求，是 SQL 等实际查询语言的基础，但 SQL 在此基础上增加了实用的语法特性。基本操作：  
**选择（Select，σ）**：如σ instructor.ID = teaches.ID}(instructor \times teaches)\)，根据条件筛选元组 。&sigma
作业题扩展（ai给我出的题目）  
$ \sigma_{instructor.ID = teaches.ID}(instructor \times teaches) $
