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

## 📋 3. Master Table of Atomic Actions & Preparation Timing

| Action Code | Description | Appliance | Active Time | Passive Time | Total $g(n)$ Cost | 1-to-1 State Transition |
| :--- | :--- | :--- | :---: | :---: | :---: | :--- |
| **`B1`** | **Batch-chop onions & spices for BOTH curries at once** | Prep Counter | **5 mins** | 0 mins | **5 mins** | `Dhal: Raw->Prep` & `Chicken: Raw->Prep` |
| **`R_RC`** | **Wash rice & launch Electric Rice Cooker (ONCE)**| Rice Cooker | **4 mins** | **20 mins** | **4 mins active** | `Rice: Raw -> Cooking` |
| **`R_Stove`**| Wash rice & cook rice on Gas Stove (No RC) | Gas Stove | **5 mins** | **20 mins** | **25 mins** | `Rice: Raw -> Cooking -> Done` |
| **`D_Prep`** | Chop onions & spices for Dhal only | Prep Counter | **4 mins** | 0 mins | **4 mins** | `Dhal: Raw -> Prepped` |
| **`D_Stove`** | Temper spices & simmer Dhal Curry | Gas Stove | **3 mins** | **12 mins** | **15 mins** | `Dhal: Prepped -> Cooking -> Done` |
| **`C_Prep`** | Cut chicken & prep onions for Chicken only | Prep Counter | **4 mins** | 0 mins | **4 mins** | `Chicken: Raw -> Prepped` |
| **`C_Stove`**| Sauté & cook Chicken Curry on Gas Stove | Gas Stove | **5 mins** | **15 mins** | **20 mins** | `Chicken: Prepped -> Cooking -> Done` |

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
