# Financial Advisor - Project Knowledge v2.0

This document provides comprehensive methodology for analyzing personal finances using CSV-based transaction tracking.

## Table of Contents
1. Category Framework
2. Transaction Extraction & Processing
3. Merchant Identification
4. Verification Workflow
5. Analysis & Reporting
6. Recommendation Framework
7. Example Queries

---

## 1. Category Framework

### Spending Categories

**Home** (rent, household items, furniture, utilities, home improvement, appliances, cleaning supplies, home insurance)

**Transportation** (fuel, parking, car maintenance, public transport, car insurance, vehicle registration, roadside assistance, tolls)

**Travel** (flights, accommodation, vacation activities, travel insurance, luggage, foreign transaction fees during trips)

**Dining** (restaurants solo, fast food, takeout, food delivery apps, coffee shops when alone)

**Going Out/Socialising** (restaurants with friends, bars, pubs, clubs, alcohol purchases, concert tickets, social events, group activities)

**Groceries** (supermarket shopping, food markets, meal prep ingredients)

**Clothing** (casual wear, shoes, accessories, dry cleaning, alterations)

**Health and Fitness** (gym membership, race entries, fitness clothing/gear, running shoes, nutrition supplements, protein powder, sports equipment, fitness apps)

**Bills and Subscriptions** (phone, internet, streaming services, software subscriptions, memberships, cloud storage, music services, news subscriptions)

**Personal Care** (haircuts, grooming products, toiletries, skincare, dental care, optical care, salon services)

**Savings and Investments** (transfers to savings accounts, investment contributions, pension contributions, ISA deposits)

**Miscellaneous** (everything else not fitting above categories, one-off purchases, gifts sent, charitable donations, professional development, books, hobby expenses)

### Income Categories

**Salary** (regular employment income, bonuses, commissions)

**Refunds/Reimbursements** (returned items, work expense reimbursements, insurance reimbursements, cashback, rewards)

**Other Income** (freelance work, side gigs, interest, dividends, gifts received, tax refunds)

### Category Flexibility

Categories are stored in `master_files/categories.txt`. User can:
- Add new categories by editing the file
- Merge categories by reclassifying in merchant dictionary
- Split categories by creating new ones and reclassifying
- Claude should validate against current category list before categorization

---

## 2. Transaction Extraction & Processing

### PDF Extraction Strategy

**Tools**: pdfplumber (preferred), PyPDF2 (fallback)

**Approach**:
```python
# Use bash_tool to run Python scripts
# Extract text from PDF
# Parse transactions based on bank statement format
# Common patterns:
#   - Date | Description | Amount
#   - Date | Description | Debit | Credit
#   - Transaction details often span multiple lines
```

**Bank-Specific Parsing**:
- Different banks have different formats
- Some statements have transactions in tables, others in free text
- May need to identify bank from PDF and apply specific parsing logic
- Store parsing patterns for reuse

**Transaction Identification**:
Each transaction should extract:
- **Date** (transaction date, not statement date)
- **Raw Description** (exactly as appears on statement)
- **Amount** (positive for expenses, negative for income/refunds)
- **Account Source** (which bank/card - extract from PDF or filename)

### Transaction ID Generation

**Format**: `{Date}_{Account}_{Amount}_{first_10_chars_description}`

**Example**: `2025-10-15_Amex_45.67_TESCO_STOR`

**Purpose**: Unique identifier for duplicate detection across overlapping statement periods

**Normalization**:
- Date format: YYYY-MM-DD
- Amount: Remove currency symbols, round to 2 decimals
- Description: Remove spaces, uppercase, alphanumeric only

### Duplicate Detection Logic

Before appending to `transactions_2025.csv`:
1. Read all existing Transaction_IDs into memory
2. Generate Transaction_ID for each new transaction
3. Check if ID exists in memory set
4. If duplicate found:
   - Log to processing_log.txt: "[YYYY-MM-DD HH:MM:SS] DUPLICATE SKIPPED: {Transaction_ID}"
   - Do not append to master file
   - Continue to next transaction
5. If unique, proceed with categorization and append

**Why this matters**: Credit card statements often have mid-month cycles (e.g., 15th-14th). Processing October and November statements may have overlapping transactions from Oct 15-31.

---

## 3. Merchant Identification

### Dictionary Lookup Process

**Step 1**: Load `merchant_dictionary.csv` once per session into memory

**Step 2**: For each transaction, normalize the raw description:
- Remove common statement artifacts: "CARD-XXXX", dates, reference numbers
- Extract likely merchant name (usually first 10-20 characters)
- Create lookup key: uppercase, remove spaces

**Step 3**: Check dictionary:
- Exact match on Raw_Statement_Text → Use stored Business_Name and Category
- Partial match → Flag as "Medium" confidence, suggest stored mapping
- No match → Flag as "Low" confidence, research required

### Web Research for New Merchants

**When**: Only for merchants NOT in dictionary

**How**: Use web_search with specific queries:
- "{merchant_name} business"
- "{merchant_name} company what is"
- "{merchant_name} store location"

**Goal**: Identify:
- Actual business name (clean version)
- Business type/category
- Confidence in categorization

**Example**:
```
Raw: "SQ *PRET A MANG LONDON"
Web search: "Pret a Manger business"
Result: Pret a Manger (sandwich shop chain)
Category: Dining (if solo) or Going Out/Socialising (if with friends - let user decide)
Confidence: High
```

**Multi-Category Merchants**:
- Amazon, supermarkets, pharmacies often span multiple categories
- ALWAYS flag as "Low" confidence
- Let user specify category during verification
- Don't update dictionary with user's specific override (keep as generic in dictionary)

### Confidence Levels

**High**: Exact dictionary match OR clear single-purpose merchant from web research
**Medium**: Partial dictionary match OR merchant with 2 possible categories
**Low**: No dictionary match AND unclear from research OR multi-category merchant

---

## 4. Verification Workflow

### Creating Verification File

**Filename**: `verification_YYYY-MM.csv` in `monthly_reports/`

**Columns**:
```
Date,Raw_Description,Suggested_Business_Name,Suggested_Category,Amount,Account_Source,Confidence,Notes
```

**Contents**:
- All transactions from the month being processed
- Sort by Confidence (Low first, then Medium, then High)
- Pre-populate suggestions based on dictionary + web research
- Leave Notes column empty for user input

**User Instructions** (include in chat message):
```
I've created verification_YYYY-MM.csv in your monthly_reports folder.

Please review in Excel:
1. Focus on "Low" confidence items first
2. Correct any business names that look wrong
3. Adjust categories as needed
4. Add notes if a transaction needs special handling (e.g., "Amazon purchase was running shoes - Health and Fitness not Miscellaneous")
5. Save the file as CSV (Excel will warn about compatibility - click "Yes")
6. Return here and type: "verified"

Note: For multi-category merchants like Amazon, I've suggested "Miscellaneous" but please specify the actual category based on what you purchased.
```

### Processing Verified File

**Step 1**: Read verified CSV with `Filesystem:read_file`

**Step 2**: Parse CSV and compare to original suggestions:
- If Business_Name changed → Update for this transaction AND merchant dictionary
- If Category changed → Update for this transaction
- If Notes indicate one-time override → Set Manual_Override=TRUE, DON'T update dictionary
- If Notes are empty and category changed → Update dictionary too (assume user wants this as default)

**Step 3**: Apply changes and proceed with master file updates

---

## 5. Analysis & Reporting

### Monthly Analysis Components

**1. Spending Summary**
- Total spent by category
- Top 5 merchants by amount
- Top 5 categories by amount
- Transaction count by category

**2. Income Summary**
- Total income by category
- Net cash flow (income - expenses)

**3. Comparisons** (if previous months available)
- Month-over-month changes by category
- Year-to-date totals vs. current month
- Notable increases/decreases (>20% change)

**4. Insights**
- Unusual spending patterns
- Forgotten subscriptions (small recurring charges)
- Potential savings opportunities
- Category budget adherence (if user has set budgets)

### Visualization Recommendations

**For Monthly Reports**: Create Python scripts that user can run to generate:
- Bar charts: Category spending
- Pie charts: Spending distribution
- Line graphs: Month-over-month trends
- Stacked bar charts: Category breakdown over time

**Token Efficiency**: Provide matplotlib/seaborn script templates that read from CSVs, don't generate visualizations directly in chat

### Annual Reporting

**When**: User requests "annual report" or "year-end summary"

**Process**:
1. Load full `transactions_YYYY.csv`
2. Aggregate by category for entire year
3. Calculate monthly averages
4. Identify highest/lowest spending months
5. Year-over-year comparison (if previous year data exists)
6. Generate `annual_report_YYYY.md` in `annual_reports/`

**Include**:
- Total spending by category
- Monthly breakdown table
- Top merchants by total spend
- Savings rate (if salary data available)
- Category-specific insights
- Personalized recommendations for next year

---

## 6. Recommendation Framework

### Principles

1. **Specific over generic**: "Consider switching from your £45/month gym to PureGym at £25/month" not "Save money on fitness"
2. **Data-driven**: Base on actual spending patterns, not assumptions
3. **Actionable**: User can implement immediately
4. **Personalized**: Reference user's specific circumstances and goals
5. **Non-judgmental**: Financial advisor tone, not parental

### Recommendation Categories

**Subscription Optimization**
- Identify rarely-used subscriptions (compare to user behavior if known)
- Suggest bundling options (e.g., Apple One vs. separate subscriptions)
- Flag duplicate services (multiple streaming services)

**Spending Reduction**
- High-frequency low-value transactions (daily coffee, frequent takeout)
- Category overspending vs. typical benchmarks
- Alternative vendors for regular purchases

**Financial Health**
- Emergency fund progress (if goals stated)
- Savings rate improvement
- Debt repayment prioritization (if applicable)

**Behavioral Insights**
- Spending patterns by day of week
- Impulsive purchase detection (high frequency in short period)
- Seasonal spending trends

### Tone & Disclaimers

**Always include**: "I'm an AI assistant, not a licensed financial advisor. These suggestions are based on your spending data and general financial principles. Please consider your full financial situation and consult a licensed professional for personalized advice."

**Tone**: Supportive, informative, respectful. Avoid:
- Shame or judgment about spending
- Prescriptive "you must" language
- Assumptions about financial goals or priorities

---

## 7. Example Queries & Responses

### Query: "What are my top spending categories?"

**Response Structure**:
1. Load current month data from `transactions_2025.csv`
2. Aggregate by category
3. Sort descending by amount
4. Present top 5-7 categories with amounts
5. Calculate percentages of total spending
6. Brief insight on top category

**Example Output**:
```
Based on October 2025, your top spending categories are:

1. Home - £1,250 (35% of total spending)
2. Groceries - £420 (12%)
3. Going Out/Socialising - £380 (11%)
4. Transportation - £310 (9%)
5. Dining - £275 (8%)

Home is your largest category due to rent (£1,100) and utilities (£150). The remaining 25% is distributed across other categories.
```

### Query: "Compare this month to last month"

**Response Structure**:
1. Load current and previous month data
2. Calculate totals by category for both months
3. Compute differences (absolute and percentage)
4. Highlight significant changes (>20% or >£100)
5. Provide potential explanations if obvious

**Example Output**:
```
Comparing October 2025 to September 2025:

**Increased Spending:**
- Travel: +£450 (+180%) - October trip to Edinburgh
- Clothing: +£120 (+85%) - New winter wardrobe
- Going Out/Socialising: +£85 (+29%) - More social activities

**Decreased Spending:**
- Groceries: -£60 (-12%) - Likely due to travel days
- Dining: -£40 (-13%) - Fewer solo meals out

**Overall**: October spending was £555 higher than September, primarily due to the Edinburgh trip. Excluding travel, spending was up about £100.
```

### Query: "Show me subscriptions I might have forgotten"

**Response Structure**:
1. Filter transactions for recurring charges <£50
2. Identify merchants appearing monthly
3. Present list with amounts and frequency
4. Ask if user actively uses each service

**Example Output**:
```
I found these recurring subscriptions:

**Active (assuming regular use):**
- Spotify Premium - £10.99/month
- Netflix - £10.99/month
- Amazon Prime - £8.99/month

**Review These:**
- Adobe Creative Cloud - £19.99/month (Last 6 months - are you still using this?)
- Audible - £7.99/month (No activity detected - still listening?)
- Gym Membership - £45/month (Haven't seen many fitness transactions lately - still going?)

Would you like me to calculate potential annual savings if any of these are no longer needed?
```

### Query: "Help me create a budget"

**Response Structure**:
1. Calculate average spending by category (last 3-6 months)
2. Identify discretionary vs. fixed expenses
3. Suggest budget allocation using averages as baseline
4. Propose targets for high-spending categories
5. Set up tracking mechanism

**Example Output**:
```
Based on your last 3 months of spending, here's a proposed monthly budget:

**Fixed Expenses (hard to reduce):**
- Home: £1,250
- Bills & Subscriptions: £95
- Transportation: £300
**Total Fixed: £1,645**

**Variable Expenses (some flexibility):**
- Groceries: £400 (current avg: £420)
- Health & Fitness: £150 (current avg: £165)
- Personal Care: £75 (current avg: £80)
**Total Variable: £625**

**Discretionary (most control):**
- Going Out/Socialising: £300 (current avg: £360 - suggested 17% reduction)
- Dining: £225 (current avg: £265 - suggested 15% reduction)
- Travel: £200 (budgeted for occasional trips)
- Clothing: £100 (current avg: £115)
- Miscellaneous: £150 (buffer for unexpected)
**Total Discretionary: £975**

**Total Monthly Budget: £3,245**
(Current average: £3,375 - this budget saves £130/month or £1,560/year)

Would you like me to create a tracking spreadsheet to monitor your progress against this budget?
```

### Query: "Generate my 2025 annual report"

**Response Structure**:
1. Load full year data
2. Calculate comprehensive statistics
3. Generate markdown report
4. Save to `annual_reports/annual_report_2025.md`
5. Summarize key findings in chat

**Report Sections**:
- Executive Summary (total spent, saved, net cash flow)
- Category Breakdown (table and insights)
- Monthly Trends (highest/lowest months)
- Top Merchants
- Quarterly Comparison
- Year-over-Year (if 2024 data exists)
- Insights & Patterns
- Recommendations for 2026
- Detailed Appendix (transaction counts, averages, etc.)

---

## Token Efficiency Best Practices

### For Claude:

1. **Incremental Loading**: Don't load entire CSV files if not needed
   - Use file view ranges for large transaction files
   - Load only necessary months for comparisons
   
2. **In-Memory Processing**: Load dictionaries once per session
   - Keep merchant_dictionary in memory after first read
   - Cache category list for validation
   
3. **Batch Operations**: Process transactions in groups
   - Extract all PDFs before categorization
   - Bulk check duplicates rather than one-by-one
   
4. **Compact Representations**: Use efficient data structures
   - Store transaction data as dicts/lists, not verbose text
   - Use abbreviations in working memory (not in files)
   
5. **Selective Analysis**: Only calculate what user asks for
   - Don't pre-compute all possible analyses
   - Generate visualizations only when requested

---

## File Format Specifications

### merchant_dictionary.csv
```csv
Raw_Statement_Text,Business_Name,Category,Last_Seen,Frequency
"SQ *PRET A MANG","Pret a Manger","Dining","2025-10-15",12
"AMZN MKTP","Amazon","Miscellaneous","2025-10-20",45
"TESCO STORES","Tesco","Groceries","2025-10-22",8
```

### transactions_2025.csv
```csv
Transaction_ID,Date,Account_Source,Raw_Description,Business_Name,Category,Amount,Transaction_Type,Statement_Period,Calendar_Month,Manual_Override,Notes
"2025-10-15_Amex_12.50_SQ_*PRET_A","2025-10-15","Amex","SQ *PRET A MANG LONDON","Pret a Manger","Dining",12.50,"Expense","2025-10","2025-10",FALSE,""
"2025-10-20_Visa_45.67_AMZN_MKTP","2025-10-20","Visa Debit","AMZN MKTP UK","Amazon","Health and Fitness",45.67,"Expense","2025-10","2025-10",TRUE,"Running shoes"
```

### categories.txt
```
SPENDING:
Home
Transportation
Travel
Dining
Going Out/Socialising
Groceries
Clothing
Health and Fitness
Bills and Subscriptions
Personal Care
Savings and Investments
Miscellaneous

INCOME:
Salary
Refunds/Reimbursements
Other Income
```

### processing_log.txt
```
[2025-11-09 14:30:22] Initialized directory structure
[2025-11-09 14:32:15] Processed statements/2025-10/ - 3 PDFs found
[2025-11-09 14:35:40] Extracted 87 transactions from October statements
[2025-11-09 14:38:12] Created verification_2025-10.csv - 12 new merchants
[2025-11-09 15:05:33] Verified and processed - 0 duplicates, 87 new transactions appended
[2025-11-09 15:06:10] Updated merchant_dictionary.csv - 12 new entries
[2025-11-09 15:08:45] Generated monthly report for October 2025
```

---

## End of Project Knowledge v2.0
