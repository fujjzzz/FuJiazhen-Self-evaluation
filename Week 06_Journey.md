本周阅读Database-System-Concepts-7th-Edition:PART TWO DATABASE DESIGN,知识点总结如下：  
Chapter 7 Relational Database Design(p303-360)  
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
