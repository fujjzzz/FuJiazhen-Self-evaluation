本周阅读**Database-System-Concepts-7th-Edition**:PART SIX QUERY PROCESSING AND OPTIMIZATION,知识点总结如下：  
**Chapter 16 Query Optimization**(p743-794)  
>日期：6.3

### 查询优化
#### 1. 查询优化简介
查询优化是从众多可能的策略里，为处理给定查询选出最高效查询评估计划的过程，尤其针对复杂查询。我们不指望用户写出能高效处理的查询，而是期望系统构建出使查询评估成本最小化的查询评估计划，这就是查询优化的作用。好策略和差策略在评估时间成本上差异巨大，可能达几个数量级，所以即便查询只执行一次，系统花大量时间选好策略也很值得。

#### 2. 关系代数与查询优化
- **关系代数表达式**：查询可用关系代数表达式表示。比如，要查询 “找出音乐系所有教师的姓名，以及他们所教课程的课程标题”，初始关系代数表达式为：  
$$\Pi_{name,title}(\sigma_{dept\_name = "Music"}(instructor\bowtie(teaches\bowtie\Pi_{course\_id,title}(course))))$$  
- **等价表达式**：若两个关系代数表达式在任意合法数据库实例上，生成相同元组集合（集合型）或相同元组多集合（SQL用多集合），则它们等价。可通过转换表达式得到更高效的等价形式。像上面的初始表达式可转换为：  
$$\Pi_{name,title}((\sigma_{dept\_name = "Music"}(instructor))\bowtie(teaches\bowtie\Pi_{course\_id,title}(course)))$$  
该转换后的表达式先过滤出音乐系的教师元组，减小了中间结果规模。

#### 3. 评估计划
- **定义**：评估计划定义关系代数表达式中每个操作的算法，以及操作间的协调方式。比如连接操作，可选哈希连接、归并连接等算法。  
- **流水线化与物化边**：评估计划里，边可流水线化或物化。流水线化边下，生产者输出直接传给消费者，不写磁盘；物化边则先写磁盘，再由消费者读取。例如连接操作的评估计划中，可用流水线化边配合哈希连接提升效率。

#### 4. 查询优化器的工作
查询优化器要找出计算结果相同、成本最低的查询评估计划，步骤如下：  
1. **生成等价表达式**：用等价规则把给定表达式转成逻辑等价的形式。关系代数表达式的等价规则有：  
    - **合取选择分解**： $\sigma_{\theta_1\land\theta_2}(E)\equiv\sigma_{\theta_1}(\sigma_{\theta_2}(E))$  
    - **选择操作交换律**： $\sigma_{\theta_1}(\sigma_{\theta_2}(E))\equiv\sigma_{\theta_2}(\sigma_{\theta_1}(E))$  
2. **标注生成计划**：用不同方式标注等价表达式，生成备选查询评估计划，包括选操作算法（如连接用哈希/归并连接）、决定边是流水线化还是物化。  
3. **估算并选成本最低计划**：用关系的统计信息（如关系大小、索引深度）估算各计划成本，选估算成本最低的。

#### 5. 不同数据库查看查询评估计划的方式
多数数据库系统支持查看评估计划：  
- **PostgreSQL**：用 `explain <查询语句>` 命令。  
- **Oracle**：用 `explain plan for` 将计划存入 `plan_table`，再用 `select * from table(dbms_xplan.display);` 查看。  
- **DB2**：类似 Oracle，但需用 `db2exfmt` 显示存储的计划。  
- **SQL Server**：提交查询前执行 `set showplan_text on`，提交时会显示评估计划而非执行查询。  
- **MySQL**：用 `explain <查询语句>`（类似 PostgreSQL），但输出难理解；执行 `explain` 后再执行 `show warnings` 可得到更易读格式。

#### 6. 物化视图
物化视图能加速查询处理。16.5节（此处未详述）会研究如何维护物化视图（保持最新），以及如何用它们做查询优化 。  

### 关系代数等价变换规则（Relational Algebra Equivalence Rules）

---

#### 1. 投影操作（Projection）
- **级联投影可简化**：  
  若有一系列投影操作，仅需保留最终投影，中间投影可省略（前提是属性集满足包含关系 ）。公式：  
  $$\Pi_{L_1}(\Pi_{L_2}(...(\Pi_{L_n}(E))...)) \equiv \Pi_{L_1}(E)$$  
  其中 $L_1 \subseteq L_2 \subseteq ... \subseteq L_n$ ，比如连续对更宽泛属性集投影后取子集投影，可直接简化为最终子集投影。  

---

#### 2. 选择与连接/笛卡尔积结合（Selection with Joins/Cartesian Products）
- **选择与笛卡尔积转θ-连接**：  
  选择操作可与笛卡尔积结合转化为θ-连接，这是θ-连接的定义。公式：  
  $$\sigma_\theta (E_1 \times E_2) \equiv E_1 \bowtie_\theta E_2$$  

- **θ-连接上的选择合并**：  
  对θ-连接结果的选择，可合并到连接条件中。公式：  
  $$\sigma_{\theta_1} (E_1 \bowtie_{\theta_2} E_2) \equiv E_1 \bowtie_{\theta_1 \land \theta_2} E_2$$  

---

#### 3. 连接操作的交换律（Commutativity of Joins）
- **θ-连接交换律**：  
  θ-连接满足交换律，自然连接（特殊θ-连接）也适用。公式：  
  $$E_1 \bowtie_\theta E_2 \equiv E_2 \bowtie_\theta E_1$$  
  *注意*：若严格区分属性顺序，等价性不成立（因连接后属性顺序会变），但关系代数中属性需命名引用，一般默认不考虑顺序影响。  

---

#### 4. 连接操作的结合律（Associativity of Joins）
- **自然连接结合律**：  
  自然连接满足结合律，调整连接顺序不影响结果。公式：  
  $$(E_1 \bowtie E_2) \bowtie E_3 \equiv E_1 \bowtie (E_2 \bowtie E_3)$$  

- **θ-连接结合律**：  
  特定条件下θ-连接也可结合（需保证子条件仅涉及对应关系属性 ）。公式：  
  $$(E_1 \bowtie_{\theta_1} E_2) \bowtie_{\theta_2 \land \theta_3} E_3 \equiv E_1 \bowtie_{\theta_1 \land \theta_3} (E_2 \bowtie_{\theta_2} E_3)$$  
  （其中 $\theta_2$ 仅涉及 $E_2$ 和 $E_3$ 的属性 ）  

---

#### 5. 选择对θ-连接的分配律（Selection Distribution over θ-Join）
- **条件仅涉及一侧关系**：  
  若选择条件 $\theta_1$ 仅涉及连接中某一侧关系（如 $E_1$ ），选择可下推到该关系。公式：  
  $$\sigma_{\theta_1} (E_1 \bowtie_\theta E_2) \equiv (\sigma_{\theta_1} (E_1)) \bowtie_\theta E_2$$  

- **条件分属两侧关系**：  
  若选择条件 $\theta_1 \land \theta_2$ 中，$\theta_1$ 仅涉及 $E_1$ 、$\theta_2$ 仅涉及 $E_2$ ，选择可拆分下推到两侧。公式：  
  $$\sigma_{\theta_1 \land \theta_2} (E_1 \bowtie_\theta E_2) \equiv (\sigma_{\theta_1} (E_1)) \bowtie_\theta (\sigma_{\theta_2} (E_2))$$  

---

#### 6. 投影对θ-连接的分配律（Projection Distribution over θ-Join）
- **连接条件仅涉及投影属性**：  
  设 $L_1$ 、 $L_2$ 为  $E_1$ 、 $E_2$ 的属性集，若连接条件 $\theta$ 仅涉及 $L_1 \cup L_2$ ，投影可下推。公式：  
  $$\Pi_{L_1 \cup L_2} (E_1 \bowtie_\theta E_2) \equiv (\Pi_{L_1} (E_1)) \bowtie_\theta (\Pi_{L_2} (E_2))$$  

- **含连接条件相关额外属性**：  
  若连接条件 $\theta$ 涉及 $E_1$  、 $E_2$ 中不在 $L_1$ 、 $L_2$ 里的属性（ $L_3$ 、$L_4$ ），需扩展投影包含这些属性再下推。公式：  
  $$\Pi_{L_1 \cup L_2} (E_1 \bowtie_\theta E_2) \equiv \Pi_{L_1 \cup L_2} ((\Pi_{L_1 \cup L_3} (E_1)) \bowtie_\theta (\Pi_{L_2 \cup L_4} (E_2)))$$  

---

#### 7. 集合操作（Set Operations）
- **并与交的交换律**：  
  - 并（Union）： $E_1 \cup E_2 \equiv E_2 \cup E_1$   
  - 交（Intersection）： $E_1 \cap E_2 \equiv E_2 \cap E_1$  
  *差集（Set Difference）不满足交换律*。  

- **并与交的结合律**：  
  - 并： $(E_1 \cup E_2) \cup E_3 \equiv E_1 \cup (E_2 \cup E_3)$  
  - 交： $(E_1 \cap E_2) \cap E_3 \equiv E_1 \cap (E_2 \cap E_3)$  

- **选择对集合操作的分配律**：  
  选择可分配到并、交、差集操作，公式示例（以并为例）：  
  $$\sigma_\theta (E_1 \cup E_2) \equiv \sigma_\theta (E_1) \cup \sigma_\theta (E_2)$$  
  交、差集类似，如  $\sigma_\theta (E_1 \cap E_2) \equiv \sigma_\theta (E_1) \cap \sigma_\theta (E_2)$  。  

---

#### 8. 投影对并操作的分配律（Projection over Union）
若  $E_1$ 和 $E_2$ 模式相同，投影可分配到并操作。公式：  
$$\Pi_L (E_1 \cup E_2) \equiv (\Pi_L (E_1)) \cup (\Pi_L (E_2))$$  

---

#### 9. 选择对聚合的分配律（Selection over Aggregation）
设  $G$ 为分组属性， $A$ 为聚合表达式，若选择条件  $\theta$ 仅涉及 $G$ 中属性，选择可下推到聚合前。公式：    
$$\sigma_\theta ( _G \gamma_A (E)) \equiv _G \gamma_A (\sigma_\theta (E))$$    

---

#### 10. 外连接（Outer Joins）
- **全外连接交换律**：  
  全外连接（Full Outer Join）满足交换律，公式：  
  $$E_1 \bowtie E_2 \equiv E_2 \bowtie E_1$$  

- **左/右外连接转换**：  
  左外连接（Left Outer Join）和右外连接（Right Outer Join）可转换，公式：  
  $$E_1 \bowtie E_2 \equiv E_2 \bowtie E_1$$  

- **选择对外连接的分配限制**：  
  选择对外连接的分配需谨慎，若条件涉及外连接保留的空值（NULL），直接下推可能改变结果。例如：  
  $$\sigma_{year=2017} (instructor \bowtie teaches)$$  
  与下推选择到 `teaches` 后的结果可能不同（因外连接会保留无课程教师的元组，下推选择会过滤 `teaches` 空值，改变最终结果 ）。  

- **外连接不满足结合律**：  
  与内连接不同，外连接不满足结合律。示例：  
  设  $r(A,B) = \{(1,1)\}$ ，$s(B,C) = \{(1,1)\}$ ， $t(A,C)$ 为空。则：  
  $$(r \bowtie s) \bowtie t \not\equiv r \bowtie (s \bowtie t)$$  
  （前者结果含 $(1,1,1)$ ，后者因中间外连接引入空值，结果为 $(1,1,null)$  ）  


这些等价规则是查询优化的核心，优化器通过应用规则**重写关系代数表达式**，找到更高效的执行计划（如减少中间结果规模、优先过滤数据等 ）。实际数据库优化中，会依据统计信息（关系大小、索引等 ），结合规则生成并选择成本最低的计划。
### 查询优化 - 关系代数表达式变换与等价规则应用
#### 核心知识点梳理

---

#### 1. 等价规则的最小性（Minimality of Equivalence Rules）
- 若一组等价规则中，**无规则可由其他规则组合推导**，则称其为“最小集”。非最小集规则会增加表达式生成方式的数量，优化器通常用最小集减少计算开销。  

---

#### 2. 投影下推（Projection Pushing）
通过等价规则（如8.a、8.b）**下推投影操作**，移除中间结果中不必要的属性，缩小中间结果规模。  
- 示例：  
  原始子表达式连接结果含属性 `(ID, name, dept_name, salary, course_id, sec_id, semester, year)`，但仅需 `name` 和 `course_id` 参与后续运算。  
  优化后表达式：  
  $$\Pi_{\text{name,title}} \Big( \big( \Pi_{\text{name,course\_id}} \big( (\sigma_{\text{dept\_name = "Music"}} (\text{instructor})) \bowtie \text{teaches} \big) \bowtie \Pi_{\text{course\_id,title}} (\text{course}) \Big)$$  
  关键：`$\Pi_{\text{name,course\_id}}$` 投影下推，减少中间结果列数。  

---

#### 3. 连接顺序优化（Join Ordering）
自然连接满足**结合律**（规则6.a）和**交换律**（规则5），调整连接顺序可显著影响中间结果大小。  
- 结合律公式：  
  $$(r_1 \bowtie r_2) \bowtie r_3 \equiv r_1 \bowtie (r_2 \bowtie r_3)$$  
- 优化逻辑：优先连接**小关系**（如过滤后结果），减少临时结果规模。  
  示例：  
  - 先连接 `$\sigma_{\text{dept\_name = "Music"}} (\text{instructor}) \bowtie \text{teaches}$`（结果小，因音乐系教师少），再与 `$\Pi_{\text{course\_id,title}} (\text{course})$` 连接，比先连 `$\text{teaches} \bowtie \Pi_{\text{course\_id,title}} (\text{course})$`（结果大，含所有课程）更高效。  


---

#### 4. 等价表达式枚举（Enumeration of Equivalent Expressions）
优化器通过等价规则**系统生成等价表达式**，流程如下（伪代码）：  
```plaintext
procedure genAllEquivalent(E)
    EQ = {E}  // 初始仅含原始表达式
    repeat
        对 EQ 中每个表达式 E_i，匹配每条等价规则 R_j
        若 E_i 的子表达式 e_j 匹配 R_j 的一侧
            生成新表达式 E'（替换 e_j 为规则另一侧形式）
            若 E' 不在 EQ 中则添加
    until 无新表达式可添加
```  
- 优化点：  
  - **共享子表达式**：利用表达式表示技术，让不同表达式指向共享子结构，减少空间占用。  
  - **成本导向剪枝**：结合成本估算（如16.3节统计信息），避免生成高成本表达式，减少优化时间。  


---

#### 5. 多规则组合应用（Multiple Rule Applications）
通过**组合等价规则**，逐步优化查询表达式。  
- 示例（含年份过滤的查询）：  
  原始查询（限制2017年授课教师）：  
  $$\Pi_{\text{name,title}} \Big( \sigma_{\text{dept\_name = "Music"} \land \text{year = 2017}} \big( \text{instructor} \bowtie (\text{teaches} \bowtie \Pi_{\text{course\_id,title}} (\text{course})) \big) \Big)$$  

  优化步骤：  
  1. **结合律调整连接顺序**（规则6.a）：  
     $$\Pi_{\text{name,title}} \Big( \sigma_{\text{dept\_name = "Music"} \land \text{year = 2017}} \big( (\text{instructor} \bowtie \text{teaches}) \bowtie \Pi_{\text{course\_id,title}} (\text{course}) \big) \Big)$$  

  2. **选择下推**（规则7.a）：  
     $$\Pi_{\text{name,title}} \Big( \big( \sigma_{\text{dept\_name = "Music"} \land \text{year = 2017}} (\text{instructor} \bowtie \text{teaches}) \big) \bowtie \Pi_{\text{course\_id,title}} (\text{course}) \Big)$$  

  3. **拆分选择条件**（规则1）：  
     $$\Pi_{\text{name,title}} \Big( \big( \sigma_{\text{dept\_name = "Music"}} (\sigma_{\text{year = 2017}} (\text{instructor} \bowtie \text{teaches})) \big) \bowtie \Pi_{\text{course\_id,title}} (\text{course}) \Big)$$  

  4. **最终下推选择**（规则7.a）：  
     $$\Pi_{\text{name,title}} \Big( \big( \sigma_{\text{dept\_name = "Music"}} (\text{instructor}) \bowtie \sigma_{\text{year = 2017}} (\text{teaches}) \big) \bowtie \Pi_{\text{course\_id,title}} (\text{course}) \Big)$$  

  关键：通过规则组合，**尽早过滤数据**（如先过滤音乐系教师、2017年授课记录），缩小中间结果。  


---

#### 6. 外连接与内连接的差异（Outer vs Inner Joins）
外连接（如左外、右外、全外连接）**不满足所有内连接的等价规则**（如选择下推可能改变结果，因外连接保留空值）。  
- 示例：  
  表达式 `$\sigma_{\text{year=2017}} (\text{instructor} \bowtie \text{teaches})$`（左外连接）与下推选择到 `teaches` 后的结果不同（会过滤外连接保留的空值元组 ）。  


---

#### 7. 启发式优化与成本优化（Heuristic vs Cost - based Optimization）
- **启发式优化**：基于规则直接重写查询计划（如匹配规则则替换子树），执行快但**不保证最优**。  
- **成本优化**：结合统计信息（关系大小、索引深度等）估算计划成本，选择**最低成本计划**（如16.4节所述），更精准但计算开销更高。  

  
### 数据库查询优化 - 统计信息与成本估算
#### 核心知识点梳理  

---

#### 1. 直方图（Histogram）  
数据库通过**直方图**存储属性值分布，优化成本估算精度。  
- **等宽直方图（Equi-width Histogram）**：  
  将属性值范围均分，统计每个区间的元组数量。  
  示例（图16.6）：属性值1-25分5个区间（1-5、6-10等），统计各区间频率。  

- **等深直方图（Equi-depth Histogram）**：  
  调整区间边界，使每个区间包含相同数量的元组。仅需存储区间边界，无需存元组计数（因深度相同）。  
  示例：数据总元组500，分5个区间 → 每个区间约100元组，边界如 `(4, 8, 14, 19)` 。  

- **增强直方图**：  
  额外记录区间内**不同值数量**，避免假设值均匀分布。对高频值，存储前 `n` 个最频繁值及其计数（如年龄4、7、18、19、23），剩余值用直方图统计。  


---

#### 2. 选择操作的规模估算（Selection Size Estimation）  
根据选择条件（如等值、比较、组合条件），结合统计信息估算结果规模。  

##### （1）等值条件（$\sigma_{A=a}(r)$）  
- 若 `a` 是高频值且有计数，直接用计数估算结果规模。  
- 否则假设值均匀分布，估算公式：  
  $$\text{估算元组数} \approx \frac{n_r}{V(A, r)}$$  
  其中：  
  - $n_r$：关系 `r` 的总元组数  
  - $V(A, r)$：属性 `A` 的不同值数量  

- 若有直方图，定位 `a` 所在区间，用区间频率和不同值数量修正估算：  
  $$\text{估算元组数} \approx \frac{\text{区间频率}}{\text{区间不同值数量}}$$  


##### （2）比较条件（$\sigma_{A \leq v}(r)$）  
假设值均匀分布，结合属性最小/最大值（`min(A, r)`、`max(A, r)`）估算：  
- 若 $v < \text{min}(A, r)$ → 0 元组  
- 若 $v \geq \text{max}(A, r)$ → $n_r$ 元组  
- 否则：  
  $$\text{估算元组数} \approx n_r \cdot \frac{v - \text{min}(A, r)}{\text{max}(A, r) - \text{min}(A, r)}$$  

- 有直方图时，用区间数据更精准估算（细节需结合直方图类型推导）。  


##### （3）复杂条件（组合条件）  
- **合取（Conjunction）**：$\sigma_{\theta_1 \land \theta_2 \land \cdots \land \theta_n}(r)$  
  假设条件独立，总选择性为各条件选择性的乘积。  
  选择性（Selectivity）：满足条件的元组占比（如 $\theta_i$ 的选择性为 $s_i/n_r$ ）。  
  估算公式：  
  $$\text{估算元组数} \approx n_r \cdot \frac{s_1 \cdot s_2 \cdot \cdots \cdot s_n}{n_r^n}$$  

- **析取（Disjunction）**：$\sigma_{\theta_1 \lor \theta_2 \lor \cdots \lor \theta_n}(r)$  
  总选择性为 $1 - \prod_{i=1}^n (1 - \frac{s_i}{n_r})$ ，估算公式：  
  $$\text{估算元组数} \approx n_r \cdot \left[ 1 - \prod_{i=1}^n \left( 1 - \frac{s_i}{n_r} \right) \right]$$  


---

#### 3. 目录统计信息（Catalog Information）  
数据库目录存储关系的核心统计信息，用于成本估算：  
- $n_r$：关系 `r` 的元组总数  
- $b_r$：存储 `r` 的磁盘块数，公式：  
  $$b_r = \left\lceil \frac{n_r}{f_r} \right\rceil$$  
  （$f_r$：每个块可容纳的元组数量，即阻塞因子）  
- $l_r$：元组大小（字节）  
- $V(A, r)$：属性 `A` 的不同值数量（若 `A` 是键，$V(A, r) = n_r$ ）  

索引统计信息（如B⁺树高度、叶页数量）也存储在目录中。  


---

#### 4. 统计信息的维护（Maintaining Statistics）  
- **采样计算**：  
  不扫描全量数据，通过**随机采样**（避免偏差）计算统计信息（如直方图），降低计算开销。  

- **周期性更新**：  
  不实时维护统计信息（更新成本高），依赖 `ANALYZE`（Oracle/PostgreSQL）或 `RUNSTATS`（DB2）等命令**周期性更新**。部分系统会在数据量变化显著时自动触发更新。  


---

#### 5. 表达式结果的统计估算（Estimating Statistics of Expression Results）  
查询执行计划的成本依赖于操作输入的统计信息（如连接结果的规模）。优化器通过**自底向上遍历查询树**，逐层估算统计信息：  
1. 从最底层操作（如扫描、选择）开始，估算其结果规模。  
2. 基于底层结果，估算上层操作（如连接、投影）的规模。  
3. 最终累加各操作成本，得到完整查询计划的成本。  

尽管估算基于假设（可能不精确），但实践表明，**最低估算成本的计划通常接近实际最优成本**。  


以上统计信息和估算方法是查询优化器的“决策依据”，通过合理维护和利用这些数据，优化器能在众多执行计划中选择成本最低的方案，提升查询效率。  
>日期：6.5  

### 数据库查询优化 - 连接与操作规模估算及执行计划选择  

---

#### 1. 连接操作规模估算（Join Size Estimation）  
连接结果的规模估算依赖于关系的统计信息（如元组数量、不同值数量），分三种场景：  

##### （1）无公共属性（$R \cap S = \emptyset$）  
自然连接等价于笛卡尔积，结果元组数为：  
$$n_{r \bowtie s} = n_r \times n_s$$  

##### （2）公共属性是某关系的键（如 $R \cap S$ 是 $R$ 的键 ）  
- $r$ 的元组最多与 $s$ 的**一个元组**连接，结果元组数 $\leq n_s$（对称场景同理）。  


##### （3）公共属性不是键（最复杂场景）  
假设属性值**均匀分布**，公共属性为 $A$，则：  
- $r$ 中一个元组 $t$ 与 $s$ 连接的平均元组数：$\frac{n_s}{V(A, s)}$  
- 总连接结果元组数估算：  
  $$n_{r \bowtie s} \approx \frac{n_r \times n_s}{V(A, s)}$$  
  交换 $r$ 和 $s$ 角色，也可估算为 $\frac{n_r \times n_s}{V(A, r)}$ 。实际取**较小值**（因可能存在不参与连接的“悬挂元组”）。  


##### （4）扩展：带直方图的估算  
若连接属性有直方图且区间一致，可**按区间拆分估算**：  
- 对每个区间，用区间内元组数替代 $n_r$ 或 $n_s$，用区间内不同值数量替代 $V(A, r)$ 或 $V(A, s)$。  
- 累加各区间估算结果，得到总规模。  


---

#### 2. 其他操作的规模估算（Size Estimation for Other Operations）  

##### （1）投影（Projection）  
投影 $\Pi_A(r)$ 会去重，结果元组数等于属性 $A$ 的不同值数量：  
$$n_{\Pi_A(r)} = V(A, r)$$  


##### （2）聚合（Aggregation）  
分组聚合 $_G \gamma_A(r)$ 的结果元组数等于分组属性 $G$ 的不同值数量：  
$$n_{_G \gamma_A(r)} = V(G, r)$$  


##### （3）集合操作（Set Operations）  
- **并集（ $r \cup s$ ）**：结果元组数估算为  $n_r + n_s$ （假设无重复，实际需结合去重逻辑）。  
- **交集（ $r \cap s$ ）**：结果元组数估算为  $\min(n_r, n_s)$ （假设重复度高）。  
- **差集（ $r - s$ ）**：结果元组数估算为  $n_r$ （假设无重叠，实际需调整）。  


##### （4）外连接（Outer Join）  
- 左外连接（$r \bowtie s$）：结果元组数 $\approx n_{r \bowtie s} + n_r$（含 $r$ 中未匹配元组）。  
- 右外连接（$r \Join s$）：对称于左外连接，$\approx n_{r \bowtie s} + n_s$。  
- 全外连接（$r \Join s$）：$\approx n_{r \bowtie s} + n_r + n_s$。  


---

#### 3. 不同值数量的估算（Estimation of Number of Distinct Values）  

##### （1）选择操作（Selection）  
对选择  $\sigma_\theta(r)$ ，属性  $A$  的不同值数量  $V(A, \sigma_\theta(r))$ 估算：  
- 若  $\theta$  强制  $A$  取固定值（如  $A=3$  ）→  $V=1$。  
- 若  $\theta$  强制  $A$  取固定集合（如 $A=1 \lor A=3$ ）→  $V$ 为集合大小。  
- 若  $\theta$  是比较条件（如  $A \leq v$ ）→  $V \approx V(A, r) \times s$ （ $s$ 为选择的选择性）。  


##### （2）连接操作（Join）  
对连接 $r \bowtie s$ ，属性集 $A$ 的不同值数量 $V(A, r \bowtie s)$  估算：  
- 若 $A$ 全来自 $r$  →  $V \approx \min(V(A, r), n_{r \bowtie s})$ （对称适用于全来自 $s$ ）。  
- 若 $A$ 含 $r$ 的 $A1$ 和 $s$ 的 $A2$ →  
  $$V \approx \min\Big( V(A1, r) \times V(A2 - A1, s),\ V(A1 - A2, r) \times V(A2, s),\ n_{r \bowtie s} \Big)$$  
  （$A1 - A2$ 表示仅来自 $r$ 的属性，$A2 - A1$  同理）  


---

#### 4. 执行计划的选择（Choice of Evaluation Plans）  

##### （1）基于成本的优化器（Cost-based Optimizer）  
生成等价查询计划，选择**估算成本最低**的计划。需结合：  
- 16.3节的统计信息估算（如操作结果规模）。  
- 算法与执行方法的成本模型（如扫描、连接的磁盘I/O、CPU开销）。  


##### （2）连接顺序选择（Join-Order Selection）  
多关系连接（如 $r_1 \bowtie r_2 \bowtie \cdots \bowtie r_n$）的计划数量极多，公式为：  
$$\text{计划数} = \frac{(2(n-1))!}{(n-1)!}$$  
（$n=3$ 时为12种，$n=5$ 时为1680种，$n=10$ 时超176亿 ）  

优化器通过**启发式规则**（如优先连接小关系）或**动态规划**减少计算量，避免枚举所有计划。  


##### （3）启发式优化（Heuristic Optimization）  
用经验规则剪枝（如尽早过滤、下推投影），降低优化成本，但**不保证最优**。  
### 数据库查询优化 - 连接顺序与执行计划优化（动态规划与启发式规则）
#### 核心知识点梳理  

---

#### 1. 动态规划优化连接顺序（Dynamic Programming for Join-Order）  
通过**动态规划算法**枚举并选择最优连接顺序，核心逻辑：  

##### （1）算法框架（伪代码）  
```python
def FindBestPlan(S):
    if bestplan[S].cost != ∞:  # 已计算过最优计划
        return bestplan[S]
    if len(S) == 1:  # 单关系，直接访问
        bestplan[S] = 基于选择条件的最优访问计划（索引扫描/全表扫描）
        return bestplan[S]
    # 多关系，拆分子集递归计算
    for S1 in 所有非空真子集 of S:
        S2 = S - S1
        P1 = FindBestPlan(S1)
        P2 = FindBestPlan(S2)
        for 连接算法A in [嵌套循环连接、哈希连接、归并连接等]:
            # 计算连接成本（需考虑算法特性，如索引嵌套循环的内外表选择）
            if A 是索引嵌套循环连接:
                选择内外表（P1/P2 或单关系+索引）
                if 内表有连接属性索引:
                    cost = P1.cost + P2.cost + A的执行成本（含索引查找）
                else:
                    cost = ∞  # 无法用索引嵌套循环
            # 其他连接算法同理计算成本...
            # 更新最优计划
            if cost < bestplan[S].cost:
                bestplan[S].cost = cost
                bestplan[S].plan = f"执行{P1.plan}; 执行{P2.plan}; 用{A}连接结果"
    return bestplan[S]
```  

##### （2）关键逻辑  
- **状态存储**：用 `bestplan` 字典缓存每个关系集合 `S` 的最优计划及成本（初始为 `∞`）。  
- **子集拆分**：递归拆分 `S` 为两个不相交子集 `S1` 和 `S2`，计算子集最优计划后，尝试所有连接算法组合。  
- **连接算法适配**：  
  - 索引嵌套循环连接需检查内表是否有连接属性索引，内外表选择会影响成本。  
  - 哈希连接需考虑构建表（build）和探测表（probe）的选择。  


##### （3）复杂度与优化  
- 时间复杂度：$O(3^n)$（$n$ 为关系数量），但通过**剪枝**（如按字母序避免重复拆分）可优化。  
- 避免笛卡尔积：拆分时过滤无连接条件的子集，减少无效计算。  


---

#### 2. 等价规则与物理优化（Equivalence Rules & Physical Optimization）  
通过**等价规则**扩展优化范围（如外连接、聚合、集合操作），核心思路：  

##### （1）逻辑到物理的转换  
引入**物理等价规则**，将逻辑操作（如自然连接）转换为物理算法（如哈希连接、嵌套循环连接）。  

##### （2）优化实现要点  
- **表达式复用**：避免重复生成等价子表达式，用高效结构存储。  
- **记忆化（Memoization）**：缓存子表达式的最优计划，重复查询时直接复用。  
- **剪枝策略**：跟踪子表达式的最优成本，丢弃比当前最优更差的计划。  


---

#### 3. 启发式优化规则（Heuristic Optimization）  
因基于成本的优化计算量大，用启发式规则**快速剪枝**，典型规则：  

##### （1）尽早执行选择（Perform Selection Early）  
优先执行选择操作（$\sigma$），减少后续操作的数据量。  
- 例外场景：若选择条件无索引、且关联小表时，延迟选择可能更优（需动态规划处理）。  


##### （2）尽早执行投影（Perform Projection Early）  
优先执行投影操作（$\Pi$），减少临时关系的大小。  


##### （3）有趣排序（Interesting Sort Order）  
若连接结果的排序对后续操作（如归并连接）有用，可牺牲当前连接成本，保留排序，避免重复排序。  


---

#### 4. 扩展：处理复杂查询（Outer Join、聚合等）  
动态规划和等价规则可扩展到复杂操作：  
- **外连接**：需保留未匹配元组，成本估算需包含“悬挂元组”的处理。  
- **聚合**：结合分组属性的统计信息（如不同值数量）估算结果规模。  
- **集合操作**：将并、交、差转换为选择条件的组合（如并→析取、交→合取）。  

### 数据库查询优化 - 启发式规则、嵌套子查询与连接优化
#### 核心知识点梳理  

---

#### 1. 启发式优化策略（Heuristic Optimization）  
通过经验规则快速简化查询优化，典型规则：  

##### （1）尽早执行选择与投影  
- **选择（$\sigma$）优先**：优先过滤数据，减少后续操作规模（例外：过滤条件无索引且关联小表时可能需延迟）。  
- **投影（$\Pi$）尽早**：减少临时关系的属性数量，降低存储和计算开销。  


##### （2）左深连接（Left-Deep Join Orders）  
限制连接顺序为**左深树**（右操作数为初始关系），降低优化复杂度：  
- 复杂度：枚举所有左深连接顺序为 $O(n!)$（$n$ 为关系数），远低于全连接顺序的 $O(3^n)$。  
- 优势：适配流水线执行（右操作数为存储关系，仅需流水线处理左输入）。  


##### （3）优化预算与计划缓存  
- **优化预算**：设置优化时间/成本阈值，超阈值则返回当前最优计划。  
- **计划缓存（Plan Caching）**：重复查询（仅常量不同）时复用缓存计划，平衡优化开销与执行效率。  


---

#### 2. 嵌套子查询优化（Optimizing Nested Subqueries）  
SQL 中子查询可转换为连接以提升效率，核心方法：  

##### （1）相关子查询的低效性  
相关子查询（Correlated Subquery）需为外层元组逐一遍历，触发大量随机 I/O，示例：  
```sql
SELECT name 
FROM instructor 
WHERE EXISTS (
    SELECT * 
    FROM teaches 
    WHERE instructor.ID = teaches.ID AND teaches.year = 2019
);
```  
概念上需为每个 `instructor` 元组执行子查询，效率极低。  


##### （2）转换为连接（Join Rewriting）  
用**半连接（Semijoin）**或等价连接替代子查询，避免逐行调用：  

###### 半连接（Multiset Semijoin）  
保留左关系中与右关系匹配的元组（含重复计数），语法：  
$$r \ltimes_\theta s$$  
示例转换：  
```sql
-- 原查询（EXISTS 子查询）
SELECT name FROM instructor 
WHERE EXISTS (SELECT * FROM teaches 
              WHERE instructor.ID = teaches.ID AND teaches.year = 2019);

-- 转换为半连接
Π_{name}(instructor ⋈_{instructor.ID=teaches.ID ∧ teaches.year=2019} teaches)
```  


###### 反半连接（Anti-Semijoin）  
处理 `NOT EXISTS` 子查询，保留左关系中与右关系无匹配的元组，语法：  
$$r \overline{\ltimes}_\theta s$$  
示例：  
```sql
-- 原查询（NOT EXISTS 子查询）
SELECT name FROM instructor 
WHERE NOT EXISTS (SELECT * FROM teaches 
                  WHERE instructor.ID = teaches.ID AND teaches.year = 2019);

-- 转换为反半连接
Π_{name}(instructor \overline{\ltimes}_{instructor.ID=teaches.ID ∧ teaches.year=2019} teaches)
```  


##### （3）正确性保障  
转换需保留 SQL 的**多集合语义**（避免重复元组丢失或错误合并），半连接天然适配 SQL 结果的重复计数。  


---

#### 3. 工业级优化器实践  
- **分层优化**：如 System R 和 Starburst 项目，基于 SQL 的嵌套块（nested-block）概念分层优化，每层独立应用成本模型。  
- **混合策略**：部分场景用启发式规则快速剪枝，关键路径用成本模型精确优化。  
- **适配复杂操作**：扩展优化范围至聚合、外连接等，通过等价规则生成物理执行计划。  

### 数据库查询优化 - 去关联化与物化视图
#### 核心知识点梳理  

---

#### 1. 去关联化（Decorrelation）  
将嵌套子查询转换为连接/半连接操作，提升执行效率，核心逻辑：  

##### （1）子查询类型适配  
- **`EXISTS` 子查询**：转换为**半连接（Semijoin）**，保留左关系中与右关系匹配的元组。  
- **`NOT EXISTS` 子查询**：转换为**反半连接（Anti-Semijoin）**，保留左关系中与右关系无匹配的元组。  
- **`IN` 子查询**：扩展半连接谓词，添加对应条件。  


##### （2）带聚合的子查询转换  
示例：查找 2019 年教授多门课程的教师，原查询：  
```sql
SELECT name 
FROM instructor 
WHERE 1 < (
    SELECT COUNT(*) 
    FROM teaches 
    WHERE instructor.ID = teaches.ID AND teaches.year = 2019
);
```  

转换为半连接（含聚合分组）：  
$$\Pi_{\text{name}} \Big( \text{instructor} \ltimes_{(\text{instructor.ID}=TID) \land (1 \lt cnt)} \big( \Gamma_{\text{ID as TID}, \text{count}(*) \text{ as cnt}} (\sigma_{\text{year}=2019} (\text{teaches})) \big) \Big)$$  

**关键逻辑**：  
- 子查询的连接条件（`instructor.ID = teaches.ID`）移入半连接谓词。  
- 聚合（`COUNT(*)`）需按 `ID` 分组，确保每个教师的课程计数独立。  


##### （3）局限性与成本权衡  
- 复杂聚合或标量子查询的去关联化难度高，部分场景无法转换。  
- 优化器需基于成本模型判断是否转换（如转换后是否减少 I/O 或计算开销）。  


---

#### 2. 物化视图（Materialized Views）  
预计算并存储视图结果，加速查询，核心概念：  

##### （1）基本定义  
普通视图仅存储查询逻辑，**物化视图**存储计算结果（冗余数据），适合频繁查询的聚合/连接结果。示例：  
```sql
CREATE VIEW department_total_salary (dept_name, total_salary) AS 
SELECT dept_name, SUM(salary) 
FROM instructor 
GROUP BY dept_name;
```  

若需频繁查询部门总工资，物化视图可直接读取结果，避免重复聚合计算。  


##### （2）视图维护（View Maintenance）  
物化视图需随基表更新而同步，策略包括：  
- **手动维护**：修改基表时显式更新视图（易出错，不推荐）。  
- **触发器维护**：基表增/删/改时触发更新，可选择：  
  - 全量重算：简单但低效（如 `UPDATE` 时重新计算整个视图）。  
  - 增量维护（Incremental View Maintenance）：仅更新受影响部分，高效且常用。  


##### （3）增量维护算法  
针对关系操作（连接、选择、投影、聚合），推导增量更新公式：  

###### 连接操作（Join）  
设物化视图 $v = r \bowtie s$，基表 $r$ 插入元组集合 $i_r$，则视图增量更新：  
$$v^{new} = v^{old} \cup (i_r \bowtie s)$$  

基表 $r$ 删除元组集合 $d_r$，则：  
$$v^{new} = v^{old} - (d_r \bowtie s)$$  


###### 选择操作（Selection）  
设物化视图 $v = \sigma_\theta(r)$，基表 $r$ 插入元组集合 $i_r$，则：  
$$v^{new} = v^{old} \cup \sigma_\theta(i_r)$$  

基表 $r$ 删除元组集合 $d_r$，则：  
$$v^{new} = v^{old} - \sigma_\theta(d_r)$$  


###### 投影操作（Projection）  
设物化视图 $v = \Pi_A(r)$，需维护投影元组的**派生计数**（避免错误删除）：  
- 插入 $i_r$：若投影元组已存在，计数 +1；否则新增计数为 1。  
- 删除 $d_r$：投影元组计数 -1，计数为 0 时删除该元组。  


###### 聚合操作（Aggregation）  
以 `COUNT` 为例，物化视图 $v = \Gamma_{G, \text{count}(B)}(r)$（按 $G$ 分组计数）：  
- 插入 $i_r$：分组存在则计数 +1，否则新增分组（计数 1）。  
- 删除 $d_r$：分组计数 -1，计数为 0 时删除该分组。  


##### （4）维护时机  
- **立即维护（Immediate View Maintenance）**：基表更新时同步维护视图（事务内完成，强一致性）。  
- **延迟维护（Deferred View Maintenance）**：批量收集更新，后续统一维护（降低事务开销，但可能临时不一致）。  


---

#### 3. 工业级实践  
- 复杂嵌套子查询的去关联化难度高，建议业务层简化查询逻辑，避免过度依赖优化器转换。  
- 物化视图适合静态/低频更新、高频查询的场景（如报表统计），需权衡存储成本与查询效率。  



---

#### 1. 物化视图的查询优化（Query Optimization with Materialized Views）  
物化视图可被视为“预计算的结果集”，优化器需智能识别其复用场景：  

##### （1）查询重写（Rewrite Queries to Use Materialized Views）  
若存在物化视图 $v = r \bowtie s$，用户查询为 $r \bowtie s \bowtie t$，可重写为 $v \bowtie t$，减少重复计算。  
**逻辑**：复用预存的连接结果，避免多次执行 $r \bowtie s$。  


##### （2）视图定义替换（Replace Materialized View with View Definition）  
若物化视图 $v = r \bowtie s$ 无索引，但基表 $r$、$s$ 有索引，查询 $\sigma_{A=10}(v)$ 可替换为 $\sigma_{A=10}(r) \bowtie s$，利用基表索引加速。  
**示例**：  
```sql
-- 原查询（直接查物化视图，可能全表扫描）
SELECT * FROM v WHERE A = 10;

-- 替换为基表查询（利用 r.A 和 s.B 的索引）
SELECT * FROM (SELECT * FROM r WHERE A = 10) AS r_filtered 
JOIN s ON r_filtered.key = s.key;
```  


---

#### 2. 物化视图与索引选择（Materialized View and Index Selection）  
需根据**系统负载（Workload）**选择物化视图和索引，核心目标：  
- 最小化查询与更新的总执行时间（含视图维护成本）。  
- 平衡不同查询/更新的优先级（如部分查询需快速响应，部分可容忍延迟）。  

**工具支持**：数据库提供工具（如 SQL Server Database Tuning Assistant、Oracle SQL Tuning Wizard），通过分析历史负载建议物化视图和索引。  


---

#### 3. 高级查询优化技术  

##### （1）Top-K 优化（Top-K Optimization）  
查询仅需前 $K$ 条结果时，避免全量计算后排序。  
**策略**：  
- 管道化计划（Pipelined Plans）：流式生成排序结果，提前终止计算。  
- 基于统计的剪枝：预估 Top-K 结果的属性范围，过滤无关数据。  


##### （2）连接最小化（Join Minimization）  
视图生成的查询可能包含冗余连接，可删除不影响结果的连接。  
**示例**：  
视图 $v$ 包含 `instructor JOIN department`，但查询仅用 `instructor` 属性，且 `instructor.dept_name` 非空，则 `department` 连接可删除。  


##### （3）更新优化（Optimization of Updates）  
处理更新时需避免**万圣节问题（Halloween Problem）**：更新影响查询执行（如更新后的元组被重复扫描）。  
**解决策略**：  
1. 先执行查询逻辑，生成受影响元组列表，最后批量更新。  
2. 检查是否可能触发问题（如更新不影响索引属性时可并行执行）。  


##### （4）多查询优化（Multiquery Optimization）  
批量查询时，复用公共子表达式（Common Subexpression Elimination），减少重复计算。  
**示例**：  
多个查询均需 `SELECT COUNT(*) FROM orders WHERE date = '2023-01-01'`，仅计算一次并复用结果。  


##### （5）参数化查询优化（Parametric Query Optimization）  
查询参数（如部门名称）变化时，预生成多个最优计划，避免重复优化。  
**逻辑**：  
优化器输出一组计划（每个计划对应参数的一个取值范围），查询执行时直接选择匹配计划。  


##### （6）物化视图的增量维护（Incremental View Maintenance）  
针对聚合操作（`sum`、`avg`、`min`、`max` 等），推导增量更新公式：  

###### Sum 聚合  
物化视图 $v = \Gamma_{G, \text{sum}(B)}(r)$（按 $G$ 分组求和）：  
- 插入 $i_r$：分组存在则累加 $t.B$，否则新增分组。  
- 删除 $d_r$：分组求和减去 $t.B$，计数为 0 时删除分组。  


###### Avg 聚合  
维护 `sum` 和 `count`，通过 $\text{avg} = \text{sum} / \text{count}$ 推导，避免直接更新平均值。  


###### Min/Max 聚合  
插入时直接更新；删除时需重新扫描分组找新的 Min/Max（建议为 $(G, B)$ 建索引加速）。  


---

#### 4. 表达式处理（Handling Expressions）  
复杂表达式的物化视图增量更新，需递归推导子表达式的增量变化。  
**示例**：  
视图 $E_1 \bowtie E_2$，基表 $r$ 插入 $i_r$ 时，先推导 $E_1$ 的增量 $D_1$，再计算 $D_1 \bowtie E_2$ 作为视图增量。  

### 数据库查询优化核心知识点
#### 一、统计信息与查询优化基础
数据库系统通过维护关系的统计信息，为操作评估策略提供依据，关键统计信息包括：
- 关系 `r` 的元组数量（记为 `n_tuples(r)`  ）
- 关系 `r` 中记录（元组）的字节大小（记为 `tuple_size(r)`  ） 
- 关系 `r` 特定属性的不同值数量（记为 `n_distinct(r, A)` ，`A` 为属性 ）  

多数系统用**直方图（Histograms）**存储属性值在各范围的分布，常通过采样计算，公式化表示为：若属性 `A` 取值划分为 `k` 个区间 `[v₁, v₂), [v₂, v₃), ..., [vₖ, vₖ₊₁]`，直方图记录每个区间内值的数量 `h(i)`，用于估算操作结果大小与成本。  

这些统计信息用于估算操作结果规模（如连接、选择后的元组数量）和执行成本，公式化的执行成本估算涉及磁盘 I/O、内存使用、CPU 计算等，是选择查询处理策略的关键依据，尤其在多个索引辅助查询时，统计信息影响索引选择与执行路径规划。  

#### 二、查询计划生成与优化技术
- **等价规则与计划生成**：利用等价规则（如选择下推、投影下推等）生成表达式的替代评估计划，公式化表示为通过 `Expr₁ ⇨ Expr₂`（`Expr₁` 与 `Expr₂` 等价）的规则，枚举可能的执行计划，选择成本最低的，优化技术（如剪枝）减少需生成的计划数量。  
- **启发式规则**：用启发式规则减少计划搜索空间，降低优化成本，典型规则：  
  ```
  - 尽早执行选择操作（“Perform selection operations as early as possible”）：优先过滤数据，减少后续操作的数据量，公式化体现为将选择操作 `σ` 尽可能靠近数据源。  
  - 尽早执行投影操作（“Perform projections early”）：提前减少属性数量，降低存储和计算开销，即 `Π` 操作尽早执行。  
  - 避免笛卡尔积（“Avoid Cartesian products”）：无关联条件的笛卡尔积会产生大量冗余数据，优先识别并避免。  
  ```
- **物化视图与增量维护**：物化视图（Materialized Views）预存查询结果加速查询，基表更新时需增量维护，通过计算操作的“差异（differential）”实现，公式化表示为：若物化视图 `MV` 由操作 `Op` 定义，基表变化 `ΔR`，则 `ΔMV = Op'(ΔR, MV_old)` ，`Op'` 为增量计算逻辑（如连接、聚合的增量算法 ）。涉及视图选择（选哪些视图物化）、查询重写（用物化视图替代原查询）等优化点。  

#### 三、高级优化技术
- **Top-K 优化**：针对只需前 `K` 条结果的查询，避免全量计算后排序，采用管道化计划（Pipelined Plans）流式生成排序结果，或基于统计预估 Top-K 范围剪枝数据。  
- **连接最小化**：视图查询中删除冗余连接，如视图含 `r ⨯ s` 但查询仅用 `r` 属性，且 `r` 关联属性非空时，可移除 `s` 连接，公式化表示为识别并删除不影响结果的连接操作。  
- **更新优化**：处理更新时避免“万圣节问题（Halloween Problem）”，即更新影响查询执行（如更新元组被重复扫描），策略包括先执行查询逻辑生成受影响元组列表再批量更新，或检查是否触发问题（如更新不涉及索引属性时并行执行 ）。  
- **多查询优化**：批量查询时复用公共子表达式（Common Subexpression Elimination），减少重复计算，公式化表示为识别多个查询中的公共部分 `Expr`，仅计算一次并复用结果。  
- **参数化查询优化**：预生成不同参数取值的最优计划，查询执行时直接匹配，避免重复优化，即优化器输出一组计划 `{Plan₁, Plan₂, ...}`，每个计划对应参数的一个取值范围，执行时根据参数选对应计划。  

#### 四、自适应查询处理（Adaptive Query Processing）
因查询优化基于统计估算，可能选到低效计划，自适应查询处理动态调整执行计划：  
- **执行时算子选择**：如 SQL Server 的自适应连接算法，根据 outer input 大小动态选嵌套循环连接（nested loops join）或哈希连接（hash join），公式化逻辑为：  
  ```
  IF size(outer_input) < threshold THEN
      USE nested loops join
  ELSE
      USE hash join
  ```  
- **执行中计划调整**：监控计划执行行为，若统计信息与预估差异大（如实际数据分布和预估不符），终止当前执行，用执行中收集的统计信息生成新计划重启，避免重复选择低效计划，目标是使查询评估总成本接近“有精确统计时的最优计划成本”。  

#### 五、总结
- 查询优化是将用户查询转换为更高效等价形式的过程，核心是找到执行成本低的关系代数表达式，通过等价规则枚举、启发式剪枝、统计信息估算成本实现。  
- 复杂查询涉及大量磁盘访问，因磁盘 I/O 相对内存和 CPU 速度慢，需投入计算选择最小化磁盘访问的方法，统计信息、等价规则、物化视图等技术共同支撑高效查询执行策略的选择 。

>日期：6.6
>课后习题选做  

---


#### **16.16 索引使用优化**  
**题目**：已知 `department` 关系上有 `(dept_name, building)` 的 B⁺-tree 索引，处理选择操作：  
$$\sigma_{(\text{building} < \text{“Watson”}) \land (\text{budget} < 55000) \land (\text{dept\_name} = \text{“Music”})} (\text{department})$$  

**解答**：  
B⁺-tree 索引的键是 `(dept_name, building)`，可按以下步骤利用索引：  
1. **匹配索引前缀**：先通过 `dept_name = "Music"` 定位索引叶子节点（精准匹配最左前缀）。  
2. **范围过滤**：在 `dept_name = "Music"` 的子树中，扫描 `building < "Watson"` 的条目，得到候选元组的行指针。  
3. **回表过滤剩余条件**：通过行指针访问 `department` 元组，检查 `budget < 55000`（此条件无法通过索引过滤，需回表验证）。  

**逻辑公式**：  
利用索引的选择操作 = 索引前缀匹配 + 范围扫描 + 回表过滤剩余条件。  


#### **16.17 关系代数等价变换**  
**题目**：用 16.2.1 节的等价规则，推导以下等价式：  
- **a.** $\sigma_{\theta_1 \land \theta_2 \land \theta_3}(E) \equiv \sigma_{\theta_1}(\sigma_{\theta_2}(\sigma_{\theta_3}(E)))$  
- **b.** $\sigma_{\theta_1 \land \theta_2}(E_1 \bowtie_{\theta_3} E_2) \equiv \sigma_{\theta_1}(E_1 \bowtie_{\theta_3} (\sigma_{\theta_2}(E_2)))$（$\theta_2$ 仅涉及 $E_2$ 的属性）  


**解答 a**：  
依据**选择操作的结合律**（$\sigma_{\theta_1 \land \theta_2}(E) \equiv \sigma_{\theta_1}(\sigma_{\theta_2}(E))$），可递归推导：  
1. 先将 $\theta_2 \land \theta_3$ 视为整体，得 $\sigma_{\theta_1 \land (\theta_2 \land \theta_3)}(E) \equiv \sigma_{\theta_1}(\sigma_{\theta_2 \land \theta_3}(E))$。  
2. 再对 $\sigma_{\theta_2 \land \theta_3}(E)$ 应用结合律，得 $\sigma_{\theta_2 \land \theta_3}(E) \equiv \sigma_{\theta_2}(\sigma_{\theta_3}(E))$。  
3. 最终推导：  
$$\sigma_{\theta_1 \land \theta_2 \land \theta_3}(E) \equiv \sigma_{\theta_1}(\sigma_{\theta_2}(\sigma_{\theta_3}(E)))$$  


**解答 b**：  
依据**选择下推（Push Selection Below Join）**规则：若选择条件 $\theta_2$ 仅涉及 $E_2$ 的属性，则 $\sigma_{\theta_2}(E_1 \bowtie_{\theta_3} E_2) \equiv E_1 \bowtie_{\theta_3} (\sigma_{\theta_2}(E_2))$。  

推导步骤：  
1. 原表达式 $\sigma_{\theta_1 \land \theta_2}(E_1 \bowtie_{\theta_3} E_2)$ 可拆分为 $\sigma_{\theta_1}(\sigma_{\theta_2}(E_1 \bowtie_{\theta_3} E_2))$（结合律）。  
2. 对 $\sigma_{\theta_2}(E_1 \bowtie_{\theta_3} E_2)$ 应用选择下推，得 $\sigma_{\theta_2}(E_1 \bowtie_{\theta_3} E_2) \equiv E_1 \bowtie_{\theta_3} (\sigma_{\theta_2}(E_2))$（因 $\theta_2$ 仅涉及 $E_2$）。  
3. 代入后最终等价式：  
$$\sigma_{\theta_1 \land \theta_2}(E_1 \bowtie_{\theta_3} E_2) \equiv \sigma_{\theta_1}(E_1 \bowtie_{\theta_3} (\sigma_{\theta_2}(E_2)))$$  


#### **16.18 半连接与自然连接的等价性**  
**题目**：考虑表达式 $\sigma_\theta(E_1 \ltimes E_2)$ 和 $\sigma_\theta(E_1 \bowtie E_2)$：  
- **a.** 举示例说明二者不等价。  
- **b.** 给出 $\theta$ 的条件，使二者等价。  


**解答 a**：  
设 $E_1(a, b)$ 数据：`{(1, 2), (3, 4)}`，$E_2(a, c)$ 数据：`{(1, 5), (1, 6)}`，$\theta$ 为 `b = 2`。  

- $E_1 \ltimes E_2$（半连接）：仅保留 $E_1$ 中与 $E_2$ 匹配的元组，结果为 `{(1, 2)}`。  
- 过滤后 $\sigma_\theta(E_1 \ltimes E_2)$：结果仍为 `{(1, 2)}`。  

- $E_1 \bowtie E_2$（自然连接）：结果为 `{(1, 2, 5), (1, 2, 6)}`。  
- 过滤后 $\sigma_\theta(E_1 \bowtie E_2)$：结果为 `{(1, 2, 5), (1, 2, 6)}`。  

显然，半连接结果是 $E_1$ 的子集，而自然连接结果是 $E_1 \times E_2$ 的子集，二者不等价。  


**解答 b**：  
若 $\theta$ 仅涉及 $E_1$ 的属性（即 $\theta$ 中无 $E_2$ 的属性），则：  
- $E_1 \ltimes E_2$ 的结果是 $E_1$ 中与 $E_2$ 匹配的元组（保留 $E_1$ 的属性）。  
- $E_1 \bowtie E_2$ 的结果是 $E_1$ 与 $E_2$ 匹配的元组（含 $E_1$ 和 $E_2$ 的属性），但 $\theta$ 仅过滤 $E_1$ 的属性，因此 $\sigma_\theta(E_1 \ltimes E_2)$ 和 $\sigma_\theta(E_1 \bowtie E_2)$ 的 $E_1$ 部分一致。  

**条件**：$\theta$ 的所有属性均来自 $E_1$。  


#### **16.19 等价规则的完备性**  
**题目**：判断 16.2.1 节的等价规则是否“完备”（即任意等价表达式均可通过规则推导）。提示：考虑 $\sigma_{3=5}(r) \equiv \{\}$。  


**解答**：  
16.2.1 节的等价规则（如选择下推、结合律、交换律等）**不完备**。  

反例：$\sigma_{3=5}(r) \equiv \{\}$（恒假条件的选择结果为空）。现有规则无法直接推导出“任意恒假条件等价于空集”，需额外规则（如“恒假条件的选择结果为空”）补充。因此，原有规则集不完备。  


#### **16.20 直方图估算选择结果大小**  
**题目**：解释如何用直方图估算 $\sigma_{A \leq v}(r)$ 的结果大小。  


**解答**：  
假设属性 $A$ 的直方图将取值划分为 $k$ 个区间 $[v_0, v_1), [v_1, v_2), ..., [v_{k-1}, v_k]$，每个区间的元组数量为 $h_i$。  

估算步骤：  
1. **定位区间**：找到最大的 $i$ 使得 $v_i \leq v$，记为区间 $[v_{i-1}, v_i]$。  
2. **计算区间内的比例**：若 $v$ 落在区间 $[v_{i-1}, v_i]$ 内，假设该区间值均匀分布，则此区间内满足 $A \leq v$ 的元组比例为 $\frac{v - v_{i-1}}{v_i - v_{i-1}}$，对应数量为 $h_i \times \frac{v - v_{i-1}}{v_i - v_{i-1}}$。  
3. **累加左侧区间**：所有 $j < i$ 的区间元组数量 $h_j$ 直接累加。  

**公式**：  
$$\text{估算大小} = \sum_{j=0}^{i-1} h_j + h_i \times \frac{v - v_{i-1}}{v_i - v_{i-1}}$$  


#### **16.21 跨直方图的连接结果估算**  
**题目**：若关系 $r$ 和 $s$ 分别在 $r.A$ 和 $s.A$ 上有直方图（范围不同），如何估算 $r \bowtie s$ 的结果大小？  


**解答**：  
1. **统一区间**：将两个直方图的区间拆分，确保所有区间端点一致（如合并 $r$ 和 $s$ 的区间，生成新的细粒度区间）。  
2. **逐区间计算连接数**：对每个新区间 $[a, b]$，设 $r$ 中该区间的元组数量为 $h_r$，$s$ 中为 $h_s$，则该区间对连接结果的贡献为 $h_r \times h_s$（假设属性值均匀分布，连接条件为 $r.A = s.A$）。  
3. **累加所有区间**：总估算大小为 $\sum (h_r \times h_s)$。  


#### **16.22 去关联化查询（Multiset Semijoin）**  
**题目**：对查询  
```sql
select A, B
from r
where r.B < some (select B from s where s.A = r.A)
```  
用 multiset 半连接去关联化。  


**解答**：  
原查询是相关子查询，条件为“$r.B$ 小于 $s$ 中与 $r.A$ 匹配的任意 $B$ 值”。  

去关联化步骤：  
1. **提取子查询关联条件**：$s.A = r.A$，子查询目标是 $s.B$。  
2. **转换为半连接**：用 multiset 半连接找到 $r$ 中满足“存在 $s$ 元组（$s.A = r.A$ 且 $r.B < s.B$）”的元组。  

**关系代数表达式**：  
$$\Pi_{A, B} \left( r \ltimes_{\exists s.B \ (r.A = s.A \land r.B < s.B)} s \right)$$  

等价于：  
$$\Pi_{A, B} \left( r \bowtie_{r.A = s.A \land r.B < s.B} s \right)$$  

（注：multiset 半连接保留 $r$ 的元组，只要存在至少一个匹配的 $s$ 元组满足条件。）  


#### **16.23 增量维护操作结果**  
**题目**：描述如何增量维护以下操作的插入和删除：  
- **a.** Union 和 Set Difference  
- **b.** Left Outer Join  


**解答 a：Union（$r \cup s$）**  
- **插入**：  
  - 向 $r$ 插入 $\Delta r$：直接添加到结果（$\Delta (r \cup s) = \Delta r$，需去重，若为 multiset 则直接累加）。  
  - 向 $s$ 插入 $\Delta s$：直接添加到结果（$\Delta (r \cup s) = \Delta s$，需去重）。  

- **删除**：  
  - 从 $r$ 删除 $\Delta r$：从结果中删除（需确保 $\Delta r$ 不在 $s$ 中，否则保留；若为 multiset 则减少计数）。  
  - 从 $s$ 删除 $\Delta s$：从结果中删除（需确保 $\Delta s$ 不在 $r$ 中，否则保留；若为 multiset 则减少计数）。  


**Set Difference（$r - s$）**  
- **插入**：  
  - 向 $r$ 插入 $\Delta r$：添加到结果（需排除 $\Delta r$ 中同时在 $s$ 的元组）。  
  - 向 $s$ 插入 $\Delta s$：从结果中删除 $\Delta s$ 中存在的元组。  

- **删除**：  
  - 从 $r$ 删除 $\Delta r$：从结果中删除 $\Delta r$。  
  - 从 $s$ 删除 $\Delta s$：将 $\Delta s$ 中存在于 $r$ 但不在当前结果的元组，添加到结果。  


**解答 b：Left Outer Join（$r \ltimes s$）**  
- **插入**：  
  - 向 $r$ 插入 $\Delta r$：新元组直接与 $s$ 连接（无匹配则保留 $r$ 元组 + 空值），添加到结果。  
  - 向 $s$ 插入 $\Delta s$：对 $r$ 中所有与 $\Delta s$ 匹配的元组（$r.A = s.A$），更新连接结果（添加新匹配的 $s$ 元组）。  

- **删除**：  
  - 从 $r$ 删除 $\Delta r$：从结果中删除 $\Delta r$ 的所有连接元组。  
  - 从 $s$ 删除 $\Delta s$：对 $r$ 中与 $\Delta s$ 匹配的元组，若删除后无其他 $s$ 元组匹配，则保留 $r$ 元组 + 空值；否则更新连接结果（移除被删除的 $s$ 元组）。  


#### **16.24 物化视图增量维护 vs 重计算**  
**题目**：举示例说明物化视图增量维护更优和重计算更优的场景。  


**示例**：  
物化视图定义：`MV = \Gamma_{A, \text{sum}(B)}(r)`（按 $A$ 分组求和）。  

- **场景 1：增量维护更优**  
  - 统计信息：$r$ 有 100 万行，每次插入 100 行。  
  - 差异（Differential）：仅需更新对应分组的 `sum(B)`（加法操作）。  
  - 对比：重计算需全表扫描 100 万行，增量维护仅需处理 100 行，成本更低。  


- **场景 2：重计算更优**  
  - 统计信息：$r$ 有 1000 行，每次删除 500 行且涉及所有分组。  
  - 差异：需遍历 500 行，更新每个分组的 `sum(B)`（减法操作），且可能删除分组。  
  - 对比：重计算需扫描 1000 行，增量维护需处理 500 行的复杂逻辑，总成本可能更高（尤其分组多、删除分散时）。  


#### **16.25 Top-K 连接查询优化**  
**题目**：查询 $r \bowtie s$ 需按 $r$ 的属性排序取前 $K$ 结果，分两种场景优化：  
- **a.** 连接键是 $r$ 的外键（非空）。  
- **b.** 连接键不是外键。  


**解答 a（外键，非空）**：  
因外键非空，$r$ 中每个元组必然匹配 $s$ 的元组。优化方法：  
1. 对 $r$ 按排序属性建索引（如 B⁺-tree）。  
2. 按索引顺序扫描 $r$ 的前 $K$ 候选元组，逐行与 $s$ 连接（利用 $s$ 的索引快速查找匹配元组）。  
3. 收集前 $K$ 个连接结果，提前终止扫描。  


**解答 b（非外键）**：  
连接可能存在不匹配的元组，优化方法：  
1. 对 $r$ 按排序属性建索引，同时对 $s$ 的连接键建索引。  
2. 按索引顺序扫描 $r$，对每个 $r$ 元组，用 $s$ 的索引查找匹配元组：  
   - 若找到，保留连接结果。  
   - 若未找到，跳过（左外连接需保留 $r$ 元组 + 空值，内连接则跳过）。  
3. 收集前 $K$ 个有效结果，提前终止扫描。  


#### **16.26 索引-only 查询计划**  
**题目**：对关系 $r(A, B, C)$（$A$ 上有索引），举示例查询可仅用索引回答（无需访问关系元组）。  


**示例**：  
查询：`select count(A) from r where A > 10`  

- 索引结构： $A$  上的 B⁺-tree 


  
