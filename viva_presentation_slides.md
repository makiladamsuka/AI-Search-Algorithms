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

## 🖥️ Slide 4: Formal State-Space Graph Definition (Lecture Notes Rule)

### 🎨 Visual Layout:
* Prominent callout box highlighting the core lecture notes graph definition.

### 📝 Slide Content:
* 📌 **Fundamental Graph Rule (Prof. Asoka Karunananda's Lecture Notes)**:
  * **Inside the Circle (Node)** $\longrightarrow$ **The STATE Vector `[...]` ONLY**
  * **On the Arc (Edge)** $\longrightarrow$ **The ACTION Name & Time Cost $g(n)$ ONLY**

### 🗣️ Speaker Script:
> *"Strictly following our lecture notes for CM2510, every circle (node) in our search graph represents a pure State Vector tuple, while every directed arc (edge) represents an Action operator and its associated time cost."*

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

## 🖥️ Slide 6: Component State Transitions & Duration (Arc Actions)

### 🎨 Visual Layout:
* 3-Column Card Layout showing exact arc actions, active vs. passive minutes, and state transitions.

### 📝 Slide Content:

#### 🍚 **Rice ($R$) Arc Actions**:
* **`Raw` $\longrightarrow$ `Cooking`**: Arc Action `Wash & Launch Rice Cooker` (**4 mins active**).
* **`Cooking` $\longrightarrow$ `Done`**: Arc Action `Passive Steaming` (**20 mins passive background**).

#### 🍲 **Dhal ($D$) Arc Actions**:
* **`Raw` $\longrightarrow$ `Prepped`**: Arc Action `Chop Onions & Wash Dhal` (**4 mins active**, or **`B1` Batch Prep**).
* **`Prepped` $\longrightarrow$ `Cooking`**: Arc Action `Temper Spices on Stove` (**3 mins active**).
* **`Cooking` $\longrightarrow$ `Done`**: Arc Action `Passive Simmering` (**12 mins passive**).

#### 🍗 **Chicken ($C$) Arc Actions**:
* **`Raw` $\longrightarrow$ `Prepped`**: Arc Action `Cut Chicken & Chop Onions` (**4 mins active**, or **`B1` Batch Prep**).
* **`Prepped` $\longrightarrow$ `Cooking`**: Arc Action `Sauté Spices & Chicken on Stove` (**5 mins active**).
* **`Cooking` $\longrightarrow$ `Done`**: Arc Action `Passive Simmering` (**15 mins passive**).

#### ⚡ **Batching Optimization (`B1`)**:
* **Arc Action `B1` (4m Active)**: Transitions BOTH `Dhal: Raw->Prep` AND `Chicken: Raw->Prep` simultaneously $\implies$ **Saves 4 minutes!**

### 🗣️ Speaker Script:
> *"Here are the exact arc actions and their durations in minutes. Rice requires 4 active minutes to wash and launch, followed by 20 passive background minutes. Dhal and Chicken each require prep, stove startup, and passive simmering. Crucially, Action B1 transitions both Dhal and Chicken from Raw to Prepped in a single 4-minute active step, saving 4 minutes!"*

---

## 🖥️ Slide 7: Atomic Actions & Time Cost Table $g(n)$

### 🎨 Visual Layout:
* Structured 5-column table listing actions, appliances, active time, passive time, and total cost $g(n)$.

### 📝 Slide Content:

| Action (Arc) | Description | Appliance | Active Time | Total Cost $g(n)$ | State Transition (Nodes) |
| :--- | :--- | :--- | :---: | :---: | :--- |
| **`B1`** | **Batch-chop onions & spices for BOTH curries** | Prep Counter | **4 mins** | **4 mins** | `S0 -> S1` |
| **`R_RC`** | **Wash rice & launch Electric Rice Cooker (ONCE)** | Rice Cooker | **4 mins** | **4 mins active** | `S1 -> S3` |
| **`D_Stove`** | Temper spices & simmer Dhal Curry | Gas Stove | **3 mins** | **15 mins** | `S3 -> S6` |
| **`C_Stove`** | Sauté & cook Chicken Curry on Gas Stove | Gas Stove | **5 mins** | **20 mins** | `S9 -> S12` |
| **`R_Stove`** | Boil & cook Rice on Gas Stove (No Rice Cooker) | Gas Stove | **5 mins** | **25 mins** | `S11 -> S14` |

### 🗣️ Speaker Script:
> *"Each arc transition represents an action operator with an associated time cost $g(n)$. For example, batching ingredient cutting for both curries takes 4 minutes, saving 4 minutes compared to chopping them separately."*

---

## 🖥️ Slide 8: Admissible Heuristic Equation & Mathematical Proof

### 🎨 Visual Layout:
* Highlighted formula box with green checkmarks for admissibility properties ($h(n) \le h^*(n)$).

### 📝 Slide Content:
$$h(n) = \max \Big( \text{Rem}_{\text{Stove}}(n), \,\, \text{Rem}_{\text{RiceCooker}}(n) \Big) + \text{Rem}_{\text{Prep}}(n)$$

### 📐 Admissibility Proof ($h(n) \le h^*(n)$):
1. **Appliance Bottleneck**: Because the Rice Cooker and Gas Stove run concurrently, passive cooking time cannot be less than whichever appliance needs the most remaining time ($\max(\text{Stove}, \text{Cooker})$).
2. **Worker Bottleneck**: Active manual prep cannot be automated, so remaining prep time must be added linearly.
3. **Conclusion**: $h(n)$ never overestimates true remaining time $\implies$ **A* Search is guaranteed to be optimal!**

### 🗣️ Speaker Script:
> *"To ensure A* Search finds the exact optimal schedule, we formulated the Hardware Bottleneck Heuristic. Because the Electric Rice Cooker and Gas Stove run concurrently, passive cooking is bounded by whichever appliance needs the most remaining time. Adding manual prep time linearly respects the single-chef constraint. This guarantees that $h(n)$ never overestimates true cost, proving admissibility."*

---

## 🖥️ Slide 9: Kitchen Rule Constraints & 5 Dead-End Failures

### 🎨 Visual Layout:
* Warning Alert Cards (Red borders) showing the 5 Invalid Nodes ($X_1 - X_5$) with $h(n) = \infty$.

### 📝 Slide Content:
* ❌ **Node `X1`**: `[Free, Busy, Free, Raw, Raw, Cooking]` (Arc: `Cook Raw Meat`) $\implies$ **Recipe Ruined!**
* ❌ **Node `X2`**: `[Free, Busy, Free, Raw, Cooking, Raw]` (Arc: `Cook Unwashed Dhal`) $\implies$ **Contaminated Food!**
* ❌ **Node `X3`**: `[Free, Busy, Free, Raw, Cooking, Cooking]` (Arc: `Stove Collision`) $\implies$ **Physical Conflict!**
* ❌ **Node `X4`**: `[Free, Free, Busy, Cooking, Raw, Raw]` (Arc: `Start Dry Rice Cooker`) $\implies$ **Appliance Scorched!**
* ❌ **Node `X5`**: `[Free, Free, Free, Done, Prepped, Prepped]` (Arc: `Serve Uncooked Meal`) $\implies$ **Uncooked Food Served!**

### 🗣️ Speaker Script:
> *"In accordance with the lecture notes, invalid human actions lead to explicit dead-end state nodes with $h(n) = \infty$. When A* Search calculates $f(n) = g(n) + \infty$, it prunes these invalid branches immediately without wasting computational effort."*

---

## 🖥️ Slide 10: Visual State-Space Search Graph (23 Nodes & 3 Goals)

### 🎨 Visual Layout:
* Clean 3-Column Diagram showing the 3 distinct goal paths ($G_1, G_2, G_3$).

### 📝 Slide Content:
* 🟢 **`G1` (41 Minutes)** ⭐:
  `S0` $\xrightarrow{\text{Batch Both (4m)}}$ `S1` $\xrightarrow{\text{Start Rice Cooker (4m)}}$ `S3` $\xrightarrow{\text{Simmer Dhal (3m)}}$ `S6` $\xrightarrow{\text{Dhal/Rice Finish (10m)}}$ `S9` $\xrightarrow{\text{Cook Chicken (20m)}}$ `S12` $\rightarrow$ **Goal G1 (41m)**
* 🟡 **`G2` (67 Minutes)**:
  `S0` $\xrightarrow{\text{Cut Dhal (4m)}}$ `S2` $\xrightarrow{\text{Cut Chicken (4m)}}$ `S4` $\xrightarrow{\text{Start Rice Cooker (4m)}}$ `S7` $\xrightarrow{\text{Simmer Dhal (15m)}}$ `S10` $\xrightarrow{\text{Cook Chicken (20m)}}$ `S13` $\rightarrow$ **Goal G2 (67m)**
* 🟠 **`G3` (100 Minutes)**:
  `S0` $\xrightarrow{\text{Cut Dhal (4m)}}$ `S2` $\xrightarrow{\text{Simmer Dhal (15m)}}$ `S5` $\xrightarrow{\text{Cut Chicken (4m)}}$ `S8` $\xrightarrow{\text{Cook Chicken (20m)}}$ `S11` $\xrightarrow{\text{Cook Rice Stove (25m)}}$ `S14` $\rightarrow$ **Goal G3 (100m)**

### 🗣️ Speaker Script:
> *"Our search space contains 23 nodes organized into 3 distinct goal paths. Goal G1 is the 41-minute optimal path utilizing batch prep and dual-appliance parallelism. Goal G2 is a 67-minute sequential path. Goal G3 is a 100-minute path where rice is cooked on the gas stove without using the rice cooker."*

---

## 🖥️ Slide 11: Search Visualization Tool Setup (`aisearch-v2`)

### 🎨 Visual Layout:
* Screenshot of `http://localhost:8000` showing `simple_multistate_cooking.json` loaded on the visualizer canvas.

### 📝 Slide Content:
* **Visualization Platform**: AI Algorithm Visualizer v2.0 (`aisearch-v2`)
* **Environment**: Local Web Server running on Port 8000
* **Data File**: `simple_multistate_cooking.json`
* **Features**: Live step-by-step traversal, OPEN/CLOSED list inspector, high-contrast multiline text pills.

### 🗣️ Speaker Script:
> *"To analyze search behavior dynamically, we integrated our graph into the AI Algorithm Visualizer v2.0 running on a local web server. The tool allows us to observe node expansions, frontier queues, and path costs in real time."*

---

## 🖥️ Slide 12: Uninformed Search Algorithm Traces

### 🎨 Visual Layout:
* 2-Column Comparison showing BFS vs. DFS vs. UCS search trees.

### 📝 Slide Content:
* 🔵 **Breadth-First Search (BFS)**:
  * Path Found: Goal 2 (`G2`) | Cost: **67 mins** | Nodes Explored: **11**
  * *Flaw*: Counts edge hops rather than time in minutes!
* 🔴 **Depth-First Search (DFS)**:
  * Path Found: Goal 3 (`G3`) | Cost: **100 mins** | Nodes Explored: **14**
  * *Flaw*: Wanders into dead ends like $X_4$ and gets trapped in deep branches.
* 🟣 **Uniform Cost Search (UCS)**:
  * Path Found: Goal 1 (`G1`) | Cost: **41 mins** | Nodes Explored: **10**
  * *Strength*: Globally optimal ($h=0$), but explores 10 nodes unnecessarily.

### 🗣️ Speaker Script:
> *"Looking at uninformed search algorithms: BFS finds a 67-minute suboptimal path because it counts edge hops instead of minutes. DFS gets trapped in deep branches and returns a 100-minute path. Uniform Cost Search finds the optimal 41-minute path, but expands 10 nodes because it lacks heuristic guidance."*

---

## 🖥️ Slide 13: Informed Search & A* Optimality

### 🎨 Visual Layout:
* Highlighted side-by-side card comparing Greedy Best-First Search vs. A* Search.

### 📝 Slide Content:
* 🟠 **Greedy Best-First Search (GBFS)**:
  * Evaluates: $f(n) = h(n)$
  * Cost: **41 mins** | Nodes Explored: **5**
  * *Risk*: Fast, but can be misled by greedy local choices on complex graphs.
* ⭐ **A* Search (Optimal)**:
  * Evaluates: $f(n) = g(n) + h(n)$
  * Cost: **41 mins** ⭐ | Nodes Explored: **5** ⭐
  * *Advancement*: Prunes dead ends ($X_1-X_5$) and guarantees global optimality!

### 🗣️ Speaker Script:
> *"Informed search algorithms utilize our bottleneck heuristic. Both Greedy Best-First and A* Search navigate directly to Goal G1 in just 5 node expansions. However, only A* Search mathematically guarantees global optimality on any arbitrary graph topology."*

---

## 🖥️ Slide 14: Comparative Benchmark Summary Table

### 🎨 Visual Layout:
* Master comparison table matching the senior PDF benchmark format.

### 📝 Slide Content:

| Search Algorithm | Category | Path Cost $g(G)$ | Nodes Explored | Goal Found? | Optimality |
| :--- | :--- | :---: | :---: | :---: | :--- |
| ⭐ **A* Search** | Informed | **41 mins** | **5** | ✅ Yes | **Globally Optimal & Most Efficient** |
| 🟠 **Greedy Best-First** | Informed | **41 mins** | **5** | ✅ Yes | Fast (Non-optimal generally) |
| 🟣 **Uniform Cost (UCS)** | Uninformed | **41 mins** | 10 | ✅ Yes | **Globally Optimal** (Slow) |
| 🔵 **BFS** | Uninformed | 67 mins | 11 | ✅ Yes | Suboptimal (Hop count) |
| 🔴 **DFS** | Uninformed | 100 mins | 14 | ✅ Yes | Suboptimal (Trapped in deep path) |
| 🟡 **IDS** | Uninformed | 67 mins | 16 | ✅ Yes | Suboptimal (Re-explores levels) |

### 🗣️ Speaker Script:
> *"This summary table compares all 6 algorithms. As shown, A* Search achieved the minimum cost of 41 minutes with the fewest node expansions (only 5 nodes), demonstrating its superiority over uninformed and greedy approaches."*

---

## 🖥️ Slide 15: Conclusion & Key Findings

### 🎨 Visual Layout:
* 3 Bullet Cards summarizing real-world impact and academic contributions.

### 📝 Slide Content:
1. **Parallel Resource Scheduling**: Modeling background appliances (Electric Rice Cooker) alongside a shared gas stove saves **26 minutes** compared to sequential cooking ($41\text{m}$ vs $67\text{m}$).
2. **Heuristic Admissibility**: The Hardware Bottleneck Max formula $h(n) = \max(\text{Stove}, \text{Cooker}) + \text{Prep}$ guarantees $h(n) \le h^*(n)$.
3. **Pruning Efficiency**: Assigning $h(n) = \infty$ to constraint violations allows A* to prune invalid dead ends without computational waste.

### 🗣️ Speaker Script:
> *"In conclusion, our project proves that state-space search can effectively solve complex real-world kitchen scheduling problems. By modeling dual-appliance parallelism and using an admissible bottleneck heuristic, A* Search finds the 41-minute optimal schedule, saving 26 minutes over sequential execution."*

---

## 🖥️ Slide 16: Q&A / Viva Defense Cheat-Sheet & Thank You

### 🎨 Visual Layout:
* Clean "Thank You!" layout with student contact details and key Viva defense formulas.

### 📝 Slide Content:
* **Thank You!**
* **Feel free to ask any questions...**
* **Quick Reference Formulas for Viva**:
  * State Tuple: $S = [H, S, RC, R, D, C]$ (6 components)
  * Heuristic: $h(n) = \max(\text{Stove}, \text{Cooker}) + \text{Prep}$
  * Optimal Cost: $g(G_1) = 41\text{ mins}$ | Nodes Explored: $5$

### 🗣️ Speaker Script:
> *"Thank you for your time and attention. I am now open to any questions."*
