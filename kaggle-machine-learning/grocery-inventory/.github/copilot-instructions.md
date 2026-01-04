# Copilot Instructions for grocery-inventory 🧾🔧

Purpose
- Short, actionable guidance so an AI coding assistant can be productive quickly in this repository.

Quick context
- Small EDA project using a single notebook: `src/eda.ipynb`.
- Raw data: `data/raw/grocery-inventory.csv` (checked into repo). Data folder contains a `.gitignore` and uses `.gitkeep`.
- Typical stack: pandas, numpy, matplotlib, seaborn, Jupyter.

Quick start (what to run locally) ✅
- Create an environment and install packages the notebook requires (example):

```bash
python -m venv .venv
source .venv/bin/activate
pip install pandas numpy matplotlib seaborn jupyterlab
jupyter lab   # open and run `src/eda.ipynb`
```

Repository-specific patterns & important details 🔎
- File to inspect first: `src/eda.ipynb` — most logic and transformations live in a single notebook.
- Data live in `data/raw/grocery-inventory.csv` and are read directly by `src/eda.ipynb` using an absolute path. Prefer changing to a relative path or a helper loader function for portability.
- Dates are stored in mm/dd/YYYY. The notebook converts them with `pd.to_datetime(..., format='%m/%d/%Y', errors='coerce')` — keep that format when parsing.
- `Unit_Price` values include a leading `$` and should be converted to float: `df['Unit_Price'] = df['Unit_Price'].str.replace('$','').astype(float)`.
- Percentage column includes a `%` and is converted with: `df['percentage'] = df['percentage'].str.replace('%','').astype(float) / 100.0`.
- Derived columns used in the notebook:
  - `Order_Received_Diff` = (Last_Order_Date - Date_Received).dt.days
  - `Shelf_Life_Days` = (Expiration_Date - Date_Received).dt.days

Concrete examples & fixes (apply directly in the notebook) 🔧
- Column selection bug: a code cell currently uses `df['Product_Name', 'Category', ...]` which is invalid. Use double brackets:

```python
# WRONG
# df['Product_Name', 'Category', ...]

# CORRECT
cols = ['Product_Name','Category','Warehouse_Location',
        'Last_Order_Date','Date_Received','Order_Received_Diff',
        'Expiration_Date','Shelf_Life_Days','Status']
df_product_date_status = df[cols].sort_values(by=['Product_Name','Last_Order_Date'], ascending=True)
```

- Typo to watch for: `Expriration_Date` (misspelling) appears in the notebook — use `Expiration_Date` consistently.

Known gotchas & constraints ⚠️
- Notebook uses an absolute filesystem path to open the CSV. This will break on other machines; switch to relative paths or a config variable.
- No `requirements.txt` or `environment.yml` present — add one when upgrading the project so reproducibility improves.
- No unit tests or CI config detected. If adding code (non-notebook helpers), include small tests (e.g., for date parsing and currency/percentage conversion) and add them to a minimal test runner config.

How to make safe PRs here ✅
- Prefer adding small Python modules (e.g., `src/data_loader.py`) and unit tests rather than making large edits inside the notebook.
- When modifying the notebook, add a short markdown cell documenting the change and include a runnable code cell that demonstrates transformation outcomes (use `.head()` for samples).
- If changing data paths, update any cells that assume absolute paths and add a small helper that resolves `data/raw/grocery-inventory.csv` relative to the repo root.

Files to reference for examples
- `src/eda.ipynb` — primary source of logic and transformations
- `data/raw/grocery-inventory.csv` — canonical sample dataset

When to ask the repo owner
- If you intend to change column names that downstream code (or notebooks) assume, confirm with the owner before mass renames (e.g., `Catagory` → `Category`).

If anything in this doc is unclear or you want me to expand an area (example tests, loader helper, or small refactor PR), tell me which part to prioritize.