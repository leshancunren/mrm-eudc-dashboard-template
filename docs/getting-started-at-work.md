# Getting started in the company workspace

How to bring this template into a company environment with VS Code and an approved AI assistant, connect it to real data, and produce the quarterly MRMC and Board pack, without any company data leaving the company.

## Ground rules

1. **The public repository is read-only for you at work.** Clone from it; never push to it. Real data, real model names, real limits and internal commentary stay in internal systems.
2. **Share structure, not records, with the AI** unless your firm has approved the assistant for confidential data. Column headers, data types and made-up example rows are enough for it to write code.
3. **Review everything the AI writes** before you run it on real data, and check the output against the build spec.
4. **Register the result as an EUA** before it is used for MRMC or Board reporting.

## Step 1. Clone the template

In VS Code: `Ctrl+Shift+P` → **Git: Clone** → paste the repository URL:

```
https://github.com/<your-github-username>/mrm-eudc-dashboard-template.git
```

Choose a folder in an approved location, such as your work drive or a team share, not a personal folder.

If git access to GitHub is blocked by the proxy, open the repository page in the browser instead, choose **Code → Download ZIP**, and unzip it into the approved folder.

## Step 2. Cut the link to the public repository

Run these in the VS Code terminal from the project folder, so nothing can be pushed back to GitHub by accident:

```bash
git remote rename origin upstream
git remote set-url --push upstream DISABLED
```

If your team has an internal repository (Azure DevOps, GitLab, GitHub Enterprise, Bitbucket), add it as the new home:

```bash
git remote add origin <internal-repo-url>
git push -u origin main
```

The template's `.gitignore` already excludes `data/private/`, `output/` and Excel files, so real extracts kept there are never committed. Check it is in place before you add any real data.

## Step 3. Look at the sample

Open `dashboard/index.html` in your browser by double-clicking it, or use VS Code's Live Preview extension. Everything you see comes from the sample CSV blocks inside that file.

## Step 4. Work with the AI assistant

Open the project folder in VS Code so the assistant can see the files. In GitHub Copilot Chat, reference files with `#file:` (for example `#file:docs/build-spec.md`); other assistants accept the file by drag-and-drop or `@`-mention. Use these prompts in order, adjusting the wording to your tool.

### 4a. Orientation (no changes yet)

```
Read docs/build-spec.md and dashboard/index.html. Summarize:
(1) the six input tables and their key fields,
(2) the business rules V1–E3 and the KRI rule K1,
(3) where the CSV data blocks sit in index.html and how the page reads them.
Do not change any files yet.
```

### 4b. Map our data to the template

Paste column headers and data types only, not records.

```
Here are the column headers and data types from our model inventory export:
<paste headers>
And from our findings tracker:
<paste headers>
And from our EUA register:
<paste headers>

Map each to the models, findings and euas tables in docs/build-spec.md.
Produce a mapping table: template field | our field | transformation needed.
List template fields we cannot fill and suggest how to derive them using
rules V1, V2, R1, F1, E1 and E2. Flag any assumption you make.
```

### 4c. Write the data-preparation script

```
Write a Python script, prep_data.py, using pandas, that:
- reads our extracts from data/private/ (file names: <list>),
- applies the column mapping we agreed,
- computes next_validation_due, validation_status, residual_risk,
  finding status and days, controls_compliant and review_status exactly as
  rules V1, V2, R1, F1, E1 and E2 in docs/build-spec.md define them,
- takes the as-of date as a command-line argument,
- writes data/models.csv, data/findings.csv and data/euas.csv with exactly the
  columns and order in the data dictionary.
Put each rule in its own small function with a docstring citing the rule ID.
```

### 4d. Test the rules against the sample

The sample CSVs were generated with these rules as of 2026-09-30, so they work as expected results.

```
Write pytest tests for prep_data.py. For each rule function, feed it the rows in
the sample data/models.csv, data/findings.csv and data/euas.csv with as-of
date 2026-09-30, and assert that it reproduces the stored values of
validation_status, residual_risk, status, days_open, days_past_due,
controls_compliant and review_status. Add edge cases: a due date exactly on the
as-of date, exactly 90 days out, and a High-risk EUA missing one control.
```

### 4e. Add the quarter to the KRI history

```
Extend prep_data.py so it computes the seven KRIs in the KRI logic table
from the prepared tables and appends one row for the quarter to data/trend.csv.
It must refuse to overwrite a quarter that already exists unless --force is given.
eua_unregistered_found comes from a separate discovery-scan count I will pass as
an argument.
```

### 4f. Build the dashboard file

```
Write build_dashboard.py that takes dashboard/index.html as a template and
replaces the contents of each <script type="text/csv" data-dataset="NAME"> block
with the contents of data/NAME.csv, then writes output/MRMC_<quarter>.html.
Leave everything else in the file unchanged. Fail with a clear message if a CSV
is missing a column the page needs.
```

### 4g. Independent check before the pack goes out

```
Act as an independent reviewer. Compare prep_data.py with docs/build-spec.md
rule by rule and list any difference. Then write a separate function that
recomputes the seven KRIs directly from the source extracts, without reusing
prep_data.py, and compare its results with the last row of data/trend.csv.
Report any mismatch.
```

### 4h. Customizing

```
Add an eighth KRI, "<name>", defined as <definition>, higher_worse,
amber > <x>, red > <y>. Update data/appetite.csv, the trend history, the
KRI logic table in docs/build-spec.md and the dashboard if needed.
Show me the diff before saving.
```

```
Change the colours and fonts in dashboard/index.html to match our brand:
primary <hex>, fonts <names>. Only edit the design tokens in the first
<style> block.
```

### 4i. If you are rebuilding in Power BI instead

```
Using docs/build-spec.md, write:
(1) Power Query M that loads our three extracts and unpivots the trend table
into KRI_History (quarter, kri_id, value),
(2) the calculated columns and DAX measures listed in the spec, adapted to our
table and column names: <list>.
For each measure, state which rule ID it implements, and give a test case I can
check against the sample data.
```

The assistant writes the M and DAX; you paste them into Power BI Desktop and build the visuals from the page-layout table.

## Step 5. Quarterly run

1. Export the three extracts as of quarter end into `data/private/`.
2. Run `python prep_data.py --asof YYYY-MM-DD` and `python build_dashboard.py`.
3. Run the tests and the independent check (4g).
4. Reconcile the seven KRIs to the source systems; investigate any difference.
5. Update `data/matters.csv` with this quarter's decisions and information items.
6. MRM review and sign-off.
7. Open `output/MRMC_<quarter>.html` and print to PDF for the committee pack. Section 1 alone is the Board view.
8. Commit to the internal repository and tag the release, for example `git tag 2026Q4`.

## Step 6. Governance

- Register the dashboard and its scripts as one EUA, tiered High because it feeds Board reporting.
- Keep the build spec as the control document: any change to a rule changes the spec first, then the code, then the tests.
- Changes to KRI limits only after MRMC or Board approval, recorded in `data/appetite.csv` with the approval date in the commit message.
