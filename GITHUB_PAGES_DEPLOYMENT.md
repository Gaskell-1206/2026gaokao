# GitHub Pages deployment checklist

This dashboard is a static site. It does not require a backend server.

## Required files

Publish these files and directories with the same relative paths:

- `gaokao_dashboard.html`
- `share.html`
- `data/share_snapshot.js`
- `data/processed/2026_plan_majors.js`
- `data/processed/2025_scores_physics_undergrad.js`
- `data/processed/2025_school_scores_physics.js`
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
  - `data/福建_招生计划_2026.xlsx`
  - Current row count: 15057 major-level records.
- `2025_scores_physics_undergrad.js` is generated from:
  - `data/福建-专业分数线-2025.xlsx`
  - Current row count: 13448 physics undergraduate major-level records.
- `2025_school_scores_physics.js` is generated from:
  - `data/06--福建/④-其它分类方法/【2017-2025】投档线/25年全国高校在福建的院校录取分数.xlsx`
  - Current row count: 3088 school/group admission records.
- `2025_rank_table_physics_undergrad.js` is generated from:
  - `data/06--福建/④-其它分类方法/【2017-2025】一分一段/福建2025年的一分一段表.xlsx`
  - Current row count: 248 score-rank records above the 2025 physics undergraduate control line.

## Path rules

- `gaokao_dashboard.html` uses only relative script paths:
  - `data/processed/2026_plan_majors.js`
  - `data/processed/2025_school_scores_physics.js`
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
node -e "const fs=require('fs'); const vm=require('vm'); const ctx={window:{}}; vm.createContext(ctx); vm.runInContext(fs.readFileSync('data/processed/2026_plan_majors.js','utf8'),ctx); vm.runInContext(fs.readFileSync('data/processed/2025_scores_physics_undergrad.js','utf8'),ctx); vm.runInContext(fs.readFileSync('data/processed/2025_school_scores_physics.js','utf8'),ctx); vm.runInContext(fs.readFileSync('data/processed/2025_rank_table_physics_undergrad.js','utf8'),ctx); console.log(ctx.window.PLAN_MAJORS.length, ctx.window.SCORES_2025.length, ctx.window.SCHOOL_SCORES_2025.length, ctx.window.RANK_TABLE_2025.length)"
```

Expected output:

```text
15057 13448 3088 248
```

## Sync behavior

- Static GitHub Pages can sync the published database files because they are committed to the repository.
- Editing records in `gaokao_dashboard.html` are stored in browser `localStorage` (`fj_gaokao_*` keys), so they persist on the same browser/device.
- Cross-device or multi-user operation-record sync requires a writable backend such as Supabase/Firebase or a private API. GitHub Pages alone cannot safely write edits back to the repository from the browser.
- Until a backend is added, use the built-in JSON/CSV export/import and share snapshot export as the operation-record transfer path.
