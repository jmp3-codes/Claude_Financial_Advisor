# Claude Financial Advisor v2.0

A privacy-first personal finance management system powered by Claude AI that analyzes bank statements, categorizes spending, and provides personalized financial insights—all while keeping your data completely local.

**Version 2.0 Features:**
- ✅ CSV-based workflow (no Excel dependencies)
- ✅ Simplified setup with filesystem tools
- ✅ Token-efficient processing
- ✅ Flexible category system
- ✅ Progressive monthly processing with duplicate detection
- ✅ Default + override merchant categorization

---

## Table of Contents

1. [Why This Exists](#why-this-exists)
2. [Quick Start](#quick-start)
3. [How It Works](#how-it-works)
4. [System Requirements](#system-requirements)
5. [Setup Instructions](#setup-instructions)
6. [Monthly Workflow](#monthly-workflow)
7. [File Structure](#file-structure)
8. [Category Framework](#category-framework)
9. [Usage Examples](#usage-examples)
10. [Troubleshooting](#troubleshooting)
11. [Privacy & Data Security](#privacy--data-security)
12. [Customization](#customization)
13. [Version History](#version-history)

---

## Why This Exists

Manual expense tracking is tedious. Mint and YNAB require linking your bank accounts. Spreadsheets are better but still time-consuming to maintain.

**This system gives you:**
- **Privacy**: Your financial data never leaves your computer
- **Intelligence**: AI-powered merchant identification and categorization
- **Persistence**: One-time categorization of merchants, reused forever
- **Insights**: Spending analysis, trend detection, and personalized recommendations
- **Simplicity**: Drop in PDFs, get analysis—no manual data entry
- **Flexibility**: Customize categories, modify past categorizations, generate reports on demand

All powered by Claude AI with your data stored locally in simple CSV files.

---

## Quick Start

**Time to first analysis: ~10 minutes**

1. **Install Claude Desktop** (if you haven't already)  
   Download from: https://claude.ai/download

2. **Enable Filesystem Access**  
   Settings → Developer → Integrations → Filesystem → ON  
   Add folder: `~/Documents/Coding/Claude/Financial Advisor/`

3. **Create a Claude Project**  
   - New Project → Name it "Financial Advisor"
   - Custom Instructions: Copy from `PROJECT_INSTRUCTIONS.txt` (update username in base path)
   - Upload to Knowledge: `FINANCIAL_ADVISOR.md`

4. **Initialize System**  
   In project chat: `"Set up my financial advisor system"`

5. **Add Your Statements**  
   Download PDF statements → Add to `statements/YYYY-MM/` folders

6. **Process First Month**  
   In project chat: `"Process October 2025"`

7. **Review & Verify**  
   Open verification CSV in Excel → Correct any categorizations → Save as CSV → Type "verified"

8. **Get Insights**  
   Ask questions: `"What are my top spending categories?"`

---

## How It Works

### The Three-Phase Process

**Phase 1: Extraction**
- Claude reads PDF bank statements from your filesystem
- Extracts transactions (date, description, amount, account)
- Checks against merchant dictionary for known merchants
- Researches new merchants using web search
- Generates verification file for your review

**Phase 2: Verification**
- You review the verification CSV in Excel
- Correct business names and categories as needed
- Add notes for one-time overrides (e.g., "This Amazon purchase was running shoes")
- Save as CSV and confirm

**Phase 3: Analysis**
- Claude updates master transaction file (with duplicate detection)
- Updates merchant dictionary with new entries
- Generates spending analysis and insights
- Provides personalized recommendations

### Key Features

**Merchant Dictionary**: Once categorized, merchants are remembered forever. "SQ *PRET A MANG" becomes "Pret a Manger → Dining" and applies automatically to future statements.

**Duplicate Detection**: Mid-month statement cycles? No problem. Claude automatically detects and skips duplicate transactions using composite Transaction IDs.

**Default + Override**: Dictionary stores default categories, but you can override specific transactions without changing the default (useful for multi-category merchants like Amazon).

**Progressive Processing**: Process months one at a time or bulk process historical data—Claude handles both workflows.

**CSV-Based**: All data stored in simple CSV files you can open in Excel, analyze with Python, or back up anywhere.

---

## System Requirements

**Required:**
- Claude Pro or higher subscription
- Claude Desktop app (Mac or Windows)
- PDF bank statements (not password-protected)
- ~50MB disk space for typical year of transactions

**Recommended:**
- Microsoft Excel or equivalent for verification (can use Google Sheets)
- Multiple months of statements for trend analysis
- Basic familiarity with file systems and folders

**Not Required:**
- Python installation (Claude handles all processing)
- Bank account linking
- Internet access for Claude (except for merchant research)

---

## Setup Instructions

### 1. Prepare Your Computer

Create the base folder:
```
~/Documents/Coding/Claude/Financial Advisor/
```

This is where all your financial data will live. You can choose a different location, but update the path in `PROJECT_INSTRUCTIONS.txt` accordingly.

### 2. Configure Claude Desktop

1. Open Claude Desktop
2. Click your profile icon → Settings
3. Navigate to: Developer → Integrations
4. Toggle **Filesystem** to ON
5. Click "Add folder"
6. Select: `~/Documents/Coding/Claude/Financial Advisor/`
7. Confirm permissions

### 3. Create Claude Project

1. In Claude Desktop, click "New Project"
2. Name it: "Financial Advisor"
3. Click "Set custom instructions"
4. Copy the entire contents of `PROJECT_INSTRUCTIONS.txt`
5. **IMPORTANT**: Update line 5 with your actual username:
   - Change: `/Users/jackpage/Documents/Coding/Claude/Financial Advisor/`
   - To: `/Users/[YOUR_USERNAME]/Documents/Coding/Claude/Financial Advisor/`
6. Paste into custom instructions field
7. Click "Add content" → Upload `FINANCIAL_ADVISOR.md` to Project Knowledge
8. Save

### 4. Initialize System

1. Start a new conversation in your Financial Advisor project
2. Type: `"Set up my financial advisor system"`
3. Claude will:
   - Confirm your base path
   - Create folder structure
   - Initialize master CSV files
   - Create processing log
4. Verify folders exist in Finder:
   ```
   Financial Advisor/
   ├── statements/
   ├── master_files/
   ├── monthly_reports/
   ├── annual_reports/
   └── logs/
   ```

### 5. Add Bank Statements

1. Download PDF statements from all your accounts (checking, credit cards, etc.)
2. Navigate to `Financial Advisor/statements/` in Finder
3. Create month folders using format: `YYYY-MM` (e.g., `2025-10` for October 2025)
4. Move PDFs into appropriate month folders:
   ```
   statements/
   ├── 2025-10/
   │   ├── amex_october.pdf
   │   ├── visa_october.pdf
   │   └── checking_october.pdf
   └── 2025-11/
       └── amex_november.pdf
   ```

**Tips:**
- You can process multiple accounts per month (Claude handles merging)
- Rename PDFs to something recognizable (not required but helpful)
- Statements should NOT be password-protected

---

## Monthly Workflow

This is your repeating process for each new month:

### Step 1: Download Statements (~2 minutes)

When your bank statements are available:
1. Download PDFs from each account
2. Create folder: `statements/YYYY-MM/`
3. Move PDFs into folder

### Step 2: Process Month (~5 minutes)

In your Financial Advisor project:
```
"Process October 2025"
```

Claude will:
- Extract all transactions from PDFs
- Match against merchant dictionary
- Research new merchants with web search
- Generate verification CSV

**⚠️ Claude will warn you to close files before proceeding—make sure master files are closed in Excel!**

### Step 3: Verify Categorizations (~10-20 minutes first month, ~5 minutes after)

1. Navigate to `monthly_reports/` folder
2. Open `verification_YYYY-MM.csv` in Excel
3. Review the categorizations:
   - Focus on "Low" confidence items first
   - Correct business names if needed (e.g., "SQ *COFFEE SH" → "Local Coffee Shop")
   - Adjust categories as needed
   - For multi-category merchants (Amazon, supermarkets), specify actual category
   - Add notes for one-time overrides: "Amazon purchase was running shoes"
4. Save file as CSV (Excel will warn—click "Yes" to keep CSV format)
5. Return to Claude and type: `"verified"`

**First month takes longer** because you're categorizing many merchants for the first time. Subsequent months are faster because merchants are remembered.

### Step 4: Review Analysis (~5 minutes)

Claude generates:
- Spending summary by category
- Top merchants
- Month-over-month comparisons (after first month)
- Insights and recommendations
- Monthly report saved to `monthly_reports/`

Ask follow-up questions:
```
"Show me my biggest spending increases this month"
"What subscriptions do I have?"
"Compare my dining spending to last month"
```

### Step 5: Done!

Master files are updated automatically. Next month, repeat from Step 1.

---

## File Structure

```
Financial Advisor/
├── statements/                          # You add PDFs here
│   ├── 2025-10/
│   │   ├── amex_october.pdf
│   │   ├── visa_october.pdf
│   │   └── checking_october.pdf
│   └── 2025-11/
│       └── amex_november.pdf
│
├── master_files/                        # Claude maintains these
│   ├── merchant_dictionary.csv          # Persistent merchant → category mappings
│   ├── transactions_2025.csv            # All your transactions for the year
│   └── categories.txt                   # Category definitions (editable)
│
├── monthly_reports/                     # Generated reports
│   ├── verification_2025-10.csv         # For your review before finalizing
│   ├── report_2025-10.md                # Analysis and insights
│   └── verification_2025-11.csv
│
├── annual_reports/                      # Year-end summaries
│   └── annual_report_2025.md
│
└── logs/                                # Audit trail
    └── processing_log.txt               # Timestamped log of all operations
```

### Key Files Explained

**merchant_dictionary.csv**: Your growing database of merchants
```csv
Raw_Statement_Text,Business_Name,Category,Last_Seen,Frequency
"SQ *PRET A MANG","Pret a Manger","Dining","2025-10-15",12
"AMZN MKTP","Amazon","Miscellaneous","2025-10-20",45
```

**transactions_2025.csv**: Complete transaction ledger
```csv
Transaction_ID,Date,Account_Source,Raw_Description,Business_Name,Category,Amount,Transaction_Type,Statement_Period,Calendar_Month,Manual_Override,Notes
"2025-10-15_Amex_12.50_SQ_*PRET_A","2025-10-15","Amex","SQ *PRET A MANG LONDON","Pret a Manger","Dining",12.50,"Expense","2025-10","2025-10",FALSE,""
```

**categories.txt**: Category definitions (fully customizable)
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

---

## Category Framework

### Default Spending Categories

**Home**: Rent, household items, furniture, utilities, home improvement, appliances, cleaning supplies, home insurance

**Transportation**: Fuel, parking, car maintenance, public transport, car insurance, vehicle registration, tolls

**Travel**: Flights, accommodation, vacation activities, travel insurance, luggage, foreign transaction fees during trips

**Dining**: Restaurants (solo), fast food, takeout, food delivery apps, coffee shops when alone

**Going Out/Socialising**: Restaurants with friends, bars, pubs, clubs, alcohol purchases, concert tickets, social events

**Groceries**: Supermarket shopping, food markets, meal prep ingredients

**Clothing**: Casual wear, shoes, accessories, dry cleaning, alterations

**Health and Fitness**: Gym membership, race entries, fitness clothing/gear, running shoes, nutrition supplements, sports equipment, fitness apps

**Bills and Subscriptions**: Phone, internet, streaming services, software subscriptions, memberships, cloud storage

**Personal Care**: Haircuts, grooming products, toiletries, skincare, dental care, optical care, salon services

**Savings and Investments**: Transfers to savings accounts, investment contributions, pension contributions, ISA deposits

**Miscellaneous**: Everything else not fitting above categories, one-off purchases, gifts, charitable donations

### Income Categories

**Salary**: Regular employment income, bonuses, commissions

**Refunds/Reimbursements**: Returned items, work expense reimbursements, insurance reimbursements, cashback, rewards

**Other Income**: Freelance work, side gigs, interest, dividends, gifts received, tax refunds

### Customizing Categories

You can modify categories anytime:

1. Edit `master_files/categories.txt` in any text editor
2. Add, remove, or rename categories
3. Save the file
4. Tell Claude about the change: `"I've updated my categories"`
5. Reclassify existing transactions if needed: `"Reclassify all 'Entertainment' as 'Going Out/Socialising'"`

---

## Usage Examples

### Analysis Queries

```
"What are my top spending categories?"
"Show me all transactions over £100 this month"
"Compare October to September"
"What's my average monthly spending on groceries?"
"Show me year-to-date totals by category"
```

### Merchant Queries

```
"List all my subscriptions"
"Show me all Amazon purchases this month"
"What did I spend at Tesco in October?"
"Find forgotten subscriptions" (recurring charges you might not use)
```

### Trend Analysis

```
"Show me spending trends for the last 3 months"
"Which categories increased the most this month?"
"What are my biggest spending changes year-over-year?"
"Visualize my monthly spending" (generates Python script for charts)
```

### Budget & Planning

```
"Help me create a budget based on my spending"
"What could I cut to save £200/month?"
"Recommend areas where I could reduce spending"
"Calculate my savings rate"
```

### Reporting

```
"Generate my annual report for 2025"
"Create a monthly summary for October"
"Show me Q4 spending overview"
"Export all transactions for tax purposes"
```

### Corrections & Updates

```
"Reclassify Amazon transactions in October as 'Health and Fitness'"
"Change 'Local Coffee Shop' category from 'Dining' to 'Going Out/Socialising'"
"Merge 'Transportation' and 'Travel' into one category"
```

---

## Troubleshooting

### "Claude can't access my files"

**Problem**: Claude says it can't find folders or files

**Solution**:
1. Check Settings → Developer → Integrations → Filesystem is ON
2. Verify folder path matches exactly what's in `PROJECT_INSTRUCTIONS.txt`
3. Try removing and re-adding the folder in Filesystem settings
4. Restart Claude Desktop

### "Folders created but not visible in Finder"

**Problem**: Claude says it created folders but you can't see them

**Cause**: Folders were created with bash_tool instead of Filesystem tools (incorrect project setup)

**Solution**:
1. In Claude project chat: `"Delete the Financial Advisor folder using bash_tool and recreate using Filesystem:create_directory"`
2. Claude will remove the invisible folders and recreate properly
3. Verify folders now appear in Finder

### "PDF extraction failed"

**Problem**: Claude can't extract transactions from your statement

**Possible Causes**:
- PDF is password-protected (remove password first)
- PDF is an image scan, not searchable text (bank may have text version)
- Uncommon bank format Claude hasn't seen before

**Solution**:
1. Try re-downloading statement from bank
2. Check if PDF text is selectable (if not, it's likely a scan)
3. Provide Claude with a different month to "learn" your bank's format
4. Last resort: Manual entry (rare, usually only for very unusual formats)

### "Duplicate transactions appearing"

**Problem**: Same transaction shows up multiple times

**Cause**: Duplicate detection failed (usually due to statement overlap without consistent dates/amounts)

**Solution**:
1. During verification, flag duplicates in Notes column: "DUPLICATE"
2. Tell Claude: `"Remove duplicates from October"`
3. Claude will compare Transaction_IDs and remove extras
4. Check future months process correctly

### "Categorization seems wrong"

**Problem**: Merchants being categorized incorrectly

**Cause**: This is normal for new merchants! That's why verification exists.

**Solution**:
1. Correct in verification CSV before confirming
2. Corrections update the merchant dictionary
3. Future occurrences will use corrected category
4. For one-time overrides, add note: "One-time purchase, don't update dictionary"

### "CSV file won't open after editing in Excel"

**Problem**: Edited verification file, now Claude can't read it

**Cause**: Excel saved as .xlsx instead of .csv

**Solution**:
1. Open file in Excel
2. File → Save As
3. Format: CSV (Comma delimited) (*.csv)
4. Save
5. Confirm in Claude: `"verified"`

### "Master files not updating"

**Problem**: Verified transactions but master files unchanged

**Solution**:
1. Make sure files were closed before Claude tried updating
2. Check `logs/processing_log.txt` for error messages
3. Verify file permissions (should be read/write)
4. Try re-running: `"Update master files with verified October data"`

---

## Privacy & Data Security

### What Stays Local

**Everything financial**:
- Your PDF statements (never uploaded to Claude)
- Transaction amounts and dates
- Account numbers and balances
- Merchant names and purchase details
- All CSV files

### What Claude Sees (In Conversation Only)

Claude processes your data in real-time during conversations but:
- Data is not permanently stored by Claude/Anthropic
- Each conversation is isolated (no cross-conversation data retention)
- Financial details are never added to Claude's memory system
- When conversation ends, transaction details are forgotten

### What Claude Remembers (Non-Financial Only)

Claude's memory system may store:
- Your category preferences
- Communication style preferences
- General financial goals (if you share them)
- Non-sensitive context about your situation

**Never stored**: Specific transaction data, account numbers, balances, amounts, or merchant names

### Web Search Privacy

When Claude researches new merchants:
- Only generic business names are searched (e.g., "Pret a Manger business")
- No personal or financial data included in searches
- Searches are for identification purposes only

### Best Practices

1. **Don't sync to cloud** if you want maximum privacy (financial data stays only on your computer)
2. **Do encrypt your disk** (FileVault on Mac, BitLocker on Windows)
3. **Do use strong password** for your computer user account
4. **Don't share statements** outside your Financial Advisor project
5. **Do backup regularly** to encrypted external drive if desired

### Comparison to Alternatives

| Feature | This System | Mint/YNAB | Manual Spreadsheets |
|---------|-------------|-----------|---------------------|
| Data Location | Your computer only | Their servers | Your computer/cloud |
| Bank Account Linking | No (PDF only) | Yes (full access) | No |
| AI Analysis | Yes (local processing) | Limited | No |
| Privacy Level | Highest | Lowest | High |
| Setup Effort | Medium | Low | High |
| Ongoing Effort | Low | Low | High |

---

## Customization

### Changing Categories

Edit `master_files/categories.txt` in any text editor:

```
SPENDING:
Home
Transportation
...your custom categories...
Miscellaneous

INCOME:
Salary
...your custom income types...
```

Save and tell Claude: `"I've updated my categories"`

### Reclassifying Past Transactions

```
"Reclassify all 'Dining' transactions at Pret a Manger as 'Going Out/Socialising'"
"Move all Amazon purchases from October to 'Health and Fitness'"
```

Claude will update both the specific transactions and the merchant dictionary.

### Custom Analysis

Request specific reports:
```
"Create a custom report showing weekday vs. weekend spending"
"Analyze my spending patterns by day of month"
"Show me purchases only between £20-£50"
"Generate a Python script to visualize my top 10 merchants"
```

### Budget Tracking

Set up budget monitoring:
```
"Set up budget tracking: £400 for groceries, £300 for dining"
"Alert me if I exceed budget in any category"
"Show me progress against my budget"
```

Claude will track against your budgets in future analyses.

### Multiple Years

System automatically handles year transitions:
- Keeps separate `transactions_YYYY.csv` for each year
- Maintains single `merchant_dictionary.csv` across years
- Can analyze cross-year trends: `"Compare 2025 to 2024"`

---

## Version History

### Version 2.0 (November 2025) - CSV Simplification

**Major Changes**:
- ✅ Switched from Excel (.xlsx) to CSV files for all data storage
- ✅ Eliminated need for Python scripts for basic operations
- ✅ Streamlined workflow using Filesystem tools exclusively
- ✅ Improved token efficiency in processing
- ✅ Added flexible category system with `categories.txt`
- ✅ Implemented default + override merchant categorization
- ✅ Simplified verification workflow (direct Excel editing of CSV)
- ✅ Enhanced duplicate detection with composite Transaction_ID

**Files Removed**:
- `create_master_files.py` (no longer needed)
- `SETUP_INSTRUCTIONS.txt` (consolidated into README)
- `QUICKSTART.md` (consolidated into README)
- `SETUP_CHECKLIST.md` (consolidated into README)
- `UPDATE_SUMMARY.md` (consolidated into README)
- `PERMISSION_FIX.md` (consolidated into README)

**Files Updated**:
- `PROJECT_INSTRUCTIONS.txt` - Complete rewrite for CSV workflow
- `FINANCIAL_ADVISOR.md` - Comprehensive methodology update
- `README.md` - Streamlined single-source documentation

**Breaking Changes**:
- Existing .xlsx files need conversion to CSV (one-time migration)
- Project instructions must be updated in existing projects

**Migration from v1.0**:
If you used the Excel-based version:
1. Export your .xlsx files to CSV format in Excel
2. Update project instructions with new `PROJECT_INSTRUCTIONS.txt`
3. Replace `FINANCIAL_ADVISOR.md` in project knowledge
4. Continue using system normally

### Version 1.0 (October 2025) - Initial Release

**Features**:
- Excel-based storage (.xlsx files)
- Python script for file creation
- Basic merchant dictionary
- Monthly processing workflow
- Web search for merchant identification
- Verification workflow
- Analysis and recommendations

---

## FAQ

**Q: Can I use this with Google Sheets instead of Excel?**
A: Yes! CSV files open in Google Sheets. Just download, edit, and upload back as CSV.

**Q: What if my bank doesn't provide PDF statements?**
A: Most banks offer PDF downloads. If yours doesn't, you can print to PDF from online banking. Last resort: Manual CSV entry (tedious but possible).

**Q: Can I process multiple accounts at once?**
A: Yes! Put all PDFs for a month in the same `YYYY-MM` folder. Claude will process all and merge transactions.

**Q: What about joint accounts or shared expenses?**
A: System treats all transactions as "yours." For shared expenses, consider either: (1) Process only your portion, or (2) Add notes to track which are shared.

**Q: How accurate is the merchant identification?**
A: Very accurate for common merchants. Unusual or local businesses may need manual verification (hence the verification step).

**Q: Can I use this for business expenses?**
A: Yes, but you'd want to customize categories for business purposes (add: "Client Entertainment", "Office Supplies", "Business Travel", etc.).

**Q: What if I have 2 years of historical statements?**
A: Process chronologically, one month at a time. First few months take longer (building merchant dictionary). After that, very fast.

**Q: Is this better than YNAB/Mint?**
A: Different trade-offs. This gives you complete privacy and AI-powered insights but requires PDF downloads. YNAB/Mint auto-sync but require bank account linking.

**Q: Can I share this with family/friends?**
A: Absolutely! Share these documentation files. Each person needs their own Claude Pro subscription and separate folder structure.

**Q: What happens if I forget to process a month?**
A: No problem! Process it anytime. Just add the PDFs to the correct `YYYY-MM` folder and tell Claude: `"Process [Month Year]"`

**Q: Can I delete old transactions?**
A: Yes, edit the CSV files directly. But keep them for year-end reports and trends. Consider archiving instead of deleting.

---

## Conclusion

You now have a powerful, private, AI-driven financial analysis system. Your data stays local, merchants are categorized once and remembered forever, and you get insights that used to require expensive financial advisors.

**Next Steps**:
1. Complete setup (if you haven't already)
2. Process your first month
3. Explore analysis capabilities
4. Set up monthly routine
5. Enjoy financial clarity!

**Questions or Issues?**
- Check Troubleshooting section
- Ask Claude in your project: `"I'm having trouble with..."`
- Review `PROJECT_INSTRUCTIONS.txt` and `FINANCIAL_ADVISOR.md` for detailed methodology

---

**Remember**: I'm an AI assistant, not a licensed financial advisor. This system provides tools for understanding your spending, but always consult qualified professionals for personalized financial planning advice.

**Take control of your finances. One PDF at a time.** 💰
