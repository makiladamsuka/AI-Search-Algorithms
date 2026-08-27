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
| **Goal Formulations** | 1 single goal node. | **3 Distinct Goal Nodes ($G_1=41\text{m}, G_2=67\text{m}, G_3=100\text{m}$)** demonstrating algorithm search behavior. |

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

## 📋 3. Master Table of Atomic Actions & 1-to-1 State Transitions

| Action Code | Description | Appliance | Active Time | Passive Time | Total $g(n)$ Cost | 1-to-1 State Transition |
| :--- | :--- | :--- | :---: | :---: | :---: | :--- |
| **`B1`** | **Batch-chop onions & spices for BOTH curries** | Prep Counter | **4 mins** | 0 mins | **4 mins** | `Dhal: Raw->Prep` & `Chicken: Raw->Prep` |
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
| ❌ **`X1`** | `[Free, Busy, Free, Raw, Raw, Cooking]` | Cook Raw Un-prepped Meat (20m) | **Precedence Rule** | Cooking raw un-prepped chicken $\implies$ **Recipe Ruined!** |
| ❌ **`X2`** | `[Free, Busy, Free, Raw, Cooking, Raw]` | Cook Unwashed Dhal (12m) | **Precedence Rule** | Cooking raw unwashed lentils $\implies$ **Contaminated Food!** |
| ❌ **`X3`** | `[Free, Busy, Free, Raw, Cooking, Cooking]`| Stove Collision (2 Pots on 1 Burner) (15m) | **Hardware Capacity**| Putting 2 pots on 1 gas stove burner $\implies$ **Physical Conflict!** |
| ❌ **`X4`** | `[Free, Free, Busy, Cooking, Raw, Raw]` | Launch Dry Rice Cooker (1m) | **Appliance Rule** | Launching Rice Cooker with dry rice $\implies$ **Cooker Scorched!** |
| ❌ **`X5`** | `[Free, Free, Free, Done, Prepped, Prepped]`| Serve Uncooked Prepped Meal (10m) | **Recipe Completion**| Serving dinner while Dhal/Chicken is still `Prepped` $\implies$ **Uncooked Meal!** |

---

## 🌲 5. Clean State-Space Graph Diagram

```mermaid
graph TD
    %% Root State
    S0["S0: [Free, Free, Free, Raw, Raw, Raw]<br/><i>g=0, h=41, f=41</i>"]

    %% Level 1 Top Decisions & Dead Ends
    S0 -->|Batch Cut Ingredients for BOTH (4m)| S1_BatchBoth["S1_BatchBoth: [Free, Free, Free, Raw, Prepped, Prepped]<br/><i>g=4, h=37, f=41 (OPTIMAL BRANCH)</i>"]
    S0 -->|Cut Ingredients for Dhal Only (4m)| S1_CutDhal["S1_CutDhal: [Free, Free, Free, Raw, Prepped, Raw]<br/><i>g=4, h=45, f=49 (SUBOPTIMAL BRANCH)</i>"]
    S0 -->|Cook Raw Un-prepped Meat (20m)| X1["❌ X1: [Free, Busy, Free, Raw, Raw, Cooking]<br/><i>g=20, h=999 [TERMINATED]</i>"]
    S0 -->|Start Dry Rice Cooker (1m)| X4["❌ X4: [Free, Free, Busy, Cooking, Raw, Raw]<br/><i>g=1, h=999 [TERMINATED]</i>"]

    %% Level 2 Second Steps & Dead Ends
    S1_BatchBoth -->|Start Rice Cooker ONCE (4m)| S2_StartRice["S2_StartRice: [Free, Free, Busy, Cooking, Prepped, Prepped]<br/><i>g=8, h=33, f=41</i>"]
    S1_BatchBoth -->|Stove Collision (15m)| X3["❌ X3: [Free, Busy, Free, Raw, Cooking, Cooking]<br/><i>g=15, h=999 [TERMINATED]</i>"]

    S1_CutDhal -->|Cut Chicken Ingredients Next (4m)| S2_CutChickenSeq["S2_CutChickenSeq: [Free, Free, Free, Raw, Prepped, Prepped]<br/><i>g=8, h=41, f=49</i>"]
    S1_CutDhal -->|Simmer Dhal First on Stove (15m)| S2_SimmerDhalFirst["S2_SimmerDhalFirst: [Free, Busy, Free, Raw, Cooking, Raw]<br/><i>g=19, h=55, f=74</i>"]
    S1_CutDhal -->|Cook Unwashed Dhal (12m)| X2["❌ X2: [Free, Busy, Free, Raw, Cooking, Raw]<br/><i>g=12, h=999 [TERMINATED]</i>"]
    S2_CutChickenSeq -->|Serve Uncooked Meal (10m)| X5["❌ X5: [Free, Free, Free, Done, Prepped, Prepped]<br/><i>g=10, h=999 [TERMINATED]</i>"]

    %% Level 3 Third Steps
    S2_StartRice -->|Simmer Dhal on Stove (3m)| S3_SimmerDhal["S3_SimmerDhal: [Free, Busy, Busy, Cooking, Cooking, Prepped]<br/><i>g=11, h=30, f=41</i>"]
    S2_CutChickenSeq -->|Start Rice Cooker (4m)| S3_StartRiceSeq["S3_StartRiceSeq: [Free, Free, Busy, Cooking, Prepped, Prepped]<br/><i>g=12, h=37, f=49</i>"]
    S2_SimmerDhalFirst -->|Cut Ingredients for Chicken (4m)| S3_CutChickenStove["S3_CutChickenStove: [Free, Free, Free, Raw, Done, Prepped]<br/><i>g=23, h=51, f=74</i>"]

    %% Level 4 Fourth Steps
    S3_SimmerDhal -->|Dhal & Rice Finish Together (10m)| S4_DhalDone_RiceDone["S4_DhalDone_RiceDone: [Free, Free, Free, Done, Done, Prepped]<br/><i>g=21, h=20, f=41</i>"]
    S3_StartRiceSeq -->|Simmer Dhal on Stove (15m)| S4_CookDhalSeq["S4_CookDhalSeq: [Free, Busy, Busy, Cooking, Cooking, Prepped]<br/><i>g=27, h=20, f=47</i>"]
    S3_CutChickenStove -->|Cook Chicken on Stove (20m)| S4_CookChickenStove["S4_CookChickenStove: [Free, Busy, Free, Raw, Done, Cooking]<br/><i>g=43, h=25, f=68</i>"]

    %% Level 5 Stove Cooking Pipelines
    S4_DhalDone_RiceDone -->|Cook Chicken Curry on Stove (20m)| S5_CookChickenOptimal["S5_CookChickenOptimal: [Free, Busy, Free, Done, Done, Cooking]<br/><i>g=41, h=0, f=41</i>"]
    S4_CookDhalSeq -->|Cook Chicken Curry on Stove (20m)| S5_CookChickenSeq["S5_CookChickenSeq: [Free, Busy, Free, Cooking, Done, Cooking]<br/><i>g=47, h=20, f=67</i>"]
    S4_CookChickenStove -->|Wash & Cook Rice on Stove (25m)| S5_CookRiceStove["S5_CookRiceStove: [Free, Busy, Free, Cooking, Done, Done]<br/><i>g=68, h=32, f=100</i>"]

    %% Level 6 Final Goals
    S5_CookChickenOptimal --> G1["🟢 G1_Optimal: [Free, Free, Free, Done, Done, Done]<br/><i>g=41, h=0, f=41 (OPTIMAL GOAL)</i>"]
    S5_CookChickenSeq --> G2["🟡 G2_Sequential: [Free, Free, Free, Done, Done, Done]<br/><i>g=67, h=0, f=67 (SUBOPTIMAL GOAL)</i>"]
    S5_CookRiceStove --> G3["🟠 G3_StoveOnly: [Free, Free, Free, Done, Done, Done]<br/><i>g=100, h=0, f=100 (STOVE ONLY GOAL)</i>"]

    %% Styling
    classDef startGoal fill:#fef3c7,stroke:#d97706,stroke-width:2px;
    classDef optimal fill:#d1fae5,stroke:#059669,stroke-width:2px;
    classDef suboptimal fill:#ffe4e6,stroke:#e11d48,stroke-width:2px;
    classDef deadend fill:#fee2e2,stroke:#dc2626,stroke-width:2px;

    class S0,G1 startGoal;
    class S1_BatchBoth,S2_StartRice,S3_SimmerDhal,S4_DhalDone_RiceDone,S5_CookChickenOptimal optimal;
    class S1_CutDhal,S2_CutChickenSeq,S2_SimmerDhalFirst,S3_StartRiceSeq,S3_CutChickenStove,S4_CookDhalSeq,S4_CookChickenStove,S5_CookChickenSeq,S5_CookRiceStove,G2,G3 suboptimal;
    class X1,X2,X3,X4,X5 deadend;
```

---

## 🧪 6. Multi-Algorithm Search Benchmark & Results

Executing 8 search algorithms on `aisearch-v2` yields the following performance data:

| Algorithm Name | Category | Path Found | Path Cost $g(G)$ | Nodes Explored | Goal Found? | Optimality Classification |
| :--- | :--- | :--- | :---: | :---: | :---: | :--- |
| ⭐ **A* Search** | Informed | `S0` $\rightarrow$ `S1_BatchBoth` $\rightarrow$ `S2_StartRice` $\rightarrow$ `S3_SimmerDhal` $\rightarrow$ `S4_DhalDone` $\rightarrow$ `G1` | **41 mins** | **5** | ✅ Yes | **Globally Optimal & Most Efficient** |
| 🟠 **Greedy Best-First**| Informed | `S0` $\rightarrow$ `S1_BatchBoth` $\rightarrow$ `S2_StartRice` $\rightarrow$ `S3_SimmerDhal` $\rightarrow$ `S4_DhalDone` $\rightarrow$ `G1` | **41 mins** | **5** | ✅ Yes | Fast, but non-optimal on arbitrary graphs |
| 🟣 **Uniform Cost (UCS)**| Uninformed| `S0` $\rightarrow$ `S1_BatchBoth` $\rightarrow$ `S2_StartRice` $\rightarrow$ `S3_SimmerDhal` $\rightarrow$ `S4_DhalDone` $\rightarrow$ `G1` | **41 mins** | **10** | ✅ Yes | **Globally Optimal**, but explores 10 nodes ($h=0$) |
| 🔵 **BFS** | Uninformed| `S0` $\rightarrow$ `S1_CutDhal` $\rightarrow$ `S2_CutChickenSeq` $\rightarrow$ `S3_StartRiceSeq` $\rightarrow$ `S4_CookDhalSeq` $\rightarrow$ `G2` | 67 mins | 11 | ✅ Yes | **Suboptimal**: Counts edge hops, NOT minutes! |
| 🔴 **DFS** | Uninformed| `S0` $\rightarrow$ `X4` (Dead End) $\rightarrow$ Backtrack $\rightarrow$ `S1_CutDhal` $\rightarrow$ `S2_SimmerDhalFirst` $\rightarrow$ `G3` | 100 mins | 14 | ✅ Yes | **Suboptimal**: Gets trapped in deep branches |
| 🟡 **IDS** | Uninformed| `S0` $\rightarrow$ `S1_CutDhal` $\rightarrow$ `S2_CutChickenSeq` $\rightarrow$ `S3_StartRiceSeq` $\rightarrow$ `S4_CookDhalSeq` $\rightarrow$ `G2` | 67 mins | 16 | ✅ Yes | **Suboptimal**: Re-explores levels repeatedly |
