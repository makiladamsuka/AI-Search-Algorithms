# 🍳 Pure State-Space Search Graph: Sri Lankan Smart Kitchen

This document provides the formal specification of the **State-Space Search Graph** strictly adhering to the fundamental lecture notes rule:

> 📌 **Lecture Notes Graph Rule**:
> * **Inside the Circle (Node)** $\longrightarrow$ **The STATE Vector `[...]` ONLY**
> * **On the Arc (Edge)** $\longrightarrow$ **The ACTION Name (Max 3 Words) & Time Cost $g(n)$ ONLY**

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

## 🍲 2. Preparation Timing & Realistic Batching Optimization

* **Single Prep for Dhal only**: **4 mins active**
* **Single Prep for Chicken only**: **4 mins active**
* **Sequential Total Prep**: $4\text{m} + 4\text{m} = \mathbf{8\text{ mins}}$
* ⚡ **Batch Prep for BOTH curries at once**: **5 mins active**  
  *(Chopping double volume on one board takes 5 mins instead of 4, but eliminates duplicate setup/cleaning $\implies$ **Saves 3 minutes!**)*

---

## 🛑 3. The 5 Kitchen Dead-End Nodes (Inside Circle = State, On Arc = Action)

Any state transition violating physical or recipe constraints leads to an **INVALID DEAD-END STATE** with $h(n) = \infty$ (Terminated Branch):

1. ❌ **Node `X1`**: `[Free, Busy, Free, Raw, Raw, Cooking]`
   * *Arc Action*: `Cook Raw Chicken (20m)` $\implies$ **Recipe Ruined!**
2. ❌ **Node `X2`**: `[Free, Busy, Free, Raw, Cooking, Raw]`
   * *Arc Action*: `Cook Unwashed Dhal (12m)` $\implies$ **Contaminated Food!**
3. ❌ **Node `X3`**: `[Free, Busy, Free, Raw, Cooking, Cooking]`
   * *Arc Action*: `Stove Collision (15m)` $\implies$ **Physical Conflict!**
4. ❌ **Node `X4`**: `[Free, Free, Busy, Cooking, Raw, Raw]`
   * *Arc Action*: `Dry Rice Launch (1m)` $\implies$ **Appliance Scorched!**
5. ❌ **Node `X5`**: `[Free, Free, Free, Done, Prepped, Prepped]`
   * *Arc Action*: `Serve Uncooked Meal (10m)` $\implies$ **Uncooked Food Served!**

---

## 🌲 4. Pure State-Space Search Graph Diagram

```mermaid
graph TD
    %% Level 0: Root State
    S0["S0: [Free, Free, Free, Raw, Raw, Raw]<br/><i>g=0, h=42, f=42</i>"]

    %% Level 1: Top Decisions & Dead Ends
    S0 -->|Batch chop veggies (5m)| S1["S1: [Free, Free, Free, Raw, Prepped, Prepped]<br/><i>g=5, h=37, f=42 (PATH 1: OPTIMAL)</i>"]
    S0 -->|Prep dhal only (4m)| S2["S2: [Free, Free, Free, Raw, Prepped, Raw]<br/><i>g=4, h=45, f=49 (PATH 2 & 3 BRANCH)</i>"]
    S0 -->|Cook Raw Chicken (20m)| X1["❌ X1: [Free, Busy, Free, Raw, Raw, Cooking]<br/><i>g=20, h=999 [TERMINATED]</i>"]
    S0 -->|Dry Rice Launch (1m)| X4["❌ X4: [Free, Free, Busy, Cooking, Raw, Raw]<br/><i>g=1, h=999 [TERMINATED]</i>"]

    %% Level 2: Second Steps & Dead Ends
    S1 -->|Start rice cooker (4m)| S3["S3: [Free, Free, Busy, Cooking, Prepped, Prepped]<br/><i>g=9, h=33, f=42</i>"]
    S1 -->|Stove Collision (15m)| X3["❌ X3: [Free, Busy, Free, Raw, Cooking, Cooking]<br/><i>g=20, h=999 [TERMINATED]</i>"]

    S2 -->|Prep chicken only (4m)| S4["S4: [Free, Free, Free, Raw, Prepped, Prepped]<br/><i>g=8, h=41, f=49 (PATH 2)</i>"]
    S2 -->|Simmer dhal stove (15m)| S5["S5: [Free, Busy, Free, Raw, Cooking, Raw]<br/><i>g=19, h=55, f=74 (PATH 3)</i>"]
    S2 -->|Cook Unwashed Dhal (12m)| X2["❌ X2: [Free, Busy, Free, Raw, Cooking, Raw]<br/><i>g=16, h=999 [TERMINATED]</i>"]
    S4 -->|Serve Uncooked Meal (10m)| X5["❌ X5: [Free, Free, Free, Done, Prepped, Prepped]<br/><i>g=18, h=999 [TERMINATED]</i>"]

    %% Level 3: Third Steps
    S3 -->|Simmer dhal stove (3m)| S6["S6: [Free, Busy, Busy, Cooking, Cooking, Prepped]<br/><i>g=12, h=30, f=42</i>"]
    S4 -->|Start rice cooker (4m)| S7["S7: [Free, Free, Busy, Cooking, Prepped, Prepped]<br/><i>g=12, h=37, f=49</i>"]
    S5 -->|Prep chicken counter (4m)| S8["S8: [Free, Free, Free, Raw, Done, Prepped]<br/><i>g=23, h=51, f=74</i>"]

    %% Level 4: Fourth Steps
    S6 -->|Both dishes finish (10m)| S9["S9: [Free, Free, Free, Done, Done, Prepped]<br/><i>g=22, h=20, f=42</i>"]
    S7 -->|Cook dhal stove (15m)| S10["S10: [Free, Busy, Busy, Cooking, Cooking, Prepped]<br/><i>g=27, h=20, f=47</i>"]
    S8 -->|Cook chicken stove (20m)| S11["S11: [Free, Busy, Free, Raw, Done, Cooking]<br/><i>g=43, h=25, f=68</i>"]

    %% Level 5: Stove Cooking Pipelines
    S9 -->|Cook chicken stove (20m)| S12["S12: [Free, Busy, Free, Done, Done, Cooking]<br/><i>g=42, h=0, f=42</i>"]
    S10 -->|Cook chicken stove (20m)| S13["S13: [Free, Busy, Free, Cooking, Done, Cooking]<br/><i>g=47, h=20, f=67</i>"]
    S11 -->|Boil rice stove (25m)| S14["S14: [Free, Busy, Free, Cooking, Done, Done]<br/><i>g=68, h=32, f=100</i>"]

    %% Level 6: Final Goals
    S12 --> G1["🟢 G1: [Free, Free, Free, Done, Done, Done]<br/><i>g=42, h=0, f=42 (PATH 1: OPTIMAL)</i>"]
    S13 --> G2["🟡 G2: [Free, Free, Free, Done, Done, Done]<br/><i>g=67, h=0, f=67 (PATH 2: SEQUENTIAL)</i>"]
    S14 --> G3["🟠 G3: [Free, Free, Free, Done, Done, Done]<br/><i>g=100, h=0, f=100 (PATH 3: STOVE-ONLY)</i>"]

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

## 📊 5. Master Action Table (Max 3-Word Action Definitions)

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
