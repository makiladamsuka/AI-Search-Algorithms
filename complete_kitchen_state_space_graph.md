# 🍳 Pure State-Space Search Graph: Sri Lankan Smart Kitchen

This document provides the formal specification of the **State-Space Search Graph** strictly adhering to the fundamental lecture notes rule:

> 📌 **Lecture Notes Graph Rule**:
> * **Inside the Circle (Node)** $\longrightarrow$ **The STATE Vector `[...]` ONLY**
> * **On the Arc (Edge)** $\longrightarrow$ **The ACTION Name & Time Cost $g(n)$ ONLY**

---

## 📌 1. Formal State Vector Tuple Definition `[...]`

Each node in the search space represents a pure 6-component state tuple:

$$\text{State } S = [\text{Chef}, \,\, \text{GasStove}, \,\, \text{RiceCooker}, \,\, \text{Rice}, \,\, \text{Dhal}, \,\, \text{Chicken}]$$

* **Chef**: `Free`, `Busy`
* **GasStove**: `Free`, `Busy` (Capacity = 1 pot at a time)
* **RiceCooker**: `Free`, `Busy` (Launched **ONCE** for 20m passive steaming)
* **Rice ($R$)**: `Raw` $\rightarrow$ `Cooking` $\rightarrow$ `Done`
* **Dhal ($D$)**: `Raw` $\rightarrow$ `Prepped` $\rightarrow$ `Cooking` $\rightarrow$ `Done`
* **Chicken ($C$)**: `Raw` $\rightarrow$ `Prepped` $\rightarrow$ `Cooking` $\rightarrow$ `Done`

---

## 🛑 2. The 5 Kitchen Dead-End Nodes (Inside Circle = State, On Arc = Action)

Any state transition violating physical or recipe constraints leads to an **INVALID DEAD-END STATE** with $h(n) = \infty$ (Terminated Branch):

1. ❌ **Node `X1`**: `[Free, Busy, Free, Raw, Raw, Cooking]`
   * *Arc Action*: `Cook Raw Un-prepped Meat (20m)` $\implies$ **Recipe Ruined!**
2. ❌ **Node `X2`**: `[Free, Busy, Free, Raw, Cooking, Raw]`
   * *Arc Action*: `Cook Unwashed Dhal (12m)` $\implies$ **Contaminated Food!**
3. ❌ **Node `X3`**: `[Free, Busy, Free, Raw, Cooking, Cooking]`
   * *Arc Action*: `Stove Collision (15m)` $\implies$ **Physical Conflict!**
4. ❌ **Node `X4`**: `[Free, Free, Busy, Cooking, Raw, Raw]`
   * *Arc Action*: `Start Dry Rice Cooker (1m)` $\implies$ **Appliance Scorched!**
5. ❌ **Node `X5`**: `[Free, Free, Free, Done, Prepped, Prepped]`
   * *Arc Action*: `Serve Uncooked Meal (10m)` $\implies$ **Uncooked Food Served!**

---

## 🌲 3. Pure State-Space Search Graph Diagram

```mermaid
graph TD
    %% Level 0: Root State
    S0["S0: [Free, Free, Free, Raw, Raw, Raw]<br/><i>g=0, h=41, f=41</i>"]

    %% Level 1: Top Decisions & Dead Ends
    S0 -->|Batch Cut Ingredients for BOTH (4m)| S1["S1: [Free, Free, Free, Raw, Prepped, Prepped]<br/><i>g=4, h=37, f=41 (OPTIMAL BRANCH)</i>"]
    S0 -->|Cut Ingredients for Dhal Only (4m)| S2["S2: [Free, Free, Free, Raw, Prepped, Raw]<br/><i>g=4, h=45, f=49 (SUBOPTIMAL BRANCH)</i>"]
    S0 -->|Cook Raw Un-prepped Meat (20m)| X1["❌ X1: [Free, Busy, Free, Raw, Raw, Cooking]<br/><i>g=20, h=999 [TERMINATED]</i>"]
    S0 -->|Start Dry Rice Cooker (1m)| X4["❌ X4: [Free, Free, Busy, Cooking, Raw, Raw]<br/><i>g=1, h=999 [TERMINATED]</i>"]

    %% Level 2: Second Steps & Dead Ends
    S1 -->|Start Rice Cooker ONCE (4m)| S3["S3: [Free, Free, Busy, Cooking, Prepped, Prepped]<br/><i>g=8, h=33, f=41</i>"]
    S1 -->|Stove Collision (15m)| X3["❌ X3: [Free, Busy, Free, Raw, Cooking, Cooking]<br/><i>g=15, h=999 [TERMINATED]</i>"]

    S2 -->|Cut Chicken Ingredients Next (4m)| S4["S4: [Free, Free, Free, Raw, Prepped, Prepped]<br/><i>g=8, h=41, f=49</i>"]
    S2 -->|Simmer Dhal First on Stove (15m)| S5["S5: [Free, Busy, Free, Raw, Cooking, Raw]<br/><i>g=19, h=55, f=74</i>"]
    S2 -->|Cook Unwashed Dhal (12m)| X2["❌ X2: [Free, Busy, Free, Raw, Cooking, Raw]<br/><i>g=12, h=999 [TERMINATED]</i>"]
    S4 -->|Serve Uncooked Meal (10m)| X5["❌ X5: [Free, Free, Free, Done, Prepped, Prepped]<br/><i>g=10, h=999 [TERMINATED]</i>"]

    %% Level 3: Third Steps
    S3 -->|Simmer Dhal on Stove (3m)| S6["S6: [Free, Busy, Busy, Cooking, Cooking, Prepped]<br/><i>g=11, h=30, f=41</i>"]
    S4 -->|Start Rice Cooker (4m)| S7["S7: [Free, Free, Busy, Cooking, Prepped, Prepped]<br/><i>g=12, h=37, f=49</i>"]
    S5 -->|Cut Ingredients for Chicken (4m)| S8["S8: [Free, Free, Free, Raw, Done, Prepped]<br/><i>g=23, h=51, f=74</i>"]

    %% Level 4: Fourth Steps
    S6 -->|Dhal & Rice Finish Together (10m)| S9["S9: [Free, Free, Free, Done, Done, Prepped]<br/><i>g=21, h=20, f=41</i>"]
    S7 -->|Simmer Dhal on Stove (15m)| S10["S10: [Free, Busy, Busy, Cooking, Cooking, Prepped]<br/><i>g=27, h=20, f=47</i>"]
    S8 -->|Cook Chicken on Stove (20m)| S11["S11: [Free, Busy, Free, Raw, Done, Cooking]<br/><i>g=43, h=25, f=68</i>"]

    %% Level 5: Stove Cooking Pipelines
    S9 -->|Cook Chicken Curry on Stove (20m)| S12["S12: [Free, Busy, Free, Done, Done, Cooking]<br/><i>g=41, h=0, f=41</i>"]
    S10 -->|Cook Chicken Curry on Stove (20m)| S13["S13: [Free, Busy, Free, Cooking, Done, Cooking]<br/><i>g=47, h=20, f=67</i>"]
    S11 -->|Wash & Cook Rice on Stove (25m)| S14["S14: [Free, Busy, Free, Cooking, Done, Done]<br/><i>g=68, h=32, f=100</i>"]

    %% Level 6: Final Goals
    S12 --> G1["🟢 G1: [Free, Free, Free, Done, Done, Done]<br/><i>g=41, h=0, f=41 (OPTIMAL GOAL)</i>"]
    S13 --> G2["🟡 G2: [Free, Free, Free, Done, Done, Done]<br/><i>g=67, h=0, f=67 (SUBOPTIMAL GOAL)</i>"]
    S14 --> G3["🟠 G3: [Free, Free, Free, Done, Done, Done]<br/><i>g=100, h=0, f=100 (STOVE ONLY GOAL)</i>"]

    %% Styling
    classDef startGoal fill:#fef3c7,stroke:#d97706,stroke-width:2px;
    classDef optimal fill:#d1fae5,stroke:#059669,stroke-width:2px;
    classDef suboptimal fill:#ffe4e6,stroke:#e11d48,stroke-width:2px;
    classDef deadend fill:#fee2e2,stroke:#dc2626,stroke-width:2px;

    class S0,G1 startGoal;
    class S1,S3,S6,S9,S12 optimal;
    class S2,S4,S5,S7,S8,S10,S11,S13,S14,G2,G3 suboptimal;
    class X1,X2,X3,X4,X5 deadend;
```

---

## 📊 4. Master Node State Table (23 Total Nodes)

| Node ID | State Vector `[...]` (Node Text) | Action Taken (Arc Text) | $g(n)$ | $h(n)$ | $f(n)$ | Classification |
| :--- | :--- | :--- | :---: | :---: | :---: | :--- |
| **`S0`** | `[Free, Free, Free, Raw, Raw, Raw]` | Start State | `0m` | `41m` | `41m` | 🔴 **Source Node** |
| **`S1`** | `[Free, Free, Free, Raw, Prepped, Prepped]` | Batch cut ingredients for BOTH (4m)| `4m` | `37m` | **`41m`**| 🟢 **Optimal Branch** ⭐ |
| **`S2`** | `[Free, Free, Free, Raw, Prepped, Raw]` | Cut Dhal ingredients only (4m) | `4m` | `45m` | `49m` | 🟡 **Suboptimal Branch** |
| **`X1`** | `[Free, Busy, Free, Raw, Raw, Cooking]` | Cook Raw Un-prepped Meat (20m) | `20m` | `999m`| `1019m`| ❌ **DEAD END 1** |
| **`X4`** | `[Free, Free, Busy, Cooking, Raw, Raw]` | Start Dry Rice Cooker (1m) | `1m` | `999m`| `1000m`| ❌ **DEAD END 2** |
| **`S3`** | `[Free, Free, Busy, Cooking, Prepped, Prepped]`| Start Rice Cooker ONCE (4m) | `8m` | `33m` | **`41m`**| 🟢 **Optimal Branch** |
| **`X3`** | `[Free, Busy, Free, Raw, Cooking, Cooking]` | Stove Collision (15m) | `15m` | `999m`| `1014m`| ❌ **DEAD END 3** |
| **`S4`** | `[Free, Free, Free, Raw, Prepped, Prepped]`| Cut Chicken ingredients next (4m)| `8m` | `41m` | `49m` | 🟡 **Suboptimal Branch** |
| **`S5`** | `[Free, Busy, Free, Raw, Cooking, Raw]`| Simmer Dhal first on stove (15m)| `19m` | `55m` | `74m` | 🟠 **No-RC Branch** |
| **`X2`** | `[Free, Busy, Free, Raw, Cooking, Raw]` | Cook Unwashed Dhal (12m) | `12m` | `999m`| `1011m`| ❌ **DEAD END 4** |
| **`X5`** | `[Free, Free, Free, Done, Prepped, Prepped]`| Serve Uncooked Meal (10m) | `10m` | `999m`| `1009m`| ❌ **DEAD END 5** |
| **`S6`** | `[Free, Busy, Busy, Cooking, Cooking, Prepped]`| Simmer Dhal on Stove (3m) | `11m` | `30m` | **`41m`**| 🟢 **Optimal Branch** |
| **`S7`** | `[Free, Free, Busy, Cooking, Prepped, Prepped]`| Start Rice Cooker (4m) | `12m` | `37m` | `49m` | 🟡 **Suboptimal Branch** |
| **`S8`** | `[Free, Free, Free, Raw, Done, Prepped]`| Cut Chicken ingredients (4m) | `23m` | `51m` | `74m` | 🟠 **No-RC Branch** |
| **`S9`** | `[Free, Free, Free, Done, Done, Prepped]`| Dhal & Rice Finish Together (10m) | `21m` | `20m` | **`41m`**| 🟢 **Optimal Branch** |
| **`S10`** | `[Free, Busy, Busy, Cooking, Cooking, Prepped]`| Simmer Dhal Curry on Stove (15m) | `27m` | `20m` | `47m` | 🟡 **Suboptimal Branch** |
| **`S11`** | `[Free, Busy, Free, Raw, Done, Cooking]`| Cook Chicken Curry on Stove (20m) | `43m` | `25m` | `68m` | 🟠 **No-RC Branch** |
| **`S12`** | `[Free, Busy, Free, Done, Done, Cooking]`| Cook Chicken Curry on Stove (20m) | `41m` | `0m` | **`41m`**| 🟢 **Optimal Branch** |
| **`S13`** | `[Free, Busy, Free, Cooking, Done, Cooking]`| Cook Chicken Curry on Stove (20m) | `47m` | `20m` | `67m` | 🟡 **Suboptimal Branch** |
| **`S14`** | `[Free, Busy, Free, Cooking, Done, Done]`| Wash & Cook Rice on Stove (25m) | `68m` | `32m` | `100m` | 🟠 **No-RC Branch** |
| **`G1`** | `[Free, Free, Free, Done, Done, Done]` | Dinner Served (41m) | **`41m`**| **`0m`** | **`41m`**| 🟢 **GOAL 1: OPTIMAL GOAL (41m)** |
| **`G2`** | `[Free, Free, Free, Done, Done, Done]` | Dinner Served (67m) | **`67m`**| **`0m`** | **`67m`**| 🟡 **GOAL 2: SUBOPTIMAL GOAL (67m)** |
| **`G3`** | `[Free, Free, Free, Done, Done, Done]` | Dinner Served (100m) | **`100m`**| **`0m`** | **`100m`**| 🟠 **GOAL 3: STOVE ONLY GOAL (100m)** |
