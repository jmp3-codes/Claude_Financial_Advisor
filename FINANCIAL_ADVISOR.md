# Financial Advisor - Project Knowledge v2.1 (Updated - Anonymised)

Methodology for analyzing personal finances using CSV-based transaction tracking with AI-powered categorization.

## Category Framework

### Spending Categories
**Home**: Rent, household items, furniture, utilities, home improvement, appliances, home insurance

**Transportation**: Fuel, parking, car maintenance, public transport, car insurance, tolls, rideshare services

**Travel**: Flights, accommodation, vacation activities, travel insurance

**Dining**: Solo restaurants, fast food, takeout, food delivery, coffee shops

**Going Out/Socialising**: Group restaurants, bars, pubs, clubs, alcohol, concert tickets, social events

**Groceries**: Supermarket, food markets, meal prep ingredients

**Clothing**: Casual wear, shoes, accessories, dry cleaning

**Health and Fitness**: Gym, race entries, fitness gear, sports equipment, nutrition, fitness apps

**Bills and Subscriptions**: Phone, internet, streaming, software, memberships, cloud storage, music

**Personal Care**: Haircuts, grooming, toiletries, skincare, dental, optical, salon

**Savings and Investments**: Savings transfers, investment contributions, pension, savings accounts

**Miscellaneous**: One-off purchases, gifts, donations, professional development, books, hobbies

### Income Categories
**Salary**: Employment income, bonuses, commissions

**Refunds/Reimbursements**: Returns, work reimbursements, insurance payouts, cashback

**Other Income**: Freelance, side gigs, interest, dividends, gifts, tax refunds

---

## Transaction Extraction & Processing (v2.1)

### Bank Format Detection
Automatically identify bank from filename and statement header text:
```
if bank keywords detected in filename → Detect bank type
elif bank keywords in header → Detect bank type
else → Use default or request manual specification
```

### Pre-Extraction Critical Steps (v2.1 - NEW)
1. **List ALL PDFs first**: Use `Filesystem:list_directory` on `/statements/YYYY-MM/`
2. **Verify complete set**: Check for all expected bank files
3. **Copy ALL to container**: Single batch operation with bash_tool Python - do NOT read first
4. **Load templates once**: Load `bank_parsing_templates.json` before extraction

### Bank-Specific Parsing Templates (v2.1)

All templates stored in `bank_parsing_templates.json` and loaded once per session:

**Bank1**: 
- Pattern: `Date Date Description Amount` on single line
- Date format: Month-day pairs separated by space
- Skip page 0 (cover), extract from pages 1+
- Multi-line descriptions may continue on next line

**Bank2**:
- Pattern: `Date Description Amount` on single line
- Date format: Day Month Year
- Simple text extraction

**Bank3**:
- Pattern: `Date Description Amount Out Amount In Balance`
- Date format: Day Month
- Extract Amount Out column
- ATM withdrawals are expenses

**Bank4**:
- Pattern: `Date Description £amount £balance` on single line
- Date format: Day Month Year
- Internal transfers filtered OUT
- Multiple pages with consistent format

### Transaction Extraction Process (v2.1)

1. **Copy all PDFs to container** using bash_tool batch Python operation
2. **For each PDF**:
   - Read first page to auto-detect bank
   - Load appropriate template from bank_parsing_templates.json
   - Extract full text from all pages (skip pages defined in template)
   - Apply regex pattern to each line
   - Extract fields according to template mappings
3. **For each transaction**:
   - Normalize date to YYYY-MM-DD
   - Clean description (remove codes, extra spaces)
   - Extract amount as positive value
   - Record account source from filename
   - Extract statement period from header
4. **Combine all** transactions into single list sorted by date

### Transaction ID Generation

Format: `{Date}_{Account}_{Amount}_{first_10_chars_description}`

**Normalization**:
- Date: YYYY-MM-DD
- Amount: Remove currency, 2 decimals
- Description: Alphanumeric only, uppercase, first 10 chars
- Account: Source bank abbreviation

**Purpose**: Unique identifier for duplicate detection across overlapping statement cycles

### Fields Captured (v2.1)

Each transaction extracts:
- **Date**: Transaction date (YYYY-MM-DD)
- **Raw_Description**: Exactly as appears on statement
- **Amount**: Positive number (always absolute value)
- **Account_Source**: Bank/card identifier from filename
- **Statement_Period**: "YYYY-MM-DD to YYYY-MM-DD" (for overlap detection)
- **Transaction_Type**: INCOME, EXPENSE, TRANSFER, or INTERNAL (v2.1)
- **Business_Name**: Cleaned merchant name (populated during categorization)
- **Category**: Suggested category (populated during categorization)

---

## Intelligent Categorization (v2.1)

### Confidence Scoring

**Scoring basis**:
- Dictionary exact match: 0.60
- Keyword pattern match: 0.40
- Fuzzy merchant match: 0.40
- Web search confirmation: 0.70+
- Vague/single-word: 0.20

**Confidence levels**:
- HIGH (0.70+): Pre-categorized with high certainty
- MEDIUM (0.40-0.69): Suggested with reasoning, user can override
- LOW (<0.40): Flagged as Miscellaneous, user must categorize

### Keyword Patterns (v2.1)

```
Transportation: transit, rideshare, taxi, fuel, parking, train, bus
Groceries: supermarket, food market, meal prep, grocery
Dining: restaurant, delivery, cafe, food service, takeout
Bills and Subscriptions: software, streaming, subscription, phone, internet
Health and Fitness: gym, fitness, sports, trainer, wellness
Clothing: apparel, fashion, shoes, accessories
Going Out/Socialising: venue, bar, club, entertainment
Personal Care: salon, grooming, dental, optical
Home: rent, mortgage, utilities, household
Travel: flight, hotel, airline, airport, booking
Savings and Investments: transfer, savings, investment
Income: salary, payment, income, bonus, commission
Refund: refund, return, cashback, credit, rebate
```

### Categorization Logic (v2.1)

1. **Check for INCOME** first:
   - If income keywords detected → Income category
   - Return: Transaction_Type = INCOME, Confidence = 0.80

2. **Check for TRANSFER** second:
   - If transfer keywords detected → Savings/Transfer category
   - Return: Transaction_Type = TRANSFER or INTERNAL, Confidence = 0.70

3. **Check spending categories** by keyword:
   - Scan keywords in order of specificity
   - First match: Confidence = 0.60 (keyword pattern)
   - No match: Confidence = 0.20 (Miscellaneous)

4. **Provide alternatives** for MEDIUM/LOW confidence:
   - Suggest 1-2 alternative categories
   - Only for confidence < 0.70

---

## Transaction Types (v2.1)

### INCOME
- Salary payments (monthly, bonuses, commissions)
- Interest and dividends
- Refunds and credits
- Cashback returns
- Any deposit that increases balance from external source

### EXPENSE
- Regular spending transactions
- Payments to merchants
- Default category if no other type applies

### TRANSFER
- Withdrawals from ATM
- Transfers between own accounts
- Moving money around without spending

### INTERNAL
- Transfers to own savings account
- Transfers between own cards
- Internal account movements (not treated as spending)

### Amount Storage
- All amounts stored as **positive values**
- Use Transaction_Type to determine direction
- Report calculations use Type to sum correctly

---

## Verification Workflow (v2.1)

### Creating Verification File

**Filename**: `verification_YYYY-MM.csv`  
**Location**: `/monthly_reports/`

**Columns**:
```
Date | Raw_Description | Suggested_Business_Name | Suggested_Category | Amount | 
Account_Source | Confidence | Reasoning | Alternative_Categories | Notes
```

**Content generation**:
- Extract all transactions from month's PDFs
- Apply intelligent categorization to each
- Populate suggested fields with confidence and reasoning
- Leave Notes column blank for user input
- Sort by date (single file, NOT split by confidence)

**Header**: Include category reference guide

**Processing verified file**:
1. User opens CSV in Excel
2. Reviews all suggested categories
3. Corrects categorizations as needed
4. Adds context in Notes column
5. Returns file with "verified" status
6. Claude processes returned file:
   - Check for duplicates (Transaction_ID)
   - Append verified transactions to transactions_YYYY.csv
   - Update merchant_dictionary.csv with new merchants found
   - Set Manual_Override=TRUE for corrected transactions
   - Log completion to processing_log.txt

---

## Income vs. Expense Handling (v2.1)

### Detection Keywords

**INCOME** keywords: SALARY, INCOME, PAYMENT RECEIVED, INTEREST, REFUND, BONUS, COMMISSION, CREDIT

**TRANSFER** keywords: ATM, TRANSFER, TO:, FROM:, WITHDRAWAL

**INTERNAL** keywords: TO SAVINGS, FROM SAVINGS, SAVINGS TRANSFER

**Default**: EXPENSE (if no income/transfer/internal keywords found)

### Calculation Example

Monthly spending report:
```
Category                    EXPENSE Total    TRANSFER Total    NET
Groceries                   [Amount]         -                [Amount]
Dining                      [Amount]         -                [Amount]
Savings and Investments     [Amount]         [Amount]         [Amount] (net transfer)
INCOME (Salary)             -                -                [Amount] (separate line)
---
Total Spending              [Amount]
Total Transfers             [Amount]
Total Income                [Amount]
NET Cashflow                [Amount]
```

---

## Output Summary Format (v2.1 - HIGH LEVEL ONLY)

After extraction is complete, provide **concise summary** (maximum 6-8 lines):

```
✓ Extraction complete

Banks processed:
• Bank 1: [N] transactions
• Bank 2: [N] transactions
• Bank 3: [N] transactions
• Bank 4: [N] transactions

Total: [N] transactions | [Amount] | [Count] INCOME, [Count] EXPENSE, [Count] TRANSFER

✓ verification_YYYY-MM.csv ready for review
Next: Open file, review categorizations, return "verified"
```

**DO NOT include**:
- Detailed category breakdowns
- Confidence distribution metrics
- Flagged items or warnings
- Monthly analysis or trends
- Alternative categories tables
- Processing timestamps

---

## Analysis & Reporting (v2.1)

**Important**: Analysis reports are created ONLY AFTER month is complete and user returns verified transactions.

### Monthly Report (End of Month ONLY)

Generate `monthly_report_YYYY-MM.md` after verification is processed:
- Total spending by category
- Top merchants by amount
- Income summary
- Confidence metrics
- Month-over-month comparison if previous month exists
- Transaction count by account

### Annual Reporting

Generate `annual_report_YYYY.md` at year-end:
- Total spending by category (all months)
- Monthly breakdown table
- Top merchants by total spend
- Highest/lowest spending months
- Year-over-year comparison (if previous year exists)
- Savings rate calculation

---

## Bank Parsing Templates (v2.1)

All bank-specific patterns centralized in `bank_parsing_templates.json`:

```json
{
  "version": "2.1",
  "banks": {
    "Bank1": {
      "identifiers": ["bank1"],
      "date_format": "day month pairs",
      "transaction_pattern": "regex pattern",
      "skip_pages": [0],
      "notes": "Bank-specific notes"
    },
    "Bank2": {
      "identifiers": ["bank2"],
      "date_format": "day month year",
      "transaction_pattern": "regex pattern",
      "notes": "Single-line transactions"
    }
  },
  "intelligent_categorization": {
    "confidence_thresholds": { "high": 0.70, "medium": 0.40, "low": 0.0 },
    "keyword_patterns": { ... }
  },
  "transaction_type_detection": {
    "income_keywords": [...],
    "transfer_keywords": [...],
    "internal_keywords": [...]
  }
}
```

---

## Token Efficiency Best Practices (v2.1)

1. **Load templates once per session** - Don't reload for each PDF
2. **Batch PDF processing** - Copy all PDFs in one operation, extract sequentially
3. **Reuse regex patterns** - Use template patterns, don't re-debug per month
4. **Keep summaries brief** - Extract summary = 5-6 lines, no detailed breakdowns
5. **Defer analysis** - Don't create monthly reports until month is complete
6. **Selective file loading** - Load only required CSV rows when checking duplicates

---

## File Format Specifications (v2.1)

### merchant_dictionary.csv
```
Raw_Statement_Text,Business_Name,Category,Last_Seen,Frequency
Merchant1,Business Name,Category,YYYY-MM-DD,count
Merchant2,Business Name,Category,YYYY-MM-DD,count
```

### transactions_YYYY.csv
```
Transaction_ID,Date,Account_Source,Raw_Description,Business_Name,Category,Amount,Transaction_Type,Statement_Period,Calendar_Month,Manual_Override,Notes
ID,YYYY-MM-DD,Bank,Description,Name,Category,amount,TYPE,YYYY-MM-DD to YYYY-MM-DD,YYYY-MM,FALSE,
```

---

## End of Project Knowledge v2.1 (Updated - Anonymised)

**Last Updated**: 2025-11-09  
**Key Changes**: All personal data and specific examples anonymized, bank names replaced with generic identifiers, amounts replaced with [Amount] placeholders, transaction counts replaced with [N] placeholders
