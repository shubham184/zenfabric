---
title: "Is Your Data Ready for AI? The Unsexy Checklist for Data Readiness"
date: 2024-12-19
draft: false
description: "AI projects fail because of bad data, not bad models. Here's a practical checklist to audit your data foundation before you waste six months on a pilot that never ships."
tags: [data-quality, ai, data-governance, microsoft-fabric, dbt]
---

Everyone wants to talk about AI. Few want to talk about whether their data can actually support it.

Here's the uncomfortable truth: most AI initiatives don't fail because of model complexity or talent gaps. They fail because the underlying data is incomplete, inconsistent, or trapped in silos nobody wants to touch. Industry estimates suggest data scientists spend anywhere from 50% to 80% of their time just finding, cleaning, and organizing data before any real analysis happens. That's not a rounding error — it's a structural problem.

Before you spin up that first ML experiment, run through this checklist. It's not glamorous, but it might save your project.

## Audit Your Data Sources

Start by mapping what you actually have. Not what's documented — what exists in practice.

Ask these questions: Where does customer data live? Is it one system or seven? When was the last time anyone validated that your "single source of truth" actually is? Do your sales numbers in the CRM match your finance system?

Tools like Microsoft Fabric's Data Factory can help you inventory and connect disparate sources, but the real work is organizational. You need someone with authority to say "this is the canonical source" — and make it stick.

**Red flag:** If two teams give you different answers to the same business question, you have a data silo problem that will torpedo any AI initiative.

## Data Quality 101

AI models are pattern-recognition engines. Feed them garbage patterns, and they'll confidently produce garbage predictions.

Check for the basics: completeness (how many nulls?), accuracy (does "CA" mean California or Canada?), consistency (is a date `2024-01-15` or `01/15/2024` or `15-Jan-24`?), and timeliness (is this data from yesterday or last quarter?).

A simple validation query can reveal a lot:

```sql
-- Quick health check: find columns with high null rates
SELECT
    'customer_id' AS column_name,
    COUNT(*) AS total_rows,
    SUM(CASE WHEN customer_id IS NULL THEN 1 ELSE 0 END) AS null_count,
    ROUND(100.0 * SUM(CASE WHEN customer_id IS NULL THEN 1 ELSE 0 END) / COUNT(*), 2) AS null_pct
FROM orders;
```

For automated, repeatable checks, dbt tests or Great Expectations let you define expectations (e.g., "this column should never be null," "values must be in this range") and fail pipelines when data violates them. Build these checks into your pipelines early — retrofitting data quality is painful.

## Metadata and Cataloging

If your team can't find data, it doesn't exist. If they find it but don't trust it, same problem.

A basic data catalog answers: What does this table contain? Who owns it? When was it last updated? What's upstream and downstream?

Fabric's lineage view gives you this visibility out of the box for assets within its ecosystem. For broader coverage, you'll need a dedicated catalog tool — but even a well-maintained Confluence page beats nothing.

## Testing Data for AI Use

AI-ready data has additional requirements beyond traditional BI. You need sufficient volume (small datasets lead to overfit models), representative samples (if your training data skews toward one customer segment, your model will too), and historical depth (time-series models need history; how much depends on what you're predicting).

Before any ML work, profile your candidate datasets. Databricks notebooks or even a Power BI dataflow can help you visualize distributions, spot outliers, and identify class imbalances that will wreck your model.

## The Checklist

Before greenlighting an AI project, confirm you can check these boxes:

1. Data sources are inventoried and ownership is clear
2. Quality checks run automatically on every pipeline refresh
3. Critical fields have documented definitions and acceptable value ranges
4. Lineage is traceable from source to consumption
5. Historical data exists in sufficient depth for your use case
6. A governance process exists for data changes

**Missing two or more?** You're not ready. Fix the foundation first.

## What to Do Monday Morning

Pick one critical dataset for your planned AI use case. Run the null-check query. Document what you find. That single action will tell you more about your readiness than any vendor demo ever will.

AI isn't magic — it's math applied to data. Get the data right, and the rest gets a lot easier.
