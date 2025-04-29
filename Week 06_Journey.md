本周阅读**Database-System-Concepts-7th-Edition**:PART TWO DATABASE DESIGN,知识点总结如下：  
**Chapter 7 Relational Database Design**(p303-360)  
### 7.1.1 Decomposition（分解）
- **分解目的**：避免模式（如 in_dep 模式 ）中信息重复问题，可将其分解为更小模式（如 instructor 和 department 模式）。但并非所有分解都有益，如将模式分解到仅含一个属性，会无法表达有趣关系。  
- **示例分析**：以 employee 模式（employee (ID, name, street, city, salary) ）为例，将其分解为 employee1 (ID, name) 和 employee2 (name, street, city, salary) 。若企业存在同名员工（如两个叫 Kim 的员工 ），这种分解会在自然连接时产生错误混合数据的新元组，导致无法区分某些数据对应哪个员工，虽元组增多但实际信息减少，此为有损分解（lossy decomposition） 。  
### 7.1.2 Lossless Decomposition（无损分解）
- **定义**：设 $R$ 为关系模式， $R_1$  和 $R_2$ 构成 $R$ 的分解（即 $R = R_1 \cup R_2$ ）。若用 $R_1$ 和 $R_2$ 替换 $R$ 时无信息丢失，则该分解为无损分解。具体来说，对于所有合法数据库实例，关系 $r$ 与通过 “select * from (select $R_1$ from $r$) natural join (select $R_2$ from $r$)” 查询结果包含相同元组集，用关系代数表示为 $\Pi_{R_1}(r) \bowtie \Pi_{R_2}(r) = r$。
- **有损分解判断**：若计算投影结果的自然连接得到的是原始关系的真超集，则为有损分解，用关系代数表示为 $r \subset \Pi_{R_1}(r) \bowtie \Pi_{R_2}(r)$。像前面 employee 模式分解为 employee1 和 employee2 的例子，连接结果是原始关系超集且丢失员工标识符对应地址和薪资信息，属于有损分解。  
### 函数依赖的用途
- **检验关系实例**：用于测试关系实例是否满足给定的函数依赖集F。
- **指定合法关系约束**：限定合法关系集，仅关注满足给定函数依赖集的关系实例。若关系模式r(R)满足函数依赖集F ，称F在r(R)上成立。

### 函数依赖的判定示例
- 以关系r的实例（图7.4）为例，判断函数依赖是否满足。如 $A \to C$ 满足，因为A值相同的元组C值也相同；而 $C \to A$ 不满足，能找到C值相同但A值不同的元组对。
- 平凡函数依赖：如 $A \to A$ 、 $AB \to A$  ，一般形式为 $\alpha \to \beta$ ，当 $\beta \subseteq \alpha$ 时是平凡函数依赖，能被所有相关关系满足。
- 实际场景考量：像教室关系（图7.5）中，从现实看“room_number $\to$ capacity”可能不总是满足，不同楼里可能有相同房间号但容量不同的教室，所以该函数依赖不纳入教室关系模式的函数依赖集；但“building, room_number $\to$ capacity”是期望成立的。

### 函数依赖的推导
已知一组函数依赖集F在关系r(R)上成立，可能推导出其他函数依赖也成立。例如模式r(A, B, C) ，若 $A \to B$ 和 $B \to C$ 成立，可推出 $A \to C$  。用 $F^+$ 表示F的闭包，即由F可推导出的所有函数依赖的集合， $F^+$ 包含F中的所有函数依赖。

### 无损分解与函数依赖
判断关系R分解为 $R_1$ 和 $R_2$ 是否无损：若 $R_1 \cap R_2 \to R_1$ 或 $R_1 \cap R_2 \to R_2$ 在 $F^+$ 中，即 $R_1 \cap R_2$ 构成 $R_1$ 或 $R_2$ 的超键时，R的分解是无损分解。可通过属性闭包高效测试超键。如in_dep模式分解为instructor和department模式，二者交集dept_name相关情况可用于判断分解是否无损。

### 规范化理论与函数依赖
- **规范化**：设计关系数据库常用方法，目标是生成无不必要冗余、便于信息检索的关系模式集。步骤包括判断关系模式是否为“良好形式”（即属于不同范式，后续章节介绍），若不是则将其分解为合适范式的小关系模式，且必须是无损分解。
- **现实约束建模**：数据库模拟现实世界实体和关系，有多种数据约束。如大学数据库中学生和教师由ID唯一标识、每人只有一个名字、每人主要关联一个部门、每个部门预算和关联建筑唯一等。满足这些现实约束的关系实例为合法实例，数据库的合法实例要求所有关系实例都是合法的。

### 符号约定
- 希腊字母表示属性集（如 $\alpha$ ），大写罗马字母表示关系模式（如R），r(R)表示关系r及其模式R。
- 超键用K表示，称“K是R的超键”。小写字母表示关系（如r），关系在某时刻的值为实例，可简称关系名表示实例。

### 键与函数依赖
- **超键定义**：关系r(R)中，R的子集K若能在任何合法实例中使不同元组的K值不同，即 $t_1 \neq t_2$ 时 $t_1[K] \neq t_2[K]$   ，则K是r(R)的超键，可唯一标识元组。
- **函数依赖定义**：给定关系模式r(R) ， $\alpha \subseteq R$  ， $\beta \subseteq R$  ，若关系实例中  $t_1[\alpha] = t_2[\alpha]$ 时必有 $t_1[\beta] = t_2[\beta]$   ，称实例满足函数依赖 $\alpha \to \beta$  ；若r(R)的每个合法实例都满足，称函数依赖 $\alpha \to \beta$ 在r(R)上成立。当函数依赖 $K \to R$ 在r(R)上成立时，K是r(R)的超键 。函数依赖可表达超键无法表达的约束，如in_dep模式中 $dept\_name \to budget$ 成立，(ID, dept_name)构成超键可表示为 $ID, dept\_name \to name, salary, building, budget$  。

### Boyce - Codd范式（BCNF）
- **定义**：对于关系模式R和函数依赖集F，若 $F^+$ 中所有形如 $\alpha \to \beta$ （ $\alpha \subseteq R$ ， $\beta \subseteq R$ ）的函数依赖，至少满足以下条件之一：
    - $\alpha \to \beta$是平凡函数依赖（即 $\beta \subseteq \alpha$  ）；
    - $\alpha$是模式R的超键。当构成数据库设计的每个关系模式都属于BCNF时，该数据库设计处于BCNF。
- **示例**：如in_dep (ID, name, salary, dept_name, building, budget)模式， $dept\_name \to budget$ 成立，但dept_name不是超键，所以in_dep不在BCNF。将其分解为instructor (ID, name, dept_name, salary)和department (dept_name, building, budget)，instructor模式中 $ID \to name, dept\_name, salary$ ，ID是超键；department模式中 $dept\_name \to building, budget$ ，dept_name是超键，二者都属于BCNF。 
- **分解规则**：若模式R不属于BCNF，存在至少一个非平凡函数依赖 $\alpha \to \beta$ ，且 $\alpha$ 不是R的超键，此时用 $(\alpha \cup \beta)$ 和 $(R - (\beta - \alpha))$ 两个模式替换R。如in_dep模式中， $\alpha = dept\_name$ ， $\beta = \{building, budget\}$ ，替换后的模式为(dept_name, building, budget)和(ID, name, dept_name, salary)。 
- **BCNF与依赖保持**：分解为BCNF有时会影响函数依赖的高效检验。以大学组织关系的dept_advisor模式为例，其存在函数依赖 $i\_ID \to dept\_name$ 、 $s\_ID, dept\_name \to i\_ID$ ，因i_ID不是超键，不属于BCNF。分解为(s_ID, i_ID)和(i_ID, dept_name)后，虽都属于BCNF，但函数依赖 $s\_ID, dept\_name \to i\_ID$ 无法在单个分解后的关系中检验，需通过连接操作，这种设计不具备依赖保持特性。

### 第三范式（3NF）
- **定义**：关系模式R对于函数依赖集F属于第三范式，需满足对于 $F^+$ 中所有形如  $\alpha \to \beta$ （ $\alpha \subseteq R$  ，  $\beta \subseteq R$  ）的函数依赖，至少满足以下条件之一：
    - $\alpha \to \beta$是平凡函数依赖；
    - $\alpha$是R的超键；
    - $\beta - \alpha$中的每个属性A都包含在R的候选键中（不要求单个候选键包含 $\beta - \alpha$ 中的所有属性，每个属性可在不同候选键中 ）。
- **与BCNF对比**：BCNF要求所有非平凡依赖的左边是超键，3NF对此约束稍作放宽，允许某些左边不是超键的非平凡函数依赖。其第三个条件一定程度上是对BCNF条件的最小松弛，有助于确保每个模式都有保持依赖的3NF分解。

### 函数依赖集的闭包
- **逻辑蕴含**：给定关系模式r(R)和函数依赖集F，若满足F的关系r(R)的每个实例也满足函数依赖f，则称f被F逻辑蕴含。例如，对于关系模式r(A, B, C, G, H, I)及函数依赖集{ $A \to B$ ,  $A \to C$ ,  $CG \to H$ ,  $CG \to I$ ,  $B \to H$ }， $A \to H$ 被该函数依赖集逻辑蕴含。通过设元组 $t_1$ 和 $t_2$ ，利用已知函数依赖逐步推导，可证明当 $t_1[A] = t_2[A]$ 时， $t_1[H] = t_2[H]$ ，符合 $A \to H$ 定义。
- **闭包定义**：函数依赖集F的闭包 $F^+$ ，是被F逻辑蕴含的所有函数依赖的集合。可根据函数依赖的形式定义直接计算 $F^+$ ，但当F较大时此过程冗长困难。
- **Armstrong公理**：包括自反律（若 $\beta \subseteq \alpha$ ，则 $\alpha \to \beta$ 成立 ）、增广律（若 $\alpha \to \beta$ 成立， $\gamma$ 为属性集，则 $\gamma\alpha \to \gamma\beta$ 成立 ）、传递律（若 $\alpha \to \beta$ 成立且 $\beta \to \gamma$ 成立，则 $\alpha \to \gamma$ 成立 ）。这些公理是健全的（不会产生错误函数依赖）且完备的（能生成所有 $F^+$  ）。此外，还有联合规则（若 $\alpha \to \beta$ 和 
 $\alpha \to \gamma$ 成立，则 $\alpha \to \beta\gamma$ 成立 ）、分解规则（若 $\alpha \to \beta\gamma$ 成立，则 $\alpha \to \beta$ 和 $\alpha \to \gamma$ 成立 ）、伪传递规则（若 $\alpha \to \beta$ 和 $\gamma\beta \to \delta$ 成立，则 $\alpha\gamma \to \delta$ 成立 ）。利用这些规则可更简便地找出 $F^+$ ，如通过传递规则由 $A \to B$和$B \to H$ 推出 $A \to H$ ；通过联合规则由 $CG \to H$ 和 $CG \to I$ 推出 $CG \to HI$ ；通过伪传递规则由 $A \to C$ 和 $CG \to I$ 推出 $AG \to I$ 。还给出了计算 $F^+$ 的过程，即先应用自反律生成所有平凡依赖，然后对 $F^+$ 中每个函数依赖应用增广律并添加结果，对每对函数依赖尝试应用传递律合并并添加结果，直至 $F^+$ 不再变化。

### BCNF和3NF比较
- **范式关系**：满足BCNF的模式也满足3NF，因为BCNF的函数依赖条件更严格，其函数依赖满足3NF定义的前两种情况之一，所以BCNF是比3NF更具限制性的范式 。
- **3NF特点**：3NF允许一些BCNF不允许的函数依赖。如dept_advisor关系模式，函数依赖 $i\_ID \to dept\_name$ 导致其不属于BCNF，但因 $dept\_name$ 包含在候选键中（基于函数依赖 $s\_ID, dept\_name \to i\_ID$  ），所以属于3NF。
- **设计目标权衡**：数据库设计目标包括BCNF、无损性和依赖保持性。3NF的优势在于总能在不牺牲无损性和依赖保持性的前提下获得设计方案；缺点是可能需用空值表示数据间关系，存在信息重复问题 。实际中因难以同时满足三个目标，常需在BCNF和3NF的依赖保持性间权衡。且SQL除通过主键或唯一约束声明超键外，难以指定函数依赖，即使有保持依赖的分解，用标准SQL也只能高效检验左边是键的函数依赖 。虽可通过物化视图降低非依赖保持性分解下检验函数依赖的成本，但多数数据库系统对物化视图约束有限或不支持，即便支持也需在更新底层关系时立即更新视图并检查约束 。总体而言，即使无法得到保持依赖的BCNF分解，仍倾向选择BCNF，因在SQL中检查非主键约束的函数依赖较困难。

### 更高范式
仅用函数依赖分解模式在某些情况下不足以避免信息不必要重复。以instructor实体集定义的变体为例，记录教师的孩子姓名集和可能多人共用的固定电话号码集，phone_number和child_name为多值属性，按E - R设计生成模式规则会得到(ID, child_name)和(ID, phone_number)。若将其合并为(ID, child_name, phone_number)，虽属于BCNF（无非平凡函数依赖），但会出现信息重复问题，如教师ID为99999，有两个孩子David和William，两个电话号码512 - 555 - 1234和512 - 555 - 4321，合并模式下每个孩子对应电话号码都需重复记录 。若不重复记录则会导致对应关系错误。由于基于函数依赖的范式不足以处理此类情况，需定义其他依赖和范式（在7.6和7.7节介绍）。

### 属性集闭包（Closure of Attribute Sets）
- **定义与作用**：若 $\alpha \to B$ ，则称属性B被 $\alpha$ 函数确定。属性集 $\alpha$ 在函数依赖集F下，所有被 $\alpha$ 函数确定的属性集合称为 $\alpha$ 在F下的闭包，记为 $\alpha^+$ 。计算 $\alpha^+$ 可用于测试 $\alpha$ 是否为超键（若 $\alpha^+$ 包含关系模式R中的所有属性，则 $\alpha$ 是超键 ）；检查函数依赖 $\alpha \to \beta$ 是否成立（通过检查 $\beta \subseteq \alpha^+$ ，若成立则函数依赖在 $F^+$ 中 ）；还可作为计算 $F^+$ 的一种替代方式（对每个 $\gamma \subseteq R$ ，求 $\gamma^+$ ，并对每个 $S \subseteq \gamma^+$ ，输出函数依赖 $\gamma \to S$  ）。 
- **计算算法**：输入函数依赖集F和属性集 $\alpha$ ，输出存储在变量result中。初始令result =  $\alpha$ ，然后重复遍历F中的每个函数依赖 $\beta \to \gamma$ ，若 $\beta \subseteq result$ ，则将 $\gamma$ 并入result，直到result不再变化。例如，计算 $(AG)^+$ ，初始result = AG，根据 $A \to B$ ，因 $A \subseteq AG$ ，将B加入result得ABG；再由 $A \to C$ ，得ABCG；由 $CG \to H$ ，得ABCGH；由 $CG \to I$ ，得ABCCHI，再次遍历无新属性加入，算法结束。该算法在最坏情况下时间复杂度为F规模的二次方，也存在时间复杂度为F规模线性的更快算法。

### 正则覆盖（Canonical Cover）
- **无关属性（Extraneous Attributes）**：对于函数依赖集F和其中的函数依赖 $\alpha \to \beta$ ：
    - **左边属性移除**：若 $A \in \alpha$ ，且F逻辑蕴含 $(F - \{\alpha \to \beta\}) \cup \{(\alpha - A) \to \beta\}$ ，则属性A在 $\alpha$ 中是无关的。例如，对于 $F = \{AB \to C, A \to D, D \to C\}$ ，可推出 $A \to C$ ，说明 $AB \to C$ 中B是无关的。
    - **右边属性移除**：若 $A \in \beta$ ，且函数依赖集 $(F - \{\alpha \to \beta\}) \cup \{\alpha \to (\beta - A)\}$ 逻辑蕴含F，则属性A在 $\beta$ 中是无关的。如 $F = \{AB \to CD, A \to C\}$ ，将 $AB \to CD$ 替换为 $AB \to D$ 后仍能推出 $AB \to C$ 和 $AB \to CD$ ，此时C在 $AB \to CD$ 的右边是无关的。可通过计算属性集闭包来高效测试属性是否无关，若 $A \in \beta$ ，构造 $F' = (F - \{\alpha \to \beta\}) \cup \{\alpha \to (\beta - A)\}$ ，计算 $F'$下$\alpha^+$ ，若包含A，则A在 $\beta$ 中无关；若 $A \in \alpha$ ，令 $\gamma = \alpha - \{A\}$ ，计算F下 $\gamma^+$ ，若包含 $\beta$ 中所有属性，则A在 $\alpha$ 中无关。
- **正则覆盖定义与计算**：F的正则覆盖 $F_c$ 是满足F逻辑蕴含 $F_c$ 中所有依赖，且 $F_c$ 逻辑蕴含F中所有依赖的函数依赖集，同时 $F_c$ 具有无关属性、每个函数依赖左边唯一的性质 。计算方法为：令 $F_c = F$ ，重复使用联合规则合并左边相同的函数依赖（如将 $\alpha_1 \to \beta_1$ 和 $\alpha_1 \to \beta_2$ 合并为 $\alpha_1 \to \beta_1\beta_2$  ），查找 $F_c$ 中 $\alpha \to \beta$ 里 $\alpha$ 或 $\beta$ 存在无关属性的函数依赖，若找到则删除该无关属性，直到 $F_c$ 不再变化 。例如，对于 $F = \{A \to BC, B \to C, A \to B, AB \to C\}$ ，先合并 $A \to BC$ 和 $A \to B$ 为 $A \to BC$ ；因 $B \to C$ 在F中，所以 $AB \to C$ 中A是无关的；又因 $A \to B$ 和 $B \to C$ 可推出 $A \to BC$ ，所以 $A \to BC$ 中C是无关的，最终正则覆盖为 $\{A \to B, B \to C\}$ 。给定的F可能存在多个正则覆盖，它们都与F具有相同闭包，测试 $F_c$ 是否满足与测试F是否满足等价，且 $F_c$ 更便于测试。

### BCNF分解
- **BCNF测试**：
    - 检查非平凡依赖 $\alpha \to \beta$ 是否违反BCNF，可计算 $\alpha$ 的属性闭包 $\alpha^+$ ，若 $\alpha^+$ 包含关系模式R的所有属性（即 $\alpha$ 是R的超键），则不违反BCNF。
    - 检查关系模式R是否满足BCNF，只需检查给定函数依赖集F中的依赖是否违反BCNF，若F中没有依赖违反BCNF，则 $F^+$ 中也不会有依赖违反BCNF。但在关系模式分解后，仅检查F是不够的 。例如，关系模式(A, B, C, D, E)， $F = \{A \to B, BC \to D\}$ ，分解为(A, B)和(A, C, D, E)，F中的依赖都不包含(A, C, D, E)的所有属性，但 $F^+$ 中有 $AC \to D$ ，说明(A, C, D, E)不满足BCNF。此时可采用替代测试方法：对于分解后的关系模式 $R_i$ 中的每个属性子集 $\alpha$ ，检查 $\alpha$ 在F下的属性闭包 $\alpha^+$ ，要么不包含 $R_i - \alpha$ 的属性，要么包含 $R_i$ 的所有属性 。若 $R_i$ 中某些属性集 $\alpha$ 违反此条件，则函数依赖 $\alpha \to (\alpha^+ - \alpha) \cap R_i$ 表明 $R_i$ 违反BCNF。
- **BCNF分解算法**：初始令result = {R}，done = false。当存在result中的模式 $R_i$ 不满足BCNF时，找到 $R_i$ 上的非平凡函数依赖 $\alpha \to \beta$ ，满足 $\alpha^+$ 不包含 $R_i$ 且 $\alpha \cap \beta = \varnothing$ ，然后将result更新为 $(result - R_i) \cup (R_i - \beta) \cup (\alpha, \beta)$ ，否则done = true 。该算法生成的分解不仅满足BCNF，还是无损分解。因为当用 $(R_i - \beta)$和$(\alpha, \beta)$ 替换 $R_i$ 时，依赖 $\alpha \to \beta$ 成立，且 $(R_i - \beta) \cap (\alpha, \beta) = \alpha$  。但该算法时间复杂度为初始模式规模的指数级，也存在多项式时间的BCNF分解算法，不过可能会过度规范化。

### 依赖保持（Dependency Preservation）
- **定义与测试算法**：设F是模式R上的函数依赖集， $R_1, R_2, \ldots, R_n$ 是R的分解。F在 $R_i$ 上的限制 $F_i$ 是 $F^+$ 中仅包含 $R_i$ 属性的所有函数依赖集 。 $F' = F_1 \cup F_2 \cup \ldots \cup F_n$ ，若 $F'^+ = F^+$ ，则该分解是依赖保持分解 。图7.10给出了测试依赖保持性的算法，输入分解后的关系模式集D和函数依赖集F，该算法需计算 $F^+$ ，成本较高。
- **替代测试方法**：
    - 若F中的每个依赖都能在分解后的某个关系上进行测试，则该分解是依赖保持的，但这只是充分条件，不满足时不能得出不是依赖保持分解的结论 。
    - 另一种避免计算 $F^+$ 的测试方法：对F中的每个 $\alpha \to \beta$ ，初始result =  $\alpha$ ，重复对分解中的每个 $R_i$ ，计算 $t = (result \cap R_i)^+ \cap R_i$ ，并将result更新为result  $\cup t$ ，直到result不再变化 。若result包含 $\beta$ 中的所有属性，则函数依赖 $\alpha \to \beta$ 被保留，当且仅当F中的所有依赖都被保留时，该分解是依赖保持的 。此测试方法基于两个关键思想：一是测试F中的每个函数依赖 $\alpha \to \beta$ 是否在 $F'$ 中被保留，通过计算 $\alpha$ 在 $F'$ 下的闭包，当闭包包含 $\beta$ 时依赖被保留；二是使用修改后的属性闭包算法计算 $F'$ 下的闭包，避免直接计算 $F'$ ，通过计算 $(result \cap R_i)$ 在F下的闭包，与 $R_i$ 相交后将结果属性集加入result，重复此步骤得到 $F'$ 下result的闭包。   

### 3NF分解
- **3NF分解算法**：图7.12展示了将关系模式分解为满足依赖保持性和无损连接性的3NF算法。该算法使用函数依赖集F的正则覆盖 $F_c$  。初始 $i = 0$ ，对于 $F_c$ 中的每个函数依赖 $\alpha \to \beta$ ，令 $i = i + 1$ ， 
 $R_i = \alpha\beta$ ；若此时的模式集 $R_j$ （ $j = 1, 2, \ldots, i$ ）中没有包含R的候选键的模式，则令 $i = i + 1$ ， $R_i$ 为R的任意候选键 。之后可选地删除冗余模式（若模式 $R_j$ 被另一个模式 $R_k$ 包含，则删除 $R_j$ ），直到不能再删除模式，最后返回模式集( $R_1, R_2, \ldots, R_i$ ) 。例如，对于 $dept\_advisor(s\_ID, i\_ID, dept\_name)$ ，其函数依赖 $f_1: i\_ID \to dept\_name$ ， $f_2: s\_ID, dept\_name \to i\_ID$ ， $F_c$ 包含 $f_1$ 和 $f_2$ ，算法生成模式 $R_1(i\_ID, dept\_name)$ 和 $R_2(s\_ID, dept\_name, i\_ID)$ ，因 $R_2$ 包含候选键，不再创建新关系模式。
- **3NF算法正确性**：3NF算法通过为正则覆盖中的每个依赖显式构建模式来确保依赖保持性 。通过保证至少有一个模式包含被分解模式的候选键来确保无损连接性 。该算法也叫3NF综合算法，它每次添加一个模式，而不是反复分解初始模式，结果不唯一（因函数依赖集可能有多个正则覆盖 ），即便关系模式已是3NF也可能被分解，但分解结果仍保证是3NF。
- **3NF与BCNF关系**：有时3NF算法得到的结果不仅是3NF，也是BCNF 。可先使用3NF算法，若3NF设计中的模式不满足BCNF，再用BCNF算法分解，若结果不满足依赖保持性，则恢复到3NF设计 。如对class关系模式应用3NF分解算法，得到的结果与BCNF分解相同（course, classroom, section ），且该分解是无损且依赖保持的。

### 多值依赖（Multivalued Dependencies）
- **多值依赖引入**：有些关系模式虽处于BCNF，但仍存在信息重复问题，如inst (ID, dept_name, name, street, city)，即便按BCNF分解后仍有冗余 。为解决此类问题，需定义新的约束——多值依赖，并基于多值依赖定义比BCNF更严格的范式——第四范式（4NF），每个4NF模式都属于BCNF，但存在不属于4NF的BCNF模式。
- **多值依赖与函数依赖区别**：函数依赖排除某些元组在关系中存在（如 $A \to B$ ，不允许A值相同但B值不同的元组存在 ），属于等式生成依赖；多值依赖不排除某些元组存在，而是要求关系中存在特定形式的其他元组，属于元组生成依赖。
- **定义与判定**：设 $r(R)$ 为关系模式，D为函数依赖和多值依赖集。多值依赖 $\alpha \twoheadrightarrow \beta$ 成立条件为，在关系 $r(R)$ 的任何合法实例中，对于任意元组对 $t_1$ 和 $t_2$ ，若 $t_1[\alpha] = t_2[\alpha]$ ，则存在元组 $t_3$ 和 $t_4$ ，满足 $t_1[\alpha] = t_2[\alpha] = t_3[\alpha] = t_4[\alpha]$ ， $t_3[\beta] = t_1[\beta]$ ， $t_3[R - \beta] = t_2[R - \beta]$ ， $t_4[\beta] = t_2[\beta]$  ， $t_4[R - \beta] = t_1[R - \beta]$  。直观上， $\alpha \twoheadrightarrow \beta$ 表示 $\alpha$ 与 $\beta$ 的关系独立于 $\alpha$ 与 $R - \beta$ 的关系 。若 $\beta \subseteq \alpha$ 或 $\beta \cup \alpha = R$ ，则 $\alpha \twoheadrightarrow \beta$ 是平凡多值依赖 。函数依赖 $\alpha \to \beta$ 蕴含多值依赖 $\alpha \twoheadrightarrow \beta$ ，且若 $\alpha \twoheadrightarrow \beta$ 成立，则 $\alpha \twoheadrightarrow R - \alpha - \beta$ 也成立。
- **作用**：用于测试关系在给定函数依赖和多值依赖集下是否合法；用于指定合法关系集的约束 。若关系r不满足给定多值依赖，可通过添加元组构造满足该依赖的关系 $r'$  。可从给定的D计算其闭包 $D^+$ （包含所有由D逻辑蕴含的函数依赖和多值依赖 ）。

### 第四范式（4NF）
- **定义**：关系模式R对于函数依赖和多值依赖集D处于第四范式（4NF），需满足对于 $D^+$ 中所有形式为 $\alpha \twoheadrightarrow \beta$ （ $\alpha \subseteq R$ 且 $\beta \subseteq R$  ）的多值依赖，要么 $\alpha \twoheadrightarrow \beta$ 是平凡多值依赖，要么 $\alpha$ 是R的超键 。每个4NF模式都属于BCNF，若模式R不属于BCNF（存在非平凡函数依赖 $\alpha \to \beta$ 且 $\alpha$ 不是超键 ），则也不属于4NF。
- **4NF分解算法**：与BCNF分解算法类似（图7.16），初始令result = {R}，done = false，计算 $D^+$ ，对于模式 $R_i$ ，令 $D_i$ 为 $D^+$ 在 $R_i$ 上的限制 。当存在result中的模式 $R_i$ 不满足4NF时，找到 $R_i$ 上的非平凡多值依赖 $\alpha \twoheadrightarrow \beta$ ，满足 $\alpha \to R_i$ 不在 $D_i$ 中且 $\alpha \cap \beta = \varnothing$ ，将result更新为 $(result - R_i) \cup (R_i - \beta) \cup (\alpha, \beta)$ ，否则done = true 。该算法生成的分解是无损的，因为关系模式R分解为 $r_1(R_1)$和$r_2(R_2)$ 时，当且仅当 $D^+$ 中至少有 $R_1 \cap R_2 \to R_1$ 或 $R_1 \cap R_2 \to R_2$ 成立，分解才是无损的 。但存在多值依赖时，依赖保持性问题更复杂，还可能出现嵌入多值依赖（只在给定模式的真子集上成立，在给定模式上无法表达 ），不过这种情况很少见。

### 更高级范式
- **连接依赖与投影连接范式（PJNF）**：连接依赖是多值依赖的推广，基于连接依赖定义了投影连接范式（PJNF ），也叫第五范式。
- **域 - 键范式（DKNF）**：存在更一般的约束，由此定义了域 - 键范式（DKNF ）。但这些广义约束难以推理，且缺乏健全完整的推理规则，所以PJNF和DKNF很少使用。

### 第一范式（1NF）
- **原子域**：在关系模型中，若域中的元素被视为不可分割的单元，则该域是原子的 。例如整数域通常被认为是原子的，但整数集的集合域是非原子的 。关键在于数据库中对域元素的使用方式，若将整数视为有序列表，整数域就可能是非原子的。
- **1NF定义**：若关系模式R的所有属性的域都是原子的，则R处于第一范式（1NF） 。E - R模型中存在多值属性和复合属性，创建关系表时需消除这种子结构，将复合属性的每个组件作为独立属性，将多值属性的每个元素作为元组 。如关系模式中若有属性children，其域元素是名字集合，则该模式不满足1NF。

### E - R模型与规范化（E - R Model and Normalization）
- **E - R模型转换关系模式**：仔细定义E - R图并正确识别所有实体集时，从E - R图生成的关系模式通常无需太多进一步规范化 。但实体集属性间可能存在函数依赖，如instructor实体集若有dept_name和dept_address属性，且存在 $dept\_name \to dept\_address$ 函数依赖，就需对从instructor生成的关系进行规范化 。多数此类依赖源于E - R图设计不佳，如设计正确应创建department实体集及instructor与department间关系集 。涉及两个以上实体集的关系集可能导致模式未达理想范式，但因多数关系集是二元的，这种情况相对少见 。函数依赖可帮助检测不良E - R设计，若生成的关系模式未达理想范式，可在E - R图中修正，即规范化可作为数据建模的一部分正式进行，也可在E - R建模时靠设计者直觉处理，之后在从E - R模型生成的关系模式上正式进行 。创建E - R设计的过程倾向于生成4NF设计，若存在多值依赖且不由对应函数依赖蕴含，通常源于多对多关系集或实体集的多值属性 。对于多对多关系集，每个相关实体集有自己的模式，关系集还有额外模式；对于多值属性，会创建包含该属性和实体集主键的单独模式（如instructor实体集的phone_number属性 ）。

### 属性和关系命名（Naming of Attributes and Relationships）
- **唯一角色假设**：数据库设计的理想特性是唯一角色假设，即每个属性名在数据库中有唯一含义，避免同一属性在不同模式中有不同含义，如用number表示instructor模式中的电话号码和classroom模式中的房间号会使关系连接无意义，不同属性用不同名可减少用户错误 。若不同关系的属性含义相同，使用相同属性名较好，如instructor和student实体集都用“name”属性，若预见可能将其泛化为person实体集，这样可避免重命名属性 。模式中属性名顺序虽技术上无关紧要，但习惯先列出主键属性，便于读取默认输出（如select * ）。
- **关系集命名**：大型数据库模式中，关系集（及从中派生的模式）常通过相关实体集名称连接命名，可能用连字符或下划线，也可使用更简短易记的名称，如teaches和takes 。不能总是简单连接命名，如员工间manager或works - for关系叫employee_employee不合适；若一对实体集间可能有多组关系集，关系集名须包含额外部分以区分 。不同组织对实体集命名有不同惯例，用单数或复数形式皆可，只要在所有实体集中保持一致。

### 性能反规范化（Denormalization for Performance）
- **反规范化概念**：有时数据库设计者选择有冗余信息（未规范化）的模式，利用冗余提高特定应用性能，代价是需额外工作（编码时间和执行时间）保持冗余数据一致 。例如，每次访问课程信息都要显示所有先修课程，规范化模式需连接course和prereq关系，一种替代方法是存储包含course和prereq所有属性的关系，可加快显示“完整”课程信息速度，但课程先修信息有更新时，应用需更新所有副本 。将规范化模式变为非规范化模式的过程叫反规范化，设计者用其优化系统性能以支持对时间要求高的操作 。更好的替代方法是使用规范化模式并将course和prereq的连接作为物化视图存储，虽也有空间和时间开销，但更新视图由数据库系统负责，而非应用程序员。

### 其他设计问题（Other Design Issues）
- **时间相关数据设计**：与时间或时间范围相关的数据设计可能存在问题，应避免不良设计 。如存储各系不同年份教师总数，可设计relation total_inst (dept_name, year, size)，其函数依赖为 $dept\_name, year \to size$ ，该关系在BCNF；另一种设计是用多个关系分别存储不同年份信息，如total_inst_2017、total_inst_2018、total_inst_2019，模式为(dept_name, size)，函数依赖为 $dept\_name \to size$ ，也在BCNF，但这种设计每年都要创建新关系和编写新查询，不是好方案。
- **原子域与非原子域**：若关系模式属性的域由可分割的标识号组成（如前两位表示部门，后四位是部门内唯一编号的员工标识号 ），则该模式不满足第一范式，使用此类标识号需编写代码解析结构，信息编码在应用程序而非数据库中，若用作主键，员工换部门时更改困难 。对于课程标识符（如“CS - 101”），只要数据库应用不拆分解释，仍可将其域视为原子的，大学模式可认为满足第一范式 。使用集合值属性可能导致数据冗余存储和不一致，如表示教师与课程节次关系，不使用单独的teaches关系，而在每个教师记录存储课程节次标识符集合，在每个课程节次记录存储教师标识符集合，数据更新时需在两处操作，否则数据库会不一致 。某些非原子值（如复合值属性、集合值属性 ）有用但需谨慎使用，在实体结构复杂的领域，强制第一范式表示会给应用程序员增加负担，现代数据库系统支持多种非原子值，但本章仍限于第一范式关系。

### 数据库设计过程（Database - Design Process）
关系模式r(R)来源有多种：从E - R图转换生成；是包含所有感兴趣属性的单个关系模式，经规范化过程分解为较小模式；是临时设计的关系结果，需测试验证是否满足期望范式 。后续会探讨这些方法的影响及数据库设计中的实际问题，包括性能反规范化和规范化未检测出的不良设计示例。   

### 时间数据建模（Modeling Temporal Data）
- **时间数据特点**：时间数据是指在特定时间间隔内有效的数据 。例如，保留大学中教师的地址信息，包括当前和过去的地址，每个地址都有对应的起始和结束日期 。对教师实体集关联随时间变化的地址，会涉及多值属性（每个值是包含地址和时间间隔的复合值） 。除属性值随时间变化，实体（如学生实体从入学到毕业的时间段 ）和关系（如课程先修关系中课程成为先修课的时间 ）也可能有相关的有效时间 。向E - R图添加这些时间细节会使其难以创建和理解，虽有扩展E - R表示法来处理时间变化的提议，但尚无公认标准。
- **实际处理方法**：实际中，数据库设计者常先忽略时间变化进行数据库设计（包括E - R设计和关系设计 ），之后研究各关系，确定哪些关系需跟踪时间变化 。以course关系为例，课程标题可能随时间改变，可通过添加起始（start）和结束（end）时间属性来处理，得到模式course (course_id, title, dept_name, credits, start, end) 。按SQL:2011标准，时间间隔左闭右开，如[start, end)，最高日期为9999 - 12 - 31 。当课程标题更新时，需更新对应元组的end属性值，并添加新元组。
- **时间相关约束**：
    - **时间函数依赖**：在特定时间点成立的函数依赖称为时间函数依赖 。数据快照指特定时间点的数据值 。形式上，若关系模式$r(R)$的所有合法实例中，r的所有快照都满足函数依赖 $\alpha \to \beta$ ，则时间函数依赖 $\alpha \stackrel{\tau}{\to} \beta$ 在 $r(R)$ 上成立。
    - **时间主键约束**：时间关系的原始主键可能无法唯一标识元组，不能仅通过在主键中添加起始和结束时间属性解决（因可能存在有效时间间隔重叠的数据 ）。时间主键约束要求若两个元组的主键值相同，则它们的有效时间间隔不能重叠。
    - **时间外键约束**：当被引用关系是时间关系时，外键约束更复杂 。时间外键约束要求不仅引用关系中的每个元组在被引用关系中有匹配元组，还需考虑它们的时间间隔 。不要求时间间隔完全匹配，允许引用关系元组的时间间隔被被引用关系中的一个或多个元组的时间间隔覆盖。
- **SQL支持**：SQL:2011标准增加了对时间数据的支持，允许声明现有属性来指定元组的有效时间间隔（如period for validtime (start, end) ），可声明时间主键（如primary key (course_id, validtime without overlaps) ）和时间外键约束 。多数数据库不支持时间主键约束，部分数据库（如PostgreSQL ）可通过变通方法实现，如使用exclude约束确保相同course_id的元组validtime间隔不重叠 。关系代数操作（如选择、投影、连接 ）可扩展用于时间关系，时间连接结果中元组的有效时间定义为派生元组有效时间的交集，若无交集则该元组从结果中丢弃，目前没有数据库原生支持时间连接，但可通过显式处理时间条件的SQL查询实现 。此外，一些数据库系统支持时间间隔相关的谓词（如overlaps、contains、before、after ）和操作（如intersection、difference ）。

### 章节总结（Summary）
- **数据库设计要点**：展示了数据库设计中的陷阱（如信息重复、无法表示某些信息 ）以及如何系统地设计数据库模式以避免这些陷阱。
- **关系数据库设计来源**：从E - R设计发展而来，并说明了模式安全组合的情况。
- **函数依赖与范式**：函数依赖用于定义BCNF和3NF两种常用范式 。若分解是依赖保持的，可仅考虑应用于单个关系的依赖来推导函数依赖，便于测试更新有效性而无需计算连接 。正则覆盖是与给定函数依赖集等价的最小化集合，用于消除多余属性 。将关系分解为BCNF的算法确保无损分解，但有些关系模式不存在依赖保持的BCNF分解 。使用正则覆盖将关系模式分解为3NF，这是对BCNF条件的放宽，该算法生成的设计既无损又依赖保持，3NF关系可能有冗余，但在无依赖保持的BCNF分解情况下可接受。
- **多值依赖与范式**：多值依赖可指定仅函数依赖无法表达的约束，基于此定义了第四范式（4NF）。

### 部分习题：
### 7.3
- **一对一关系**：若在学生（student）和教师（instructor）实体集间存在一对一关系集，意味着对于学生实体集中的每个学生，教师实体集中有且仅有一个教师与之对应，反之亦然 。用函数依赖表示为： $student\_id \to instructor\_id$ 且 $instructor\_id \to student\_id$ ，其中student_id是学生实体集的唯一标识，instructor_id是教师实体集的唯一标识 。这表明知道学生标识就能确定对应的教师标识，知道教师标识也能确定对应的学生标识。
- **多对一关系**：若学生和教师实体集间存在多对一关系集，即多个学生可对应同一个教师 。用函数依赖表示为： $student\_id \to instructor\_id$ ，即通过学生标识能确定与之关联的教师标识，但仅知道教师标识不能唯一确定学生标识（因为一个教师对应多个学生 ）。

### 7.7
#### 计算正则覆盖$F_c$
- **首先检查函数依赖左部的多余属性**：对于 $A \to BC$ ，若去掉 $B$ ， $A^+$ （仅根据 $A \to C$ 计算）不能推出 $B$ ，若去掉 $C$ ， $A^+$ （仅根据 $A \to B$ 计算）不能推出 $C$ ，所以 $A \to BC$ 左部无多余属性；对于 $CD \to E$ ，若去掉 $C$ ， $D^+$ 不能推出 $E$ ，若去掉 $D$ ， $C^+$ 不能推出 $E$ ，所以 $CD \to E$ 左部无多余属性；对于 $B \to D$ ，左部无多余属性；对于 $E \to A$ ，左部无多余属性。
- **然后合并函数依赖**：这里没有可以合并的函数依赖。所以， $F_c = \{A \to BC, CD \to E, B \to D, E \to A\}$ 。

### 7.9
#### SQL查询测试函数依赖$B \to C$
```sql
SELECT COUNT(DISTINCT B, C) = COUNT(DISTINCT B)
FROM r;
```
如果结果为1，说明对于关系r中相同的B值，对应的C值唯一，即函数依赖 $B \to C$ 成立；若结果为0，则不成立。

#### SQL断言强制函数依赖$B \to C$
在SQL中可使用CHECK约束（但实际部分数据库对这种复杂约束支持有限 ）：
```sql
ALTER TABLE r
ADD CONSTRAINT func_dep_check
CHECK (NOT EXISTS (
    SELECT 1
    FROM r r1, r r2
    WHERE r1.B = r2.B AND r1.C <> r2.C
));
```
此断言确保不存在B值相同但C值不同的情况，从而强制实现函数依赖 $B \to C$ 。 
