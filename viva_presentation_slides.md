# 📊 PowerPoint Presentation Blueprint & Viva Speech Script

**Project Title**: Application of Search Algorithms: Optimizing Sri Lankan Kitchen Workflows  
**Course**: CM2510 – Modern Approach to Artificial Intelligence  
**Module Leader**: Prof. Asoka Karunananda  
**Platform**: AI Algorithm Visualizer v2.0 (`aisearch-v2`)  

---

## 🖥️ Slide 1: Title Slide

### 🎨 Visual Layout:
* High-contrast title layout with a clean green/dark-slate color scheme (matching senior slide style).

### 📝 Slide Content:
* **Main Title**: Application of Search Algorithms: Optimizing Sri Lankan Kitchen Workflows
* **Subtitle**: Multi-Resource Task Scheduling & Parallel State-Space Search
* **Course**: CM2510 – Modern Approach to Artificial Intelligence
* **Presenter**: [Your Name] | Student ID: [Your ID]

### 🗣️ Speaker Script (What to Say Out Loud):
> *"Good morning, Professor and examiners. Today I am presenting my project for CM2510: 'Application of Search Algorithms: Optimizing Sri Lankan Kitchen Workflows'. My project models a real-world multi-resource scheduling problem using state-space search to minimize meal preparation time."*

---

## 🖥️ Slide 2: Overview / Table of Contents

### 🎨 Visual Layout:
* 6-box grid with clean icons.

### 📝 Slide Content:
1. **Problem Definition & Motivation** (Sri Lankan Kitchen Scenario)
2. **Detailed Dish-by-Dish Recipe Steps** (Rice, Dhal Curry, Chicken Curry)
3. **State Space & Component Transitions** ($S = [\text{Chef}, \text{Stove}, \text{RiceCooker}, \text{Rice}, \text{Dhal}, \text{Chicken}]$)
4. **Heuristic Formulation & Proof** ($h(n) = \max(\text{Stove}, \text{RiceCooker}) + \text{Prep}$)
5. **Constraint Handling & Dead Ends** ($X_1 - X_5$ with $h(n) = \infty$)
6. **Search Visualization & Algorithm Benchmark** (BFS, DFS, UCS, IDS, Greedy, A*)
7. **Conclusion & Viva Defense Summary**

### 🗣️ Speaker Script:
> *"Here is an overview of today's presentation. I will begin with the real-world problem definition, detail the atomic step-by-step recipes for each dish, explain our 6-component state vector and admissible heuristic equation, demonstrate constraint pruning with dead-end nodes, compare 6 search algorithms on our visualizer, and conclude with key benchmark findings."*

---

## 🖥️ Slide 3: Problem Definition & Real-World Motivation

### 🎨 Visual Layout:
* 2-Column Layout: Left column shows Sri Lankan dishes (Rice, Dhal Curry, Chicken Curry); Right column shows hardware resource constraints.

### 📝 Slide Content:
* **Target Meal**: White Rice + Dhal Curry + Chicken Curry
* **The Challenge**: Preparing 3 dishes efficiently under strict kitchen hardware constraints.
* **Hardware & Resource Limits**:
  * 👨‍🍳 **Human Chef ($H$)**: 1 Worker (Capacity: 1 active manual task at a time).
  * 🔥 **Gas Stove ($S$)**: 1 Burner (Capacity: 1 pot at a time).
  * ⚡ **Electric Rice Cooker ($RC$)**: 1 Dedicated Unit (Passive background steaming).

### 🗣️ Speaker Script:
> *"In a typical household, cooking a complete meal involves multiple active preparation steps and passive cooking processes. Our goal is to schedule these tasks to serve dinner in the shortest possible time, respecting the constraint that a single human chef can only cut or stir one item at a time, and the gas stove has only one burner."*

---

## 🖥️ Slide 4: Preparation Timing & Realistic Batching Optimization

### 🎨 Visual Layout:
* Comparison card showing **Sequential Prep (8 mins)** vs **Batch Prep (5 mins)**.

### 📝 Slide Content:
* **Single Dish Preparation**:
  * 🍲 **Dhal Prep**: Chop onions, garlic & wash lentils = **4 mins active**
  * 🍗 **Chicken Prep**: Cut chicken & chop spices = **4 mins active**
  * ⏱️ **Total Sequential Prep**: $4\text{m} + 4\text{m} = \mathbf{8\text{ mins}}$
* ⚡ **Realistic Batching Optimization (`B1`)**:
  * Chop onions, garlic, ginger & chillies for **BOTH curries at once** on one cutting board = **5 mins active**
  * 💡 **Realistic Rationale**: Chopping a double volume of vegetables takes 5 mins instead of 4, but completely eliminates duplicate counter setup, board washing, and ingredient fetching $\implies$ **Saves 3 minutes!**

### 🗣️ Speaker Script:
> *"In real kitchens, chopping vegetables for both curries simultaneously takes 5 minutes—slightly longer than single prep (4 minutes) due to double volume—but eliminates redundant board cleaning and setup, saving 3 minutes compared to sequential 8-minute prep."*

---

## 🖥️ Slide 5: Multi-State Vector Tuple Representation `[...]`

### 🎨 Visual Layout:
* Prominent equation box showcasing the 6-component state vector with color-coded domain values and physical constraints.

### 📝 Slide Content:
$$\text{State Vector } S = [\text{Chef}, \,\, \text{GasStove}, \,\, \text{RiceCooker}, \,\, \text{Rice}, \,\, \text{Dhal}, \,\, \text{Chicken}]$$

* 👨‍🍳 **Chef ($H$)**: `Free`, `Busy` (At decision nodes, Chef = `Free`)
* 🔥 **GasStove ($S$)**: `Free`, `Busy` (Capacity Limit: 1 burner pot)
* ⚡ **RiceCooker ($RC$)**: `Free`, `Busy` (Capacity Limit: 1 appliance, launched ONCE)
* 🍚 **Rice ($R$)**: `Raw` $\longrightarrow$ `Cooking` $\longrightarrow$ `Done`
* 🍲 **Dhal ($D$)**: `Raw` $\longrightarrow$ `Prepped` $\longrightarrow$ `Cooking` $\longrightarrow$ `Done`
* 🍗 **Chicken ($C$)**: `Raw` $\longrightarrow$ `Prepped` $\longrightarrow$ `Cooking` $\longrightarrow$ `Done`

### 🗣️ Speaker Script:
> *"Every discrete milestone in our search space is represented by a 6-component state tuple: Chef, Gas Stove, Rice Cooker, Rice, Dhal, and Chicken. Rice moves through 3 progress values, while Dhal and Chicken move through 4 progress values under strict worker, stove burner, and precedence constraints."*

---

## 🖥️ Slide 6: Master Action Tables (Max 3-Word Action Definitions)

### 🎨 Visual Layout:
* Clean 3-section table showing all paths with max 3-word action descriptions.

### 📝 Slide Content:

#### 🟢 Path 1: Optimal Parallel Path (Goal $G_1 = 42\text{m}$) ⭐
* `S0 → S1`: `Dhal: Prep` & `Chicken: Prep` | **Batch chop veggies** (5m) $\implies g=5\text{m}, h=37\text{m}, f=42\text{m}$
* `S1 → S3`: `Rice: Cook` | **Start rice cooker** (4m) $\implies g=9\text{m}, h=33\text{m}, f=42\text{m}$
* `S3 → S6`: `Dhal: Cook` | **Simmer dhal stove** (3m) $\implies g=12\text{m}, h=30\text{m}, f=42\text{m}$
* `S6 → S9`: `Dhal: Done` & `Rice: Done` | **Both dishes finish** (10m) $\implies g=22\text{m}, h=20\text{m}, f=42\text{m}$
* `S9 → S12`: `Chicken: Cook` | **Cook chicken stove** (20m) $\implies g=42\text{m}, h=0\text{m}, f=42\text{m}$
* `S12 → G1`: `Chicken: Done` | **Serve dinner meal** (0m) $\implies \mathbf{g=42\text{m}, f=42\text{m}}$

#### 🟡 Path 2: Standard Sequential Path (Goal $G_2 = 67\text{m}$)
* `S0 → S2`: **Prep dhal only** (4m) | `S2 → S4`: **Prep chicken only** (4m) | `S4 → S7`: **Start rice cooker** (4m) | `S7 → S10`: **Cook dhal stove** (15m) | `S10 → S13`: **Cook chicken stove** (20m) | `S13 → G2`: **Serve dinner meal** (0m) $\implies \mathbf{g=67\text{m}}$

#### 🟠 Path 3: Single-Burner Bottleneck Path (Goal $G_3 = 100\text{m}$)
* `S0 → S2`: **Prep dhal only** (4m) | `S2 → S5`: **Simmer dhal stove** (15m) | `S5 → S8`: **Prep chicken counter** (4m) | `S8 → S11`: **Cook chicken stove** (20m) | `S11 → S14`: **Boil rice stove** (25m) | `S14 → G3`: **Serve dinner meal** (0m) $\implies \mathbf{g=100\text{m}}$

### 🗣️ Speaker Script:
> *"Our search space contains 3 distinct goal paths. Goal G1 is the 42-minute optimal path utilizing 5-minute batch prep and dual-appliance parallelism. Goal G2 is a 67-minute sequential path. Goal G3 is a 100-minute stove-only bottleneck path."*

---

## 🖥️ Slide 7: Comparative Benchmark Summary Table

### 🎨 Visual Layout:
* Master comparison table matching the senior PDF benchmark format.

### 📝 Slide Content:

| Search Algorithm | Category | Path Cost $g(G)$ | Nodes Explored | Goal Found? | Optimality |
| :--- | :--- | :---: | :---: | :---: | :--- |
| ⭐ **A* Search** | Informed | **42 mins** | **5** | ✅ Yes | **Globally Optimal & Most Efficient** |
| 🟠 **Greedy Best-First** | Informed | **42 mins** | **5** | ✅ Yes | Fast (Non-optimal generally) |
| 🟣 **Uniform Cost (UCS)** | Uninformed | **42 mins** | 10 | ✅ Yes | **Globally Optimal** (Slow) |
| 🔵 **BFS** | Uninformed | 67 mins | 11 | ✅ Yes | Suboptimal (Hop count) |
| 🔴 **DFS** | Uninformed | 100 mins | 14 | ✅ Yes | Suboptimal (Trapped in deep path) |
| 🟡 **IDS** | Uninformed | 67 mins | 16 | ✅ Yes | Suboptimal (Re-explores levels) |

### 🗣️ Speaker Script:
> *"As shown in this benchmark summary table, A* Search finds the 42-minute optimal schedule in just 5 node expansions, while uninformed algorithms like BFS and DFS find suboptimal paths."*

---

## 🖥️ Slide 8: Q&A / Viva Defense Cheat-Sheet & Thank You

### 🎨 Visual Layout:
* Clean "Thank You!" layout with student contact details and key Viva defense formulas.

### 📝 Slide Content:
* **Thank You!**
* **Feel free to ask any questions...**
* **Quick Reference Formulas for Viva**:
  * State Tuple: $S = [H, S, RC, R, D, C]$ (6 components)
  * Heuristic: $h(n) = \max(\text{Stove}, \text{Cooker}) + \text{Prep}$
  * Optimal Cost: $g(G_1) = 42\text{ mins}$ | Nodes Explored: $5$

### 🗣️ Speaker Script:
> *"Thank you for your time and attention. I am now open to any questions."*
