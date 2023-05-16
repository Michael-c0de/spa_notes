###### A. Sign Analysis

1. **Definition**

   $[v]$ denoting an abstract state that gives the **sign values** for all variables at the program point immediately **after** $v$, abstract state can be +, -, 0, $\top$...

2. **Join function**

   The top element $\top$ of our lattice is  universe(variable can be any sign value), so we use union; 

   The sign value of variable at node *v* depends on statements before that node, so we combines the abstract states from the predecessors;

   For node *v*, we have

$$
JOIN(v) = \bigcup_{w\ \epsilon\ {pred(v)}}{
\left[w\right]}
$$

3. **Constraint rule**

   for different statements, we define different constraint rule:

   - $X = E$

     the abstract state is equal to the the state before the assignment, except that the abstract value of $X$ is updated.
     $$
     [v] = JOIN(v)[X\rightarrow{eval\left(JOIN(v),E\right)}]
     $$
     $eval(σ,E)$ performs an abstract evaluation of expression $E$ relative to an abstract state $σ$

   - var $\ X_1,...,X_n$

     $X_i$ is not assigned, so their abstract value can be any.
     $$
     [v] = JOIN(v)[X_1\rightarrow{\top},...,X_n\rightarrow{\top}]
     $$

   - others
     $$
     [v] = JOIN(v).
     $$

###### B. Constant Propagation Analysis

$\left[v\right]$denoting an abstract state that gives the **constant values** for all variables at the program point immediately **after** *v*, abstract states include all integers, top and button. others is the same as **Sign Analysis**.



###### C. Live Variables Analysis 

1. **Definition**

   A variable is *live* at the point *v*, if it may be read later before being written, we use $[v]$ to denote **the subset of variables** that are live at program point **before** node $v$.

2.  **Join function**

   The top element $\top$ of our lattice is universe (all variables are live), so we use union; 

   The live variables at node *v* depend on statements after that node, so we combines the abstract states from the successors.

   For node *v*, we have
   $$
   JOIN(v) = \bigcup_{w\ \epsilon\ {succ(v)}}{
   \left[w\right]}
   $$

3.  **Constraint rule**

   for different statements, we define different constraint rule:

   - $X = E$

     remove the variable being written to, union the variables that are needed for the right-hand-side expression
     $$
     [v] = JOIN(v)\setminus\left\{X\right\}\bigcup{vars\left(E\right)}
     $$
     

   - branch condition and outputs statements
     $$
     [v] = JOIN(v)\bigcup{vars\left(E\right)}
     $$

   - var $\ X_1,...,X_n$

     just kill $X_i$
     $$
     [v] = JOIN(v)\setminus\left\{ X1,...,X_n \right\}
     $$

   - exit nodes
     $$
     [exit] = \emptyset
     $$

   - others
     $$
     [v] = JOIN(v).
     $$
     

###### D. Available Expression Analysis

1. **Definition**

   A nontrivial expression is *available* at a program point if it has already been computed earlier, we use $[v]$ to denote the **subset of expressions** that are guaranteed always to be available at the program point **after** that node.

2. **Join function**

   The top element $\top$ of our lattice is  empty(none is available), so we use intersection; 

   The available expressions at node *v* depend on statements before that node, so we combines the abstract states from the predecessors.

   For node *v*, we have
   $$
   JOIN(v) = \bigcap_{w\ \epsilon\ {pred(v)}}{
   \left[w\right]}
   $$

3. **Constraint rule**

   for different statements, we define different constraint rule:

   - $X = E$

     union all nontrivial expressions about $E$, remove all expressions that contain the variable $X$
     $$
     [v] = JOIN(v)\bigcup{exps\left(E\right)}\downarrow{X}
     $$

   - branch condition and outputs statements
     $$
     [v] = JOIN(v)\bigcup{exps\left(E\right)}
     $$

   - entry nodes

     no expressions are available here
     $$
     [enrty] = \emptyset
     $$

   - others
     $$
     [v] = JOIN(v).
     $$
     

###### E. Very Busy Expressions Analysis

1. **Definition**

   An expression is very *busy* if it will definitely evaluated again before its values changes, we use $[v]$ to denote the **subset of expressions** that definitely are busy **before** the node.

2. **Join function**

   The top element $\top$ of our lattice is  empty(none is busy), so we use intersection; 

   The busy expressions at node *v* depend on statements after that node, so we combines the abstract states from the successors.

   For node *v*, we have
   $$
   JOIN(v) = \bigcap_{w\ \epsilon\ {succ(v)}}{
   \left[w\right]}
   $$

3. **Constraint rule**

   for different statements, we define different constraint rule:

   - $X = E$

     remove all expressions that contain the variable $X$, union all nontrivial expressions about $E$
     $$
     [v] = JOIN(v)\downarrow{X}\bigcup{exps\left(E\right)}
     $$

   - exit nodes

     no expressions are busy here
     $$
     [exit] = \emptyset
     $$

   - others

     the same as for **Available Expression Analysis**

     

###### F. Reaching Definition Analysis

1. **Definition**

   The *reaching definitions* for a given program point are those assignments which may influence current variables, we use $[v]$ to denote the **set of assignments** that may define values of variables at the program point **after** the node.

2. **Join function**

   The top element $\top$ of our lattice is universe(all assignments are useful), so we use union; 

   The busy expressions at node *v* depend on statements before that node, so we combines the abstract states from the predecessors.
   $$
   JOIN(v) = \bigcup_{w\ \epsilon\ {pred(v)}}{
   \left[w\right]}
   $$

3. **Constraint rule**

   for different statements, we define different constraint rule:

   - $X = E$

     remove all assignment that to the variable $X$, while union new assignment.
     $$
     [v] = JOIN(v)\downarrow{X}\bigcup{exps\left(X = E\right)}
     $$

   - others
     $$
     [v] = JOIN(v)
     $$