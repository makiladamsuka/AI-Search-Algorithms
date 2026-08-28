# 🎓 Academic Project Report: Sri Lankan Smart Kitchen Workflow Scheduler

**Course**: CM2510 – Modern Approach to Artificial Intelligence  
**Domain**: Multi-Resource State-Space Search & Workflow Scheduling  
**Target Goal**: Prepare White Rice, Dhal Curry, and Chicken Curry in Minimum Total Time  
**Visualization Platform**: AI Algorithm Visualizer v2.0 (`aisearch-v2`)  

---

## 📌 Executive Summary & Senior PDF Benchmark Analysis

By inspecting the project submissions of senior students (*Pramuditha W.M.L.*, *Ariyarathna Y S D*, and *Abeyrathne S.M.S*), we identified key formatting standards and critical areas where our project advances the state of the art:

### 💡 Senior Presentations Analysis vs. Our Enhanced Project:

| Evaluation Aspect | Senior Project Approach | Our Sri Lankan Kitchen Scheduler (Enhanced) |
| :--- | :--- | :--- |
| **Domain Modeling** | Static travel bag packing or chemical reaction steps. | **Dynamic Multi-Resource Kitchen Scheduling** (Human Chef + Gas Stove + Electric Rice Cooker). |
| **State Vector** | Single scalar node labels (e.g. `FeSO4` or `Item`). | **6-Component Vector `[Chef, Stove, RiceCooker, Rice, Dhal, Chicken]`** representing discrete milestone states. |
| **Parallelism** | Strictly 1 step at a time (sequential edges). | **Dual-Appliance Kitchen Parallelism** (Electric Rice Cooker steaming + Gas Stove simmering simultaneously). |
| **Heuristic Formulation**| Simple linear count: $h(n) = \text{Remaining Steps} \times 10$. | **Admissible Bottleneck $\max()$ Formula**: $h(n) = \max(\text{Stove}, \text{RiceCooker}) + \text{Prep}$. |
| **Constraint Handling** | Simple node graphs without dead ends. | **5 Realistic Dead-End Failure Scenarios ($X_1-X_5$)** with $h(n) = \infty$ pruning. |
| **Goal Formulations** | 1 single goal node. | **3 Distinct Goal Nodes ($G_1=42\text{m}, G_2=67\text{m}, G_3=100\text{m}$)** demonstrating algorithm search behavior. |

---

## 🍳 1. Problem Definition & Real-World Motivation

In a Sri Lankan household, preparing a standard meal consisting of **White Rice**, **Dhal Curry**, and **Chicken Curry** requires executing active manual prep tasks and passive appliance cooking. 

### ⚙️ Kitchen Hardware & Resource Constraints:
1. **Human Chef ($H$)**: 1 Single worker (Capacity: 1 active manual task at a time).
2. **Gas Stove ($S$)**: 1 Single burner (Capacity: 1 pot at a time).
3. **Electric Rice Cooker ($RC$)**: 1 Dedicated unit (Capacity: 1 batch, works independently in parallel).

---

## 📊 2. Formal State-Space Tuple Representation

Every discrete state node in the search graph is represented by a 6-component tuple:

$$\text{State Vector } S = [\text{Chef}, \,\, \text{GasStove}, \,\, \text{RiceCooker}, \,\, \text{Rice}, \,\, \text{Dhal}, \,\, \text{Chicken}]$$

* **Chef ($H$)**: `Free`, `Busy`
* **GasStove ($S$)**: `Free`, `Busy`
* **RiceCooker ($RC$)**: `Free`, `Busy`
* **Rice ($R$)**: `Raw` $\rightarrow$ `Cooking` $\rightarrow$ `Done`
* **Dhal ($D$)**: `Raw` $\rightarrow$ `Prepped` $\rightarrow$ `Cooking` $\rightarrow$ `Done`
* **Chicken ($C$)**: `Raw` $\rightarrow$ `Prepped` $\rightarrow$ `Cooking` $\rightarrow$ `Done`

---

## 📋 3. Master Step-by-Step Action Table (Max 3-Word Action Definitions)

### 🟢 Path 1: Optimal Parallel Path (Goal $G_1 = 42\text{ mins}$) ⭐

| Step | Food State Transition | Kitchen Action | Step $\Delta g$ | Time Spent $g(n)$ | Remaining $h(n)$ | Total Score $f(n)$ |
| :---: | :--- | :--- | :---: | :---: | :---: | :---: |
| **`S0`** | `All Dishes: Raw` | **Start cooking** | `0m` | `0m` | `42m` | **`42m`** |
| **`S0 → S1`** | `Dhal: Raw → Prep`<br>`Chicken: Raw → Prep` | **Batch chop veggies** | `5m` | `5m` | `37m` | **`42m`** |
| **`S1 → S3`** | `Rice: Raw → Cook` | **Start rice cooker** | `4m` | `9m` | `33m` | **`42m`** |
| **`S3 → S6`** | `Dhal: Prep → Cook` | **Simmer dhal stove** | `3m` | `12m` | `30m` | **`42m`** |
| **`S6 → S9`** | `Dhal: Cook → Done`<br>`Rice: Cook → Done` | **Both dishes finish** | `10m` | `22m` | `20m` | **`42m`** |
| **`S9 → S12`** | `Chicken: Prep → Cook` | **Cook chicken stove** | `20m` | `42m` | `0m` | **`42m`** |
| **`S12 → G1`** | `Chicken: Cook → Done` | **Serve dinner meal** | `0m` | **`42m`** | **`0m`** | 🏆 **`42m` (Optimal)** |

---

### 🟡 Path 2: Standard Sequential Path (Goal $G_2 = 67\text{ mins}$)

| Step | Food State Transition | Kitchen Action | Step $\Delta g$ | Time Spent $g(n)$ | Remaining $h(n)$ | Total Score $f(n)$ |
| :---: | :--- | :--- | :---: | :---: | :---: | :---: |
| **`S0 → S2`** | `Dhal: Raw → Prep` | **Prep dhal only** | `4m` | `4m` | `45m` | **`49m`** |
| **`S2 → S4`** | `Chicken: Raw → Prep` | **Prep chicken only** | `4m` | `8m` | `41m` | **`49m`** |
| **`S4 → S7`** | `Rice: Raw → Cook` | **Start rice cooker** | `4m` | `12m` | `37m` | **`49m`** |
| **`S7 → S10`** | `Dhal: Prep → Done` | **Cook dhal stove** | `15m` | `27m` | `20m` | **`47m`** |
| **`S10 → S13`**| `Chicken: Prep → Cook` | **Cook chicken stove** | `20m` | `47m` | `20m` | **`67m`** |
| **`S13 → G2`** | `Chicken: Cook → Done` | **Serve dinner meal** | `0m` | **`67m`** | **`0m`** | **`67m` (Sequential)** |

---

### 🟠 Path 3: Single-Burner Bottleneck Path (Goal $G_3 = 100\text{ mins}$)

| Step | Food State Transition | Kitchen Action | Step $\Delta g$ | Time Spent $g(n)$ | Remaining $h(n)$ | Total Score $f(n)$ |
| :---: | :--- | :--- | :---: | :---: | :---: | :---: |
| **`S0 → S2`** | `Dhal: Raw → Prep` | **Prep dhal only** | `4m` | `4m` | `55m` | **`59m`** |
| **`S2 → S5`** | `Dhal: Prep → Cook` | **Simmer dhal stove** | `15m` | `19m` | `55m` | **`74m`** |
| **`S5 → S8`** | `Chicken: Raw → Prep`<br>`Dhal: Cook → Done` | **Prep chicken counter** | `4m` | `23m` | `51m` | **`74m`** |
| **`S8 → S11`** | `Chicken: Prep → Done` | **Cook chicken stove** | `20m` | `43m` | `25m` | **`68m`** |
| **`S11 → S14`**| `Rice: Raw → Cook` | **Boil rice stove** | `25m` | `68m` | `32m` | **`100m`** |
| **`S14 → G3`** | `Rice: Cook → Done` | **Serve dinner meal** | `0m` | **`100m`**| **`0m`** | 🟠 **`100m` (Stove-Only)** |

---

## 🛑 4. Master List of 5 Kitchen Dead Ends (Nodes = State Tuples, Edges = Action Violations)

Any state transition violating physical or recipe constraints triggers an **INVALID DEAD-END NODE** with heuristic cost $h(n) = \infty$ (Terminated Branch):

| Invalid Node | Target Node State Tuple `[...]` | Action Attempted (Edge) | Violation Type | Reason / Explanation |
| :--- | :--- | :--- | :--- | :--- |
| ❌ **`X1`** | `[Free, Busy, Free, Raw, Raw, Cooking]` | Cook Raw Chicken (20m) | **Precedence Rule** | Cooking raw un-prepped chicken $\implies$ **Recipe Ruined!** |
| ❌ **`X2`** | `[Free, Busy, Free, Raw, Cooking, Raw]` | Cook Unwashed Dhal (12m) | **Precedence Rule** | Cooking raw unwashed lentils $\implies$ **Contaminated Food!** |
| ❌ **`X3`** | `[Free, Busy, Free, Raw, Cooking, Cooking]`| Stove Collision (15m) | **Hardware Capacity**| Putting 2 pots on 1 gas stove burner $\implies$ **Physical Conflict!** |
| ❌ **`X4`** | `[Free, Free, Busy, Cooking, Raw, Raw]` | Dry Rice Launch (1m) | **Appliance Rule** | Launching Rice Cooker with dry rice $\implies$ **Cooker Scorched!** |
| ❌ **`X5`** | `[Free, Free, Free, Done, Prepped, Prepped]`| Serve Uncooked Meal (10m) | **Recipe Completion**| Serving dinner while Dhal/Chicken is still `Prepped` $\implies$ **Uncooked Meal!** |

---

## 🧪 5. Multi-Algorithm Search Benchmark & Results

Executing 8 search algorithms on `aisearch-v2` yields the following performance data:

| Algorithm Name | Category | Path Found | Path Cost $g(G)$ | Nodes Explored | Goal Found? | Optimality Classification |
| :--- | :--- | :--- | :---: | :---: | :---: | :--- |
| ⭐ **A* Search** | Informed | `S0` $\rightarrow$ `S1` $\rightarrow$ `S3` $\rightarrow$ `S6` $\rightarrow$ `S9` $\rightarrow$ `S12` $\rightarrow$ `G1` | **42 mins** | **5** | ✅ Yes | **Globally Optimal & Most Efficient** |
| 🟠 **Greedy Best-First**| Informed | `S0` $\rightarrow$ `S1` $\rightarrow$ `S3` $\rightarrow$ `S6` $\rightarrow$ `S9` $\rightarrow$ `S12` $\rightarrow$ `G1` | **42 mins** | **5** | ✅ Yes | Fast, but non-optimal on arbitrary graphs |
| 🟣 **Uniform Cost (UCS)**| Uninformed| `S0` $\rightarrow$ `S1` $\rightarrow$ `S3` $\rightarrow$ `S6` $\rightarrow$ `S9` $\rightarrow$ `S12` $\rightarrow$ `G1` | **42 mins** | **10** | ✅ Yes | **Globally Optimal**, but explores 10 nodes ($h=0$) |
| 🔵 **BFS** | Uninformed| `S0` $\rightarrow$ `S2` $\rightarrow$ `S4` $\rightarrow$ `S7` $\rightarrow$ `S10` $\rightarrow$ `S13` $\rightarrow$ `G2` | 67 mins | 11 | ✅ Yes | **Suboptimal**: Counts edge hops, NOT minutes! |
| 🔴 **DFS** | Uninformed| `S0` $\rightarrow$ `X4` (Dead End) $\rightarrow$ Backtrack $\rightarrow$ `S2` $\rightarrow$ `S5` $\rightarrow$ `G3` | 100 mins | 14 | ✅ Yes | **Suboptimal**: Gets trapped in deep branches |
| 🟡 **IDS** | Uninformed| `S0` $\rightarrow$ `S2` $\rightarrow$ `S4` $\rightarrow$ `S7` $\rightarrow$ `S10` $\rightarrow$ `S13` $\rightarrow$ `G2` | 67 mins | 16 | ✅ Yes | **Suboptimal**: Re-explores levels repeatedly |
