# GitHub Pages deployment checklist

This dashboard is a static site. It does not require a backend server.

## Required files

Publish these files and directories with the same relative paths:

- `gaokao_dashboard.html`
- `share.html`
- `data/share_snapshot.js`
- `data/processed/2026_plan_majors.js`
- `data/processed/2025_scores_physics_undergrad.js`
- `data/processed/2025_rank_table_physics_undergrad.js`

Optional but useful for audit or regeneration:

- `data/processed/2026_fujian_plan_majors_from_xlsx.csv`
- `data/processed/2026_fujian_plan_groups_from_xlsx.csv`
- `data/processed/2025_fujian_scores_physics_undergrad.csv`
- `scripts/import_xlsx_2026_plan.py`
- `scripts/import_2025_scores.py`
- `scripts/import_2025_rank_table.py`

## Current data alignment

- `2026_plan_majors.js` is generated from:
  - `data/06--福建/福建2026招生计划/本科批/物理类/福建2026年物理类本科批招生计划/2026年福建物理类本科批招生计划.xlsx`
  - Current row count: 15051 major-level records.
- `2025_scores_physics_undergrad.js` is generated from:
  - `data/06--福建/④-其它分类方法/【2017-2025】专业录取分数线/25年全国高校在福建的专业录取分数.xlsx`
  - Current row count: 8409 physics undergraduate major-level records.
- `2025_rank_table_physics_undergrad.js` is generated from:
  - `data/06--福建/④-其它分类方法/【2017-2025】一分一段/福建2025年的一分一段表.xlsx`
  - Current row count: 248 score-rank records above the 2025 physics undergraduate control line.

## Path rules

- `gaokao_dashboard.html` uses only relative script paths:
  - `data/processed/2026_plan_majors.js`
  - `data/processed/2025_scores_physics_undergrad.js`
  - `data/processed/2025_rank_table_physics_undergrad.js`
- `share.html` is the read-only sharing page. It uses:
  - `data/share_snapshot.js`
  - `data/processed/2026_plan_majors.js`
  - `data/processed/2025_scores_physics_undergrad.js`
- Do not move the HTML file without also preserving the relative `data/processed/` path.
- Avoid committing temporary Excel lock files that start with `~$`.

## Regeneration commands

Run these from the repository root when source spreadsheets change:

```bash
python3 scripts/import_xlsx_2026_plan.py
python3 scripts/import_2025_scores.py
python3 scripts/import_2025_rank_table.py
```

## Quick local verification

```bash
node -e "const fs=require('fs'); const vm=require('vm'); const ctx={window:{}}; vm.createContext(ctx); vm.runInContext(fs.readFileSync('data/processed/2026_plan_majors.js','utf8'),ctx); vm.runInContext(fs.readFileSync('data/processed/2025_scores_physics_undergrad.js','utf8'),ctx); vm.runInContext(fs.readFileSync('data/processed/2025_rank_table_physics_undergrad.js','utf8'),ctx); console.log(ctx.window.PLAN_MAJORS.length, ctx.window.SCORES_2025.length, ctx.window.RANK_TABLE_2025.length)"
```

Expected output:

```text
15051 8409 248
```
