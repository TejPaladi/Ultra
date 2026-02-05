# Knapsack-Style Team Formation + Meal Bundle Recommendation (Notebooks)

This repo contains a set of Jupyter notebooks that explore **set-coverage / knapsack-style selection** using two domains:

1) **Research team formation for proposals** (researchers ↔ skills, proposals ↔ required skills)  
2) **Meal bundle recommendation** (meals ↔ categories, user requirements ↔ required categories)

The core idea across notebooks is **selecting a small set of items under a budget** to maximize **coverage** (and optionally reward **redundancy**).

---

## Repository contents

### Notebooks
- **`knapsack_v1.ipynb`**  
  *Version 1 baseline (coverage-first greedy set cover)*  
  Builds a team by repeatedly adding the person who contributes the most **new** required skills, stopping when:
  - coverage reaches 100% (value = 1.0), or
  - no candidate adds any new skills.

- **`exact_knapsack_ilp_from_uploaded_skills.ipynb`**  
  *Exact “knapsack” / budgeted maximum coverage (oracle baseline)*  
  Selects up to **K** people to maximize **weighted coverage** of proposal skills.  
  Uses ILP if available (PuLP), otherwise uses a **small exact brute-force fallback** on a restricted candidate pool.

- **`dynamic_knapsack_Mariginal_utility.ipynb`**  
  *Dynamic marginal-utility knapsack (scalable main method)*  
  Greedy selection under team-size budget **K**, but with:
  - **diminishing returns** for redundant skill coverage (robustness)
  - optional **adaptive seat cost** to prevent bloated teams

- **`meal_reco.ipynb`**  
  *Same dynamic marginal-utility method applied to meals*  
  Meals are “items”, categories are “skills”, user requirements are “proposal required skills”.  
  Outputs a bundle of **K meals** per user/occasion and evaluates bundles using the same goodness metric.

---

## Data format

### Teaming data
- `large_researcher_skills.csv`
  - expected columns:
    - `researcher_name`
    - `skills` (string representation of a Python list/set)
- `large_proposal_skills.csv`
  - expected columns:
    - `nsf_proposal_links_v0` (or similar ID/link column)
    - `skills` (string representation of a Python list/set)

### Meal data
- `user_meal_requirements.csv`
  - expected columns:
    - `user_id`
    - `meal_occasion`
    - `required_categories` (string representation of a Python list/set)
- `meal_categories.csv`
  - expected columns:
    - `meal_name`
    - `categories` (string representation of a Python list/set)

> Note: If your column names differ, each notebook has a small section where you can update the column-name variables.

---

## Goodness metric (M1 ultra metric)

All notebooks can evaluate the selected teams/bundles using:

```python
M1.apply_ultra_metric(req_skills, team, pseudo_skills_map)
