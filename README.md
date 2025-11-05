# Integrated Supply Planning for a Manufacturing Company | Excel Solver  

**Author:** Bùi Xuân Bảo Duy (Kelvin)  
**Date:** September 2025  
**Tools Used:** Excel Solver  

## 🗂️ Table of Contents
1️⃣ [Context](#context)  
2️⃣ [Problem Description & Parameters](#problem-description--parameters)  
3️⃣ [Model Formulation](#model-formulation)  
4️⃣ [Solution Approach](#solution-approach)  
5️⃣ [Visualization](#visualization)  
6️⃣ [Insights & Recommendations](#insights--recommendations)  

---

## 1️⃣ Context

📘 **Main Context**  

- The company is a **gardening tools manufacturer** with **peak demand** occurring in **spring**, creating strong **seasonality in sales**.
- It operates in a **highly competitive market** where **efficient production and inventory management** are critical to sustain **customer satisfaction** and **cost efficiency**.
- At the start of the planning horizon (**January**), the company has **1,000 units of inventory** and **80 workers**.
- To respond to demand fluctuations, management can consider multiple options: **hiring or laying off workers**, **allocating overtime**, **subcontracting**, **backordering** in peak periods, or **carrying inventory** in low-demand months.
- No strict limits are imposed on **subcontracting, inventory, or backorders**, but all generate associated **costs** that must be accounted for.
- The **Supply Planning team** is tasked with building a **6-month aggregate production plan (Jan–Jun)** using **Excel/Solver** that:
  - Ensures **demand fulfillment** while complying with **operational constraints**.  
  - Compares alternative **strategies** (e.g., **Chase** vs. **Level**) to evaluate **cost trade-offs**.  
  - Reflects different **constraints/targets** depending on the chosen strategy (e.g., **ending inventory target** for **Chase**, **workforce stability** for **Level**).  
- **Objective:** **Minimize total operating cost** while achieving **service level targets** and supporting **efficient resource utilization**.

👥 **Target Audience**  
- Supply Planning & Production Planning Teams  
- Operations & Manufacturing Managers  
- Decision-makers & Stakeholders  

🎯 **Objective**  
To design a **supply planning model** that ensures **demand fulfillment, prevents stockouts, minimizes costs** (inventory, production, overtime, subcontracting), and improves **resource utilization** — ultimately supporting operational efficiency and customer satisfaction.  

🛠️ **Tools Used**  
- **Excel:** Used to input data and calculate costs (**inventory, workforce, hiring/layoff, overtime, subcontracting, etc.**) through direct formulas  

- **Excel Solver:** Used to test different planning strategies (**Chase vs. Level**) and determine the **optimal production–workforce plan** under constraints (**no stockouts, ending inventory requirement, limited overtime, etc.**)

---

## 2️⃣ Problem Constraints & Parameters  
### 📖 Definition
- **Chase Plan**: A production strategy where output is adjusted each period to match demand, keeping inventory minimal but causing frequent workforce changes.
- **Level Plan**: A production strategy where output is kept constant over time, stabilizing the workforce but leading to inventory build-up when demand fluctuates.

### ⏳ Planning Horizon  
- 6 months: **Jan – Jun**  

### ⚙️ Parameters

| **Object**              | **Qty** | **Unit**           |
| ----------------------- | ------: | ------------------ |
| **Inventory Holding Cost**  |       2 | USD/unit/month     |
| **Stockout Cost**           |       5 | USD/unit/month     |
| **Hiring Cost**             |     300 | USD/worker         |
| **Regular-Time Salary**     |     640 | USD/worker/month   |
| **Layoff Cost**             |     500 | USD/worker         |
| **Subcontracting Cost**     |      30 | USD/unit           |
| **Overtime Cost**           |       6 | USD/hour           |
| **Available Regular Time**  |     160 | hours/month/worker |
| **Maximum Overtime**        |      10 | hours/month/worker |
| **Starting Inventory**      |   1,000 | units              |
| **Ending Inventory Target** |     500 | units              |
| **Starting Workforce**      |      80 | workers            |
| **Material Cost**           |      10 | USD/unit           |
| **Net Production Time**     |       4 | hours/unit         |
| **Working Day**             |      20 | days/month         |
| **Working Hour**            |       8 | hours/day/worker   |

### 📋 Specific Business Rules  

- ⚡ **Chase Plan**  
  - Do not create any inventory from **Jan to May**, ending inventory of **June = 500**  
  - Do not have any **stockout**  
  - Do not use any **overtime** and **subcontracting**  
  - Try to produce what is demanded (**production closely follows monthly demand; workforce can be adjusted via hiring/layoff**)  

- 📊 **Level Plan**  
  - Do not use any **overtime** and **subcontracting**  
  - Produce the **same quantity** every period (**fixed production level each month**)  
  - **Carrying inventory** is allowed  
  - Do not create any **stockout**  
  - Aim to **minimize required capacity** (minimize number of workers needed given fixed production rate)  

### 🔎 Outputs Required  
- **Month-by-month table**: Gross Demand, Net Demand, Production, Workers Employed, Workers Hired, Workers Laid off, On-hand Inventory, Overtime, Subcontract, Stockout, Total Cost  
- **Scenario comparison**: Total cost breakdown (material, holding, hiring/layoff, salary, OT/subcontract if used, stockout)  
- **Charts**: Demand vs Production, Workforce trend, Inventory level, Cost breakdown

---

## 3️⃣ Model Formulation
### ❓ Why Excel Solver  
- **Excel Solver** = optimization tool: It finds the **best outcome** (e.g., minimize cost, maximize profit) for a given problem in Excel.
- **Concept**: Based on three elements – **objective function** (goal), **decision variables** (controllable inputs), and **constraints** (limitations). Solver adjusts the variables to optimize the goal.
- **Meaning**: Enables **decision-making** in real-world scenarios (production planning, workforce allocation, inventory management) **without the need for coding**.

### 🏗️ Planning Model  

- Each **row** = 1 month (**Jan–Jun**)  
- Each **column** = Demand, Workforce, Production, Inventory, Costs  

#### 🔹 Inputs: 
- Gross Demand (i): number of gross demand needed in period i.    
- Parameters

#### 🔹 Decision Variables
- Workers Hired(i): number of workers hired in period i.  
- Workers Laid off(i): number of workers laid off in period i.
- Overtime(i): number of units produced through overtime in period i.
- Subcontract(i): number of units subcontracted in period i.
- Stockout(i): number of backlogged (unmet) demand units carried to next period from period i.

#### 🔹 Key Formulas

| **Description**                          | **Formula**                                                                                                                                                                                                                                                               | **Explanation**                                                                                                                                                                                           |
| ---------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Net demand in period *i***             | `Net Demand(i) = Gross Demand(i) – Inventory(i–1)`                                                                                                                                                                                                                        | The actual demand that must be met in period *i* after accounting for available inventory from the previous period.                                                                                      |
| **Production quantity in period *i***    | `Production(i) = WorkersEmployed(i) × WorkingDay × WorkingHour ÷ NetProductionTime`                                                                                                                                                                                       | Calculates how many units can be produced in period *i* based on the number of workers, working days, hours per day, and standard production time per unit.                                              |
| **Workers employed in period *i***       | `WorkersEmployed(i) = Workers(i–1) + Hired(i) – LaidOff(i)`                                                                                                                                                                                                               | Tracks the workforce level by updating last period’s workers with hires and layoffs in the current period.                                                                                               |
| **Inventory balance at end of period *i*** | `On-hand Inventory(i) = Inventory(i–1) + Production(i) + Overtime(i) + Subcontract(i) + Stockout(i) – Stockout(i–1) – Gross Demand(i)`                                                                                                                                    | Determines how much inventory remains at the end of the period by adding supply sources (production, overtime, subcontract, backlog carried in) and subtracting demand plus any backlog carried forward. |
| **Total cost in period *i***             | `Total Monthly Cost(i) = Production(i) × MaterialCost + WorkersEmployed(i) × RegularTimeSalary + Hired(i) × HiringCost + LaidOff(i) × LayoffCost + Inventory(i) × HoldingCost + Overtime(i) × OvertimeCost + Subcontract(i) × SubcontractCost + Stockout(i) × StockoutCost` | Computes the total operating cost by summing up material, labor, hiring, layoff, inventory, overtime, subcontracting, and stockout costs in the period.                                                  |


#### 🔹 Solver Objective
- **Minimize**: TotalCost = Cost(Jan) + Cost(Feb) + Cost(Mar) + Cost(Apr) + Cost(May) + Cost(Jun)
- Ensure all constraints defined by the chosen plan are satisfied.  

## 4️⃣ Solution Approach  
### ⚡ Chase Plan  

#### 🧮 Supply Planning with Excel & Solver

The supply planning problem for Chase Plan is addressed using **Excel formulas** and **Solver optimization**.  
The approach consists of the following steps:

#### 📝 Step 1. Model Setup in Excel
- Build a month-by-month planning table (Jan–Jun).
- Input fixed data: gross demand, cost parameters, initial workforce, and inventory.
- Define **decision variable cells**: Production, Workers Employed, Hired, Laid off, Inventory (highlighted in the table).
- Link all other cells (Net Demand, Workforce Balance, Inventory Balance, Total Costs) using formulas.

![Images](https://github.com/kelvinduybui/Supply-Planning/blob/main/Images/Chase%20initial.png?raw=true)

#### ⚙️ Step 2. Define Constraints  
Constraints differ by planning strategy:  
- No inventory from Jan–May; ending inventory of Jun = 500 units.
- No stockout allowed.
- No overtime and subcontracting.
- Production closely follows monthly demand (workforce adjusts via hiring/layoff).

![Image](https://github.com/kelvinduybui/Supply-Planning/blob/main/Images/Chase%20Solver.png?raw=true)  

#### 📈 Step 3. Solver Result
- **Objective**: Minimize total cost across the 6-month horizon.  
- **Decision Variables**: Production, Workforce Hired, Workforce Laid off, Workforce Employed, Inventory.  
- **Constraints**: As defined above for Chase plan.  
- **Solver Method**: *Simplex LP* (suitable for linear relationships in the model).

![Image](https://github.com/kelvinduybui/Supply-Planning/blob/main/Images/Chase%20result.png?raw=true)

### 📊 Level Plan:  

#### 🧮 Supply Planning with Excel & Solver

The supply planning problem for Level Plan is addressed using **Excel formulas** and **Solver optimization**.  
The approach consists of the following steps:

#### 📝 Step 1. Model Setup in Excel
- Build a month-by-month planning table (Jan–Jun).
- Input fixed data: gross demand, cost parameters, initial workforce, and inventory.
- Define **decision variable cells**: Production, Workers Employed, Hired, Laid off, Inventory (highlighted in the table).
- Link all other cells (Net Demand, Workforce Balance, Inventory Balance, Total Costs) using formulas.

![Images](https://github.com/kelvinduybui/Supply-Planning/blob/main/Images/Level%20initial.png?raw=true)

#### ⚙️ Step 2. Define Constraints  
Constraints differ by planning strategy:  
- Allow carrying inventory from Jan–May
- No stockout allowed.
- No overtime and subcontracting.
- Produce the same quantity in every period.

![Image](https://github.com/kelvinduybui/Supply-Planning/blob/main/Images/Chase%20Solver.png?raw=true)  

#### 📈 Step 3. Solver Result
- **Objective**: Minimize total cost across the 6-month horizon.  
- **Decision Variables**: Production, Workforce Hired, Workforce Laid off, Workforce Employed, Inventory.  
- **Constraints**: As defined above for Level plan.  
- **Solver Method**: *Simplex LP* (suitable for linear relationships in the model).

![Image](https://github.com/kelvinduybui/Supply-Planning/blob/main/Images/Level%20result.png?raw=true)

## 5️⃣ Visualization
### Net Demand vs Production
![Image](https://github.com/kelvinduybui/Supply-Planning/blob/main/Images/Net%20Demand%20vs%20Production.png?raw=true)

#### Key Takeaways:
- ⚡ **Chase Plan:** **Production** closely **follows demand** across months, ranging from 600 units in January to 2,700 units in June, ensuring output always matches actual requirements.
- 📊 **Level Plan:** Production is **fixed** at 1,425 units each month, while **net demand fluctuates widely**, creating a clear difference between constant supply and variable demand.

### Onhand Inventory
![Image](https://github.com/kelvinduybui/Supply-Planning/blob/main/Images/Onhand%20Inventory.png?raw=true)

#### Key Takeaways:
- ⚡ **Chase Plan:** Keeps **inventory minimal**, starting with 1,000 units and ending with only 500 units.
- 📊 **Level Plan:** Inventory builds up steadily from 1,000 → 7,175 units as **constant production** exceeds demand in low-demand months.

### Workforce
![Image](https://github.com/kelvinduybui/Supply-Planning/blob/main/Images/Workforce.png?raw=true)

#### Key Takeaways:
- ⚡ **Chase Plan:** **Workforce fluctuates** sharply with continuous hiring and layoffs (layoff 65 in Jan, hire 80 from Feb–Apr, layoff 40 in May, hire 13 in Jun), leading to high labor cost.
- 📊 **Level Plan:** **Stable workforce** maintained at 36 workers (after laying off 44 in Jan), ensuring no further changes throughout the period.

### Cost
![Image](https://github.com/kelvinduybui/Supply-Planning/blob/main/Images/Cost%20distribution.png?raw=true)

#### Key Takeaways:
- ⚡ **Chase Plan**:
  - Cost is dominated by worker employed (51%) and production (32%), with significant shares from layoff (11%) and hiring (6%), reflecting **heavy workforce fluctuation**.
  - **Cost driven by labor**: **Total cost = 484,250 USD**, mainly from **hiring/layoff expenses** and **regular-time salaries**, with **no overtime, subcontracting, or stockouts**.

- 📊 **Level Plan**:
  - Cost is concentrated in worker employed (48%) and production (30%), with additional burden from inventory holding (14%) and layoff (8%), reflecting **stable workforce but high inventory**.
  - **Cost structure:** Total cost = **284,088 USD**, mainly from **regular-time salaries, initial layoff cost, and rising inventory holding cost** (no overtime, subcontracting, or stockouts).

## 6️⃣ Insights & Recommendations
### Insights

#### Cost Structure Trade-off
⚡ **Chase Plan**: Total cost = **484,250 USD**.  
- ✅ **Advantage**: Almost **no inventory** → inventory cost nearly zero.  
- ❌ **Drawback**: **Workforce fluctuates** heavily → high hiring/layoff costs and labor management challenges.  

📊 **Level Plan**: Total cost = **284,088 USD**.  
- ✅ **Advantage**: **Stable workforce** with minimal fluctuation → easier labor management.  
- ❌ **Drawback**: **Inventory builds up** significantly → high inventory holding cost.  

#### ⚙️ Operational Feasibility

⚡ **Chase**: High **HR management risks** (continuous layoffs & hiring → lower employee morale, higher training costs).  

📊 **Level**: High **financial and storage risks** (large inventory, potential obsolescence, especially for short product life cycles).  

### Recommendations
- If the company prioritizes **labor stability** → lean toward **Level plan**.
- If the company prioritizes **inventory minimization & working capital** efficiency → lean toward **Chase plan**.
- However, **Pure Chase or Level plan** is not optimal → A **hybrid approach** is preferable: e.g., maintain a leveled production rate around the average demand + allow some overtime/subcontracting to cover peak demand reduces inventory without excessive workforce fluctuation.
