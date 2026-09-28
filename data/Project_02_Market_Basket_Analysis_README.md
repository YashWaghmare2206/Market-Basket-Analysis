# Project 2 — Market Basket Analysis & Association Rule Mining

## 1. Project Overview

This project applies **Unsupervised Learning techniques for discovering relationships between products purchased together**.

We will use the **UCI Online Retail dataset**, a real transactional dataset containing **541,909 transaction records** from a UK-based non-store online retailer covering transactions from **1 December 2010 to 9 December 2011**. UCI lists the dataset as supporting **classification and clustering** and provides transaction-level fields including invoice number, stock code, product description, quantity, invoice date, unit price, customer ID, and country.

Official dataset:

https://archive.ics.uci.edu/dataset/352/online-retail

This project is specifically focused on:

- Association Rule Learning
- Frequent itemset mining
- Apriori
- FP-Growth
- Support
- Confidence
- Lift
- Rule filtering
- Rule interpretation
- Business-oriented analysis

The goal is **not simply to generate a list of rules**.

The goal is to understand:

> **How raw transaction data is transformed into baskets, how frequent itemsets are discovered, how association rules are generated, how rule metrics behave, and how to determine whether a discovered relationship is actually useful.**

---

# 2. Main Aim

The main objective is:

> **To develop practical understanding of Association Rule Learning by taking raw retail transaction data, converting it into a suitable basket representation, mining frequent itemsets, generating association rules, comparing Apriori and FP-Growth, and critically evaluating the resulting rules.**

The complete workflow should look like:

```text
Raw Transaction Data
        ↓
Understand Transactions
        ↓
Clean Transaction Data
        ↓
Define a "Basket"
        ↓
Create Transaction × Product Representation
        ↓
Frequent Itemset Mining
        ↓
      ┌───────────────┐
      ↓               ↓
   Apriori         FP-Growth
      ↓               ↓
      └───────┬───────┘
              ↓
       Association Rules
              ↓
     Support / Confidence
              ↓
             Lift
              ↓
       Rule Filtering
              ↓
       Rule Interpretation
              ↓
    Business Recommendations
```

---

# 3. What We Are NOT Trying To Do

We are **NOT** trying to:

- simply run `apriori()` and print rules
- generate thousands of rules without interpretation
- optimize only for the number of rules
- assume high confidence means a useful rule
- assume correlation/association means causation
- treat every product relationship as meaningful
- implement Apriori or FP-Growth from scratch
- use machine learning classification to predict purchases
- jump directly from raw CSV to association rules

Instead, the project should make us understand the **entire association-rule mining pipeline**.

---

# 4. Dataset

## UCI Online Retail

The dataset contains transactions from a UK-based registered non-store online retailer. It contains **541,909 records** covering transactions between **01/12/2010 and 09/12/2011**. The company mainly sells unique all-occasion gifts, and many customers are wholesalers.

### Official Dataset

https://archive.ics.uci.edu/dataset/352/online-retail

### Main Variables

| Variable | Meaning |
|---|---|
| `InvoiceNo` | Transaction/invoice number |
| `StockCode` | Product identifier |
| `Description` | Product name |
| `Quantity` | Quantity purchased |
| `InvoiceDate` | Transaction date/time |
| `UnitPrice` | Price per unit |
| `CustomerID` | Customer identifier |
| `Country` | Customer country |

UCI notes that an invoice number beginning with `C` indicates a cancellation.

### Dataset Citation

Chen, D. (2015). *Online Retail*. UCI Machine Learning Repository. DOI: 10.24432/C5BW33.

---

# 5. Core Learning Questions

Throughout the project, continuously answer questions like:

## Transaction Representation

- What exactly is a transaction?
- What exactly is a basket?
- Why can't we directly apply Apriori to the raw table?
- How should products be represented?
- Should quantity matter?
- Should repeated products within a basket count once or multiple times?

## Data Cleaning

- How should cancelled invoices be handled?
- What should we do with missing `CustomerID`?
- Should negative quantities be retained?
- What about zero/negative prices?
- Should product descriptions be cleaned?
- Should duplicate product entries within an invoice be combined?

## Frequent Itemsets

- What does support actually mean?
- Why does increasing minimum support reduce itemsets?
- Why can low-support itemsets explode combinatorially?
- What is an itemset?
- What is a frequent itemset?
- Why is Apriori computationally expensive?

## Association Rules

- What is antecedent?
- What is consequent?
- What does confidence actually tell us?
- Why is confidence alone dangerous?
- What does lift add?
- Why can a rule have high confidence but low usefulness?
- What does lift > 1 mean?

## Algorithm Comparison

- How does Apriori search for itemsets?
- How does FP-Growth avoid candidate generation?
- Why can FP-Growth be faster?
- When does the difference matter?

## Business Interpretation

- Which products are commonly purchased together?
- Which rules are strong?
- Which rules are trivial?
- Which rules might be actionable?
- Are the discovered relationships stable and meaningful?

---

# 6. Project Scope

This project should cover the complete Association Rule Learning pipeline.

## Required Topics

### 1. Association Rule Learning fundamentals

Understand:

- transaction
- basket
- item
- itemset
- frequent itemset
- antecedent
- consequent
- association rule

### 2. Support

Understand:

```text
support(A) =
transactions containing A
-------------------------
total transactions
```

Experiment with different minimum support thresholds.

### 3. Confidence

Understand:

```text
confidence(A → B) =
support(A ∪ B)
---------------
support(A)
```

Investigate why confidence can be misleading.

### 4. Lift

Understand:

```text
lift(A → B) =
confidence(A → B)
-----------------
support(B)
```

Interpret:

```text
Lift > 1  → positive association
Lift = 1  → approximately independent
Lift < 1  → negative association
```

Do not treat these as absolute proof of causality.

---

# 7. Data Understanding

Before mining anything, understand the raw data.

Investigate:

- number of transactions
- number of unique products
- number of customers
- countries
- date range
- average basket size
- distribution of quantities
- distribution of prices
- cancelled transactions
- missing customer IDs
- duplicate records
- unusual quantities
- unusual prices

Important:

> **Do not immediately aggregate everything. First understand what one row and one invoice represent.**

---

# 8. Defining the Basket

This is one of the most important decisions in the project.

For association rule mining, we need:

```text
Transaction
      ↓
Set of products purchased
```

For example:

```text
Invoice 10001

{Tea Cup, Candle, Gift Box}
```

Another:

```text
Invoice 10002

{Candle, Greeting Card}
```

The quantity may be useful for other analysis, but classical association-rule mining generally works with **item presence/absence**.

Therefore, we need to deliberately decide:

> **Does each product count once per invoice, regardless of quantity?**

For the primary analysis, the answer should generally be **yes**.

But we should understand why.

---

# 9. Data Cleaning

Data cleaning should be a substantial part of the project.

Investigate:

## Cancelled Invoices

The UCI documentation states that invoice numbers beginning with `C` indicate cancellations.

Experiment with how these should be handled.

For the primary basket-mining analysis, cancelled transactions should generally be excluded because we want actual purchase baskets.

But we should document this decision.

---

## Missing Customer IDs

Customer ID may be missing.

Ask:

> Do we actually need `CustomerID` for Market Basket Analysis?

For basic transaction-level association rules, **CustomerID is not required**.

This is an important distinction.

We can still use customer information later for optional analysis.

---

## Negative Quantity

Investigate negative quantities.

These can arise from returns/cancellations.

Do not blindly treat them as normal purchases.

---

## Unit Price

Investigate:

- zero prices
- unusually high prices
- price distribution

Unit price is not required for the core association-rule mining stage, but it is useful for exploratory analysis and optional business interpretation.

---

# 10. Basket Construction

Create a transaction representation similar to:

| Invoice | Product A | Product B | Product C | Product D |
|---|---:|---:|---:|---:|
| 10001 | 1 | 1 | 0 | 1 |
| 10002 | 0 | 1 | 1 | 0 |
| 10003 | 1 | 0 | 1 | 1 |

Where:

```text
1 = product appears in basket
0 = product does not appear
```

This is the representation that frequent-itemset algorithms will operate on.

However, we should understand the consequences of this transformation.

---

# 11. Product Filtering

A major practical problem is that retail datasets can contain many products.

If we allow every product:

```text
Thousands of products
        ↓
Huge possible combinations
        ↓
Large computational search space
        ↓
Potentially enormous number of rules
```

Therefore, investigate:

- very rare products
- very common products
- product frequency
- minimum product occurrence thresholds

We should experiment with product filtering rather than arbitrarily deleting products.

---

# 12. Apriori

Apriori is a major focus of this project.

We should understand the algorithm conceptually before using the implementation.

## Core Idea

Apriori uses the principle:

> **If an itemset is frequent, all of its subsets must also be frequent.**

And conversely:

> **If an itemset is infrequent, all of its supersets can be pruned.**

Understand:

```text
1-itemsets
    ↓
2-itemsets
    ↓
3-itemsets
    ↓
4-itemsets
    ↓
...
```

At every level:

```text
Generate candidates
        ↓
Calculate support
        ↓
Prune infrequent candidates
        ↓
Continue
```

---

# 13. Apriori Experiments

Do not run Apriori only once.

Experiment with:

### Minimum Support

For example:

```text
min_support = high
min_support = medium
min_support = low
```

Observe:

- number of frequent itemsets
- maximum itemset size
- runtime
- number of candidates/rules
- usefulness of resulting patterns

The key question:

> **What happens when support becomes too low?**

This should demonstrate the computational trade-off.

---

# 14. FP-Growth

Then repeat the frequent-itemset mining process using FP-Growth.

Understand:

- FP-tree intuition
- compressed transaction representation
- pattern growth
- why candidate generation is avoided/reduced
- why this can be faster than Apriori

Do not implement FP-Growth from scratch.

Use an established implementation/library.

The goal is to understand the **algorithmic difference and practical behavior**.

---

# 15. Apriori vs FP-Growth Experiment

This should be one of the most important experiments.

Run both algorithms on comparable preprocessing and support settings.

Compare:

| Property | Apriori | FP-Growth |
|---|---|---|
| Candidate generation | ... | ... |
| Core idea | ... | ... |
| Runtime | ... | ... |
| Memory behavior | ... | ... |
| Frequent itemsets | ... | ... |
| Scalability | ... | ... |
| Ease of understanding | ... | ... |

Important:

> **The algorithms should discover equivalent frequent-itemset patterns when configured consistently.**

The interesting difference is primarily **how efficiently they discover them**.

---

# 16. Association Rule Generation

After frequent itemsets are obtained, generate rules.

Example:

```text
{A, B} → {C}
```

Investigate:

- antecedent
- consequent
- support
- confidence
- lift

Also understand that:

```text
{A, B} → {C}
```

is not equivalent to:

```text
{C} → {A, B}
```

Their confidence can be very different.

---

# 17. Support Experiments

Investigate how changing support affects:

```text
Minimum support
      ↓
Number of itemsets
      ↓
Number of rules
      ↓
Rule complexity
      ↓
Business usefulness
```

Create a small experiment table.

Example:

| Min Support | Itemsets | Max Size | Rules |
|---:|---:|---:|---:|
| ... | ... | ... | ... |
| ... | ... | ... | ... |
| ... | ... | ... | ... |

The exact values should be determined experimentally.

---

# 18. Confidence Experiments

Investigate confidence thresholds.

For example:

```text
Low confidence threshold
        ↓
Many rules
        ↓
Higher threshold
        ↓
Fewer rules
```

But ask:

> Does increasing confidence necessarily improve the quality of the rules?

This should lead directly to the importance of **lift**.

---

# 19. Lift — Critical Concept

This should be one of the most deeply understood concepts in the project.

Suppose:

```text
A → B
confidence = 0.80
```

That sounds impressive.

But if:

```text
support(B) = 0.90
```

then B is already extremely common.

The high confidence may not represent a particularly interesting association.

Lift helps correct this perspective.

We should investigate examples where:

```text
High confidence + low lift
```

and:

```text
Moderate confidence + high lift
```

occur.

This is one of the most important practical lessons of Association Rule Learning.

---

# 20. Rule Filtering

Generating rules is easy.

Finding **useful rules** is the difficult part.

We should filter using combinations of:

- minimum support
- minimum confidence
- minimum lift
- antecedent size
- consequent size
- product frequency
- redundancy

For example:

```text
Generate 20,000 rules
        ↓
Support filter
        ↓
Confidence filter
        ↓
Lift filter
        ↓
Remove trivial/redundant rules
        ↓
50 potentially useful rules
        ↓
Manually inspect
        ↓
10–20 meaningful rules
```

The exact numbers will depend on the experiments.

---

# 21. Redundant Rules

Investigate whether multiple rules essentially express the same relationship.

Example:

```text
A → B
A,C → B
A,D → B
A,C,D → B
```

These may not all provide additional business value.

We should learn to identify:

- redundant rules
- overly specific rules
- trivial rules
- rules driven by extremely common products

---

# 22. Interestingness Beyond Metrics

A rule can be mathematically strong but practically useless.

For every final rule, ask:

1. Is the support large enough?
2. Is the confidence meaningful?
3. Is the lift meaningful?
4. Is the rule interpretable?
5. Is the relationship actionable?
6. Is it simply caused by a very common product?
7. Does it make business sense?

This prevents us from treating association mining as a pure metric-ranking exercise.

---

# 23. Optional Customer-Level Analysis

The core project is **transaction-level Market Basket Analysis**.

However, because the dataset contains `CustomerID`, we can optionally investigate:

- customer purchase frequency
- repeat baskets
- customer-level product preferences
- whether particular associations are driven by a small number of customers

This should remain **secondary**.

Do not turn Project 2 into Project 1 again.

The main focus remains:

> **Product-to-product associations within baskets.**

---

# 24. Time-Based Analysis

Because the dataset contains transaction dates, we can optionally investigate temporal behavior.

For example:

```text
Early period
     vs
Later period
```

Ask:

- Do strong associations remain stable?
- Do product relationships change over time?
- Are some associations seasonal?
- Does the same rule remain strong across different periods?

This is an excellent advanced experiment if time permits.

It should not replace the core Apriori/FP-Growth work.

---

# 25. Country Analysis

The dataset also contains `Country`.

Optional analysis:

```text
All countries
      vs
Major individual countries
```

Investigate whether product associations differ across major markets.

However:

> Do not create dozens of country-specific models.

Choose a small number of meaningful comparisons only if the core project is already complete.

---

# 26. Visualization

Visualizations should answer questions.

Useful visualizations include:

### Dataset

- transaction volume over time
- product frequency distribution
- basket-size distribution
- country distribution

### Frequent Itemsets

- support vs itemset size
- itemset frequency
- top frequent products

### Rules

- support vs confidence
- support vs lift
- confidence vs lift
- top rules by lift
- top rules by confidence

For example:

```text
        Lift
          ↑
          |
          |      •
          |   •
          |        •
          | •
          +----------------→ Support
```

This can help visualize the trade-off between **interestingness and prevalence**.

---

# 27. Algorithm Comparison

At the end, compare Apriori and FP-Growth.

The comparison should include:

- frequent itemsets discovered
- runtime
- support threshold behavior
- scalability
- memory considerations
- ease of understanding
- practical usefulness

The final conclusion should answer:

> **When would I choose Apriori and when would I choose FP-Growth?**

Not simply:

> "FP-Growth was faster."

---

# 28. Three-Day Structure

## DAY 1 — Transaction Data + Basket Construction

### Part 1 — Dataset Understanding

- Load dataset
- Understand all columns
- Identify transaction IDs
- Identify products
- Identify customers
- Understand dates
- Understand cancellations
- Understand quantities

### Part 2 — EDA

Investigate:

- transaction count
- unique products
- unique customers
- basket size
- product frequency
- quantity distribution
- cancellations
- missing CustomerIDs
- countries
- temporal distribution

### Part 3 — Data Cleaning

Decide and document:

- cancellation handling
- returns/negative quantities
- missing values
- invalid prices
- duplicate entries

### Part 4 — Basket Definition

Create:

```text
Invoice → Set of products
```

### Part 5 — Transaction × Product Representation

Create the basket matrix.

### Part 6 — Product Frequency

Investigate:

- common products
- rare products
- filtering thresholds

### Day 1 Goal

> **Understand how raw retail transaction data becomes a representation suitable for association-rule mining.**

---

# DAY 2 — Apriori + FP-Growth Deep Dive

## Part 1 — Association Rule Fundamentals

Understand:

- itemsets
- support
- confidence
- lift
- antecedent
- consequent

## Part 2 — Apriori

Experiment with:

- minimum support
- itemset sizes
- number of frequent itemsets
- runtime
- rule counts

## Part 3 — FP-Growth

Repeat comparable experiments.

Investigate:

- runtime
- frequent itemsets
- scalability
- differences from Apriori

## Part 4 — Algorithm Comparison

Build:

```text
Apriori
   vs
FP-Growth
```

comparison.

## Part 5 — Rule Generation

Generate rules from frequent itemsets.

Experiment with:

- confidence
- lift
- support

### Day 2 Goal

> **Understand how frequent patterns are discovered and how Apriori and FP-Growth differ in practice.**

---

# DAY 3 — Rule Quality + Interpretation + Advanced Analysis

## Part 1 — Metric Experiments

Investigate:

- support thresholds
- confidence thresholds
- lift thresholds

## Part 2 — Rule Filtering

Reduce:

```text
Thousands of rules
        ↓
Strong candidate rules
```

## Part 3 — Rule Quality

Investigate:

- high confidence / low lift
- moderate confidence / high lift
- trivial rules
- redundant rules
- rare but interesting rules

## Part 4 — Business Interpretation

Identify meaningful product relationships.

For each important rule:

```text
Rule
 ↓
Support
 ↓
Confidence
 ↓
Lift
 ↓
Interpretation
 ↓
Possible action
```

## Part 5 — Optional Advanced Analysis

If time remains:

- temporal stability
- country comparison
- customer-level analysis

## Part 6 — Final Conclusions

Answer:

- What product relationships did we discover?
- Which rules are genuinely interesting?
- What did support teach us?
- What did confidence teach us?
- Why is lift important?
- How did Apriori compare with FP-Growth?
- What preprocessing decisions mattered?
- What limitations exist?
- What would we investigate next?

---

# 29. Scratch Implementation Policy

We are **not implementing Apriori or FP-Growth from scratch**.

We will use established implementations/libraries.

The reason is deliberate.

The algorithms have already been studied mathematically.

This project is about learning to:

> **Represent transaction data correctly, choose sensible thresholds, analyze algorithm behavior, evaluate rules, and interpret results.**

We will still connect experiments back to the underlying algorithms.

For example:

```text
Apriori candidate generation
        ↓
Apriori pruning
        ↓
Support calculation
        ↓
Minimum-support threshold
        ↓
Number of candidates/itemsets
        ↓
Runtime
```

And:

```text
FP-tree
   ↓
Compressed transaction representation
   ↓
Pattern growth
   ↓
Frequent itemsets
```

---

# 30. Depth Standard

For every major concept, follow:

```text
1. Understand
       ↓
2. Build the representation
       ↓
3. Apply baseline
       ↓
4. Change important parameters
       ↓
5. Observe what changes
       ↓
6. Explain why
       ↓
7. Evaluate
       ↓
8. Interpret
       ↓
9. Compare
       ↓
10. Decide when to use it
```

If we cannot explain **why a threshold or representation changed the resulting rules**, the experiment is not finished.

---

# 31. Expected Final Notebook Structure

```text
01. Project Objective
02. Dataset Introduction
03. Business Problem
04. Association Rule Learning Overview
05. Import Libraries
06. Load Dataset
07. Data Understanding
08. Data Dictionary
09. Exploratory Data Analysis
10. Data Quality Investigation
11. Transaction/Cancellation Analysis
12. Basket Definition
13. Data Cleaning
14. Basket Construction
15. Transaction × Product Matrix
16. Product Frequency Analysis
17. Product Filtering Experiments
18. Support / Confidence / Lift Theory
19. Apriori Baseline
20. Apriori Experiments
21. FP-Growth Baseline
22. FP-Growth Experiments
23. Apriori vs FP-Growth
24. Association Rule Generation
25. Support Experiments
26. Confidence Experiments
27. Lift Analysis
28. Rule Filtering
29. Redundancy / Trivial Rule Analysis
30. Rule Visualization
31. Final Rule Selection
32. Business Interpretation
33. Optional Temporal Analysis
34. Optional Country Analysis
35. Final Algorithm Comparison
36. Limitations
37. Key Learnings
38. Final Conclusion
```

This is the **target structure**, not a requirement that every section becomes enormous.

---

# 32. Final Deliverables

By the end of the three days:

```text
Project_02_Market_Basket_Analysis/
│
├── README.md
│
├── notebooks/
│   └── 01_Market_Basket_Analysis.ipynb
│
├── data/
│   └── README.md
│
├── images/
│   └── important_visualizations/
│
└── requirements.txt
```

We should **not upload the raw UCI dataset unnecessarily** if the repository can instead document how to obtain it.

---

# 33. GitHub Quality

The GitHub version should communicate:

### Problem

What are we trying to discover?

### Dataset

Where did it come from?

### Transaction Representation

How did we transform raw transactions into baskets?

### Methodology

What did we try?

### Experiments

What happened when support/confidence thresholds changed?

### Algorithm Comparison

How did Apriori and FP-Growth behave differently?

### Results

Which rules were interesting?

### Interpretation

Why are those rules meaningful?

### Limitations

What should we be careful about?

### Learning

What did this project teach us?

A strong project should make it possible for someone to understand **why the final rules were selected**, not just see a dataframe of rules.

---

# 34. LinkedIn Angle

The interesting story is not:

> "I used Apriori on a retail dataset."

Instead:

> **I built a complete Market Basket Analysis pipeline on 541K real retail transactions, transforming transaction data into product baskets and investigating how support, confidence and lift affect the quality of association rules. I also compared Apriori and FP-Growth to understand the practical trade-offs between the two frequent-itemset mining approaches.**

This communicates actual learning rather than simply library usage.

---

# 35. What Success Looks Like

At the end of Project 2, you should be able to answer:

- **What exactly is a transaction/basket in association-rule mining?**
- **Why do we convert the raw data into a transaction × product representation?**
- **Why do we usually treat product presence rather than quantity for classical basket analysis?**
- **What does support actually tell us?**
- **Why can lowering minimum support dramatically increase computation?**
- **What does confidence tell us?**
- **Why can high confidence be misleading?**
- **What does lift add?**
- **Why might a high-lift rule still be useless?**
- **How does Apriori prune the search space?**
- **Why can FP-Growth be faster?**
- **When would you prefer FP-Growth over Apriori?**
- **How do you filter thousands of rules into a useful shortlist?**
- **How do you identify redundant or trivial rules?**
- **How would you explain an association rule to a non-technical person?**

If you can answer those from your own experiments, **the project has succeeded.**

---

# 36. Final Project Definition

| Item | Decision |
|---|---|
| **Project 2** | Market Basket Analysis |
| **Dataset** | UCI Online Retail |
| **Scale** | 541,909 transaction records |
| **Time** | ~3 days × 3–4 hours |
| **Primary Goal** | Practical understanding of Association Rule Learning |
| **Algorithms** | Apriori, FP-Growth |
| **Core Metrics** | Support, Confidence, Lift |
| **Focus** | Data cleaning → basket construction → itemsets → rules → filtering → interpretation |
| **Scratch Implementation** | ❌ No |
| **GitHub** | ✅ |
| **LinkedIn-ready** | ✅ |
| **Customer Segmentation** | ❌ Not the focus |
| **Project 3** | ❌ Not touched until Project 2 is complete |

---

# Project Philosophy

The project follows:

> **Understand → Represent → Mine → Experiment → Evaluate → Interpret → Compare → Conclude**

Not:

> **Load dataset → run Apriori → print rules → done.**

The objective is to finish Project 2 with **practical intuition about association-rule learning**, including when the rules are trustworthy, when metrics can mislead you, and how Apriori and FP-Growth behave on real transactional data.
