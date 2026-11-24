# HR Analytics & Employee Performance

A Python ETL pipeline that loads, cleans and merges three HR datasets into one master dataframe, then adds rule-based metrics (bonus, promotion, demotion, cost totals) for HR reporting.

![Python](https://img.shields.io/badge/Python-3.x-blue?style=flat-square&logo=python) ![Pandas](https://img.shields.io/badge/Library-Pandas%20%7C%20NumPy-green?style=flat-square) ![Jupyter](https://img.shields.io/badge/Notebook-Jupyter-orange?style=flat-square&logo=jupyter)

## Business Questions Answered

1. **Project outcomes:** Which employees are linked to finished vs. failed projects?
2. **Cost attribution:** What is the total project cost for each employee?
3. **Promotion and demotion:** Whose designation changes, based on age and project status?
4. **Bonus distribution:** How much bonus is paid on finished projects?
5. **Geographic filter:** Which employees work in cities whose name contains the letter "o"?

## 💡 Key Outputs

| Output | Detail |
|---|---|
| Master dataframe | One table merging employees, projects and seniority |
| Bonus column | 5% of project cost for projects with status "Finished" |
| Designation changes | Failed project: designation -1. Age above 29: designation +1 |
| Per-employee cost totals | Total project cost for each employee (grouped by ID and first name) |
| City filter | Employees whose city name contains "o" |

## Designation Rule

Higher designation number = more senior.
- Employees on a **failed** project are demoted: designation **-1**
- Employees with **Age > 29** (30 and older) are promoted: designation **+1**

## 🔧 ETL Pipeline

```
Raw data (3 .xls files)
        │
        ▼
[1] Load & Inspect
    • Read Employee.xls, Project.xls, Seniority.xls into DataFrames
    • Check data types, null counts, shape
        │
        ▼
[2] Clean & Transform
    • Fill missing Cost values with a running average (for-loop)
    • Split full name into First Name and Last Name
    • Add Mr./Mrs. prefix based on Gender, then drop Gender
        │
        ▼
[3] Merge
    • LEFT JOIN: employees + projects on employee ID
    • LEFT JOIN: result + seniority on employee ID
    • Result: master dataframe (Final)
        │
        ▼
[4] Feature Engineering
    • Bonus = 5% of Cost WHERE Status = 'Finished'
    • Designation -= 1 WHERE Status = 'Failed'
    • Designation += 1 WHERE Age > 29
    • Total Cost = SUM(Cost) GROUP BY ID, First Name
        │
        ▼
[5] Filter
    • Employees whose city name contains 'o'
```

## Tasks Completed

| # | Task | Technique |
|---|---|---|
| 1 | Created 3 DataFrames from the raw XLS files | `pd.read_excel()` |
| 2 | Filled missing cost values | Running average loop |
| 3 | Split full name into First and Last name | `str.split()` |
| 4 | Merged the 3 DataFrames into one master | `pd.merge()` (LEFT JOIN) |
| 5 | Added bonus column (5% for finished projects) | `np.where()` |
| 6 | Demoted designation for failed projects | Boolean mask + `.loc` update |
| 7 | Added Mr./Mrs. prefix and dropped Gender | `np.where()`, `df.drop()` |
| 8 | Promoted designation for employees aged over 29 | Boolean mask + `.loc` update |
| 9 | Computed total project cost per employee | `groupby().sum()` |
| 10 | Filtered employees by city name containing 'o' | `str.contains()` |

## 💻 Sample Code

```python
import pandas as pd
import numpy as np

# Bonus: 5% of project cost for finished projects only
Final['Bonus'] = np.where(Final['Status'] == 'Finished', Final['Cost'] * 0.05, 0)

# Demotion: failed projects
Final.loc[Final['Status'] == 'Failed', 'Designation'] -= 1

# Promotion: employees aged over 29
Final.loc[Final['Age'] > 29, 'Designation'] += 1

# Total project cost per employee
TotalProjCost = Final.groupby(["ID", "First Name"])["Cost"].sum().reset_index()
TotalProjCost = TotalProjCost.rename(columns={"Cost": "Total Cost"})
```

## 📁 Repository Structure

```
HR-Analytics-Capstone-Python/
├── HR Employee Project data Analytics.ipynb   # Annotated Jupyter Notebook
├── Employee.xls                               # Employee details
├── Project.xls                                # Project assignments and costs
├── Seniority.xls                              # Designation levels
└── README.md
```

## ⚙️ How to Run

1. Install the libraries:
   ```
   pip install pandas numpy xlrd jupyter
   ```
2. Keep `Employee.xls`, `Project.xls` and `Seniority.xls` in the same folder as the notebook.
3. Open `HR Employee Project data Analytics.ipynb` in Jupyter.
4. Run **Kernel → Restart & Run All**. The tasks run in order, 1 to 10.

## Data

| File | Rows | Columns | Fields |
|---|---|---|---|
| Employee.xls | 5 | 6 | ID, Gender, City, Age, First Name, Last Name |
| Project.xls | 14 | 4 | ID, Project, Cost, Status |
| Seniority.xls | 5 | 2 | ID, Designation Level |

Source: Skillovilla

## Sample Output
| ID   | First Name   |   Total Cost |
|:-----|:-------------|-------------:|
| A001 | Mr. John     | 5,212,312.50 |
| A002 | Mrs. Alice   | 2,680,000.00 |
| A003 | Mr. Tom      | 5,150,000.00 |
| A004 | Mrs. Nina    | 9,500,000.00 |
| A005 | Mrs. Amy     | 3,850,500.00 |

## 🛠 Tools & Libraries

| Tool | Purpose |
|---|---|
| Python 3.x | Core language |
| Pandas | DataFrames, merging, groupby, filtering |
| NumPy | Conditional column logic (`np.where`) |
| xlrd | Reading `.xls` files |
| Jupyter Notebook | Annotated, interactive analysis |

## 🎓 Skills Demonstrated

- **Multi-source data integration:** merged three separate HR files into one table
- **Data cleaning:** filled missing values, split name fields, fixed formats
- **Feature engineering:** built bonus, promotion and demotion columns from business rules
- **Aggregation and filtering:** grouped cost totals and filtered by text patterns
