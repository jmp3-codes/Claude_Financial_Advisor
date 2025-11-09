# Claude Financial Advisor v2.1 (Updated)

A privacy-first personal finance management system powered by Claude AI that analyzes bank statements, categorizes spending, and provides personalized financial insights—all while keeping your data completely local.

**Version 2.1 Features:**
- ✅ Intelligent merchant categorization with confidence scoring
- ✅ Transaction_Type field (INCOME/EXPENSE/TRANSFER/INTERNAL)
- ✅ Automatic bank format detection
- ✅ Reusable bank parsing templates (load once, reuse for all PDFs)
- ✅ Context-aware verification with alternatives
- ✅ Enhanced statement period tracking for overlap detection
- ✅ Batch PDF processing (all files in one operation)
- ✅ Filesystem-first file access (never use bash_tool on user filesystem)
- ✅ Extraction-phase verification only (no analysis until month complete)
- ✅ High-level output summaries (5-6 lines, no detailed metrics)
- ✅ CSV-based workflow (no Excel dependencies)
- ✅ Token-efficient document structure

---

## Table of Contents

1. [What's New in v2.1](#whats-new-in-v21)
2. [Critical Processing Rules (v2.1 Updated)](#critical-processing-rules-v21-updated)
3. [Why This Exists](#why-this-exists)
4. [Quick Start](#quick-start)
5. [How It Works](#how-it-works)
6. [Monthly Workflow](#monthly-workflow)
7. [Category Framework](#category-framework)
8. [Intelligent Categorization (v2.1)](#intelligent-categorization-v21)
9. [Transaction Types (v2.1)](#transaction-types-v21)
10. [Setup Instructions](#setup-instructions)
11. [Usage Examples](#usage-examples)
12. [Troubleshooting](#troubleshooting)
13. [Privacy & Data Security](#privacy--data-security)
14. [Version History](#version-history)

---

## What's New in v2.1

### Improvements (Original)
- **Smart Categorization**: AI-powered suggestions with confidence scoring (High/Medium/Low). User confirms instead of entering from scratch
- **Bank Format Detection**: Automatically identifies bank from PDF headers/filenames, loads appropriate parsing template
- **Bank Parsing Templates**: Reusable regex patterns stored in `bank_parsing_templates.json`. No re-debugging each month
- **Transaction Types**: New field distinguishes INCOME, EXPENSE, TRANSFER, INTERNAL. Better income vs. expense reporting
- **Enhanced Verification**: Shows reasoning for category suggestions, alternative categories, and merchant search results
- **Statement Period Tracking**: Captures full date range from statement, improves duplicate detection across overlapping cycles
- **Confidence Metrics**: Verification shows confidence level so you know which suggestions need review
- **Context Window**: Verification includes reasoning ("Dictionary match" vs. "Web search confirmation" vs. "Keyword pattern")

### Critical Updates (v2.1 Updated - November 2025)
- **Filesystem First Rule**: Always use Filesystem tools to list and discover files, batch copy with bash_tool Python, then extract. NEVER use bash_tool to read user filesystem directly.
- **Process All Banks**: List directory first to find ALL PDFs, process EVERY bank file in month folder. Don't process only one bank when multiple exist.
- **Extraction-Only Verification**: Create ONLY `verification_YYYY-MM.csv` during extraction phase. Defer monthly analysis and reports to AFTER month is complete and user verification is done.
- **High-Level Output**: Chat summary limited to 5-6 lines showing banks processed, transaction counts, and next step. NO detailed tables, metrics, or analysis during extraction.

### User Experience Impact
- **80% less verification effort**: Pre-categorized transactions, user just confirms
- **No re-debugging**: Bank parsing templates mean instant processing each month
- **All banks in one run**: List directory first, process all banks automatically
- **Better income reporting**: INCOME/EXPENSE type field makes financial analysis clearer
- **Transparency**: See why each category was suggested, easily override with alternatives
- **Batch PDF processing**: All PDFs copied at once, processed in single flow
- **Concise output**: 5-line summary instead of detailed report (significant token savings)

---

## Critical Processing Rules (v2.1 Updated)

### BEFORE You Start Processing a Month

```
□ Use Filesystem:list_directory() to find ALL PDFs in statements/YYYY-MM/
□ Verify complete set (should have multiple PDFs from different sources)
□ Load bank_parsing_templates.json ONCE before processing
□ Batch copy ALL PDFs to container in single bash_tool operation
```

**WHY**: This ensures all bank data is processed together, no PDFs are missed, templates are loaded efficiently.

### DURING Extraction Phase

```
□ Process EVERY PDF in the month (not just the first one)
□ Auto-detect each bank format from filename/header
□ Apply intelligent categorization with confidence scoring
□ Detect transaction types (INCOME/EXPENSE/TRANSFER/INTERNAL)
□ Generate ONLY verification_YYYY-MM.csv file
□ Update processing_log.txt with extraction summary
□ Keep chat summary to 5-6 lines (banks processed, totals, next step)
```

**WHY**: This ensures complete data capture, accurate categorization, and efficient token use.

### AFTER User Returns Verified File

```
□ Read verified CSV
□ Check for duplicates using Transaction_ID
□ Append verified transactions to transactions_YYYY.csv
□ Update merchant_dictionary.csv with new merchants
□ NOW OKAY: Generate monthly_report_YYYY-MM.md with analysis
□ Set Manual_Override=TRUE for user-corrected transactions
□ Log completion to processing_log.txt
```

**WHY**: This phases analysis correctly, prevents duplicate processing, maintains audit trail.

### NEVER (Anti-Patterns)

```
❌ Use bash_tool to read files from user filesystem directly
❌ Extract PDF text before copying to container first
❌ Process only one bank when multiple files exist
❌ Create monthly README or detailed analysis during extraction phase
❌ Show detailed output with tables and metrics in chat extraction summary
❌ Use bash_tool to create/delete directories or CSV files
❌ Reload bank_parsing_templates.json for each PDF
❌ Extract from cover pages - use skip_pages from template
```

**WHY**: These anti-patterns cause missing data, confusing workflows, and excessive token use.

---

## Why This Exists

Manual expense tracking is tedious. Many commercial solutions require linking your bank accounts. Spreadsheets are better but still time-consuming.

**This system gives you:**
- **Privacy**: Your financial data never leaves your computer
- **Intelligence**: AI-powered categorization with confidence scoring
- **Efficiency**: Smart defaults reduce verification work by ~80%, processing time is minimal
- **Persistence**: One-time categorization of merchants, reused forever
- **Insights**: Spending analysis, trend detection, personalized recommendations
- **Simplicity**: Drop in PDFs, get analysis—no manual data entry
- **Flexibility**: Customize categories, modify past categorizations, generate reports on demand
- **Complete**: All banks processed automatically in one run

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
   Download PDF statements from ALL accounts → Add to `statements/YYYY-MM/` folders

6. **Process Full Month**  
   In project chat: `"Process [MONTH] [YEAR]"`
   - Claude lists all PDFs in month folder
   - Auto-detects each bank
   - Extracts transactions from ALL banks in one run
   - Generates verification file

7. **Review & Verify**  
   Open verification CSV in Excel → Review suggested categories with confidence levels → Override if needed → Save as CSV → Type "verified"

8. **Get Insights**  
   Ask questions: `"What are my top spending categories?"`

---

## How It Works

### The Three-Phase Process (v2.1 Enhanced)

**Phase 1: Full Month Extraction & Smart Categorization**
- Claude lists all PDFs in `statements/YYYY-MM/` directory (finds ALL files)
- Auto-detects bank format for EACH PDF
- Uses reusable parsing templates for fast extraction (load once per session, reuse for all PDFs)
- For each transaction from ALL banks:
  - Checks merchant dictionary
  - Applies intelligent categorization (keywords, fuzzy match, web search)
  - Assigns confidence score (High/Medium/Low)
  - Assigns Transaction_Type (INCOME/EXPENSE/TRANSFER/INTERNAL)
- Generates verification file with smart defaults
- Provides summary of banks processed and transaction counts
- **STOPS HERE** - No analysis yet, verification file ONLY

**Phase 2: User Verification (Streamlined)**
- You review verification CSV in Excel
- Most transactions pre-categorized with HIGH confidence (just confirm)
- MEDIUM confidence items show reasoning and alternatives
- Only LOW confidence items need manual categorization
- Add notes for one-time overrides
- Save as CSV and confirm with "verified"

**Phase 3: Analysis & Insights** (After verification is complete)
- Claude updates master transaction file (with duplicate detection)
- Updates merchant dictionary with new entries
- Generates monthly report with spending analysis
- Income vs. expense clearly separated
- Provides personalized recommendations

### Key Features (v2.1 Updated)

**Complete Coverage**: All banks processed in one run. List directory finds all source files - no PDFs missed.

**Smart Categorization**: Keywords, dictionary matching, fuzzy matching, and web search work together to suggest the right category with confidence level. HIGH confidence = pre-categorized, MEDIUM = user confirms, LOW = user decides.

**Bank Format Detection**: System automatically identifies bank and loads appropriate parsing template. No manual bank selection needed.

**Batch Processing**: All PDFs copied in one operation, all banks extracted in single flow. No re-debugging per PDF.

**Transaction Types**: INCOME (salary, interest), EXPENSE (spending), TRANSFER (ATM, account transfers), INTERNAL (savings movements). Makes income vs. expense reporting accurate.

**Merchant Dictionary**: Once categorized, merchants are remembered forever with confidence levels.

**Duplicate Detection**: Mid-month statement cycles handled automatically with composite Transaction IDs + statement period tracking.

**Context-Aware Verification**: Shows *why* a category was suggested ("Dictionary match" vs. "Web search" vs. "Keyword pattern"), making overrides easy.

**Efficient Output**: Summary output during extraction (significant token savings).

---

## Monthly Workflow

This is your repeating process for each month:

### Step 1: Download All Statements (~5 minutes)
1. Download PDF statements from ALL accounts
2. Create folder: `statements/YYYY-MM/`
3. Move ALL PDFs into folder

### Step 2: Process Full Month (~3 minutes)
```
"Process [Month] [Year]"
```

Claude will:
- List all PDFs in the month folder (shows you what's found)
- Auto-detect bank formats for EACH PDF
- Extract all transactions from ALL banks using parsing templates
- Apply intelligent categorization with confidence scores
- Generate verification file with suggested categories
- Show summary of what was processed

### Step 3: Verify & Categorize (~5-10 minutes, much faster than before!)
1. Open `verification_YYYY-MM.csv` in Excel
2. Review pre-categorized transactions
3. HIGH confidence items (0.70+): Just confirm with ✓
4. MEDIUM confidence items (0.40-0.69): Review reasoning, confirm or override
5. LOW confidence items (<0.40): Manually categorize
6. Add notes for special handling where needed
7. Save as CSV (Excel will warn about format - click "Yes" to keep CSV)
8. Return to Claude and type: `verified`

### Step 4: Get Analysis (~1 minute) - Now Ready
Once verified, Claude generates monthly report with:
- Spending breakdown by category
- Top merchants
- Income vs. expense summary
- Month-over-month comparison

### Step 5: Get Insights (~1 minute each)
Ask questions about your spending patterns and categories

---

## Category Framework

### Spending Categories
| Category | Includes | Examples |
|----------|----------|----------|
| **Home** | Rent, utilities, household items, furniture, home improvement | Rent, utility bill, furniture purchase, home insurance |
| **Transportation** | Public transit, fuel, maintenance, rideshare, insurance | Rideshare, fuel, public transit, car maintenance |
| **Travel** | Flights, hotels, vacation activities, travel insurance | Flight ticket, hotel booking, travel activities |
| **Dining** | Solo restaurants, fast food, coffee, takeout, food delivery | Restaurant, coffee shop, food delivery, fast food |
| **Going Out/Socialising** | Group dining, bars, clubs, concerts, social events | Group dining, venue, concert ticket, social event |
| **Groceries** | Supermarket, food markets, meal prep ingredients | Supermarket, food market, meal prep |
| **Clothing** | Casual wear, shoes, accessories, dry cleaning | Clothing store, shoes, jewelry, dry cleaning |
| **Health & Fitness** | Gym, fitness apps, sports equipment, supplements | Gym membership, fitness class, sports equipment, supplements |
| **Bills & Subscriptions** | Phone, internet, streaming, software, memberships | Phone bill, internet, software subscription, membership |
| **Personal Care** | Haircuts, grooming, toiletries, dental, optical | Haircut, salon, dental, skincare products |
| **Savings & Investments** | Savings transfers, investments, pension, ISA | Savings transfer, investment, pension contribution |
| **Miscellaneous** | One-off purchases, gifts, donations, hobbies | Gift, donation, hobby supplies, one-off purchase |

### Income Categories
| Category | Includes |
|----------|----------|
| **Salary** | Employment income, bonuses, commissions |
| **Refunds/Reimbursements** | Returned items, work reimbursements, insurance payouts, cashback |
| **Other Income** | Freelance, side gigs, interest, dividends, gifts, tax refunds |

---

## Intelligent Categorization (v2.1)

### How Smart Categorization Works

The system uses multiple signals to suggest the right category:

**1. Dictionary Matching (Confidence: 0.60)**
- Known merchants always map to same category
- Consistent categorization from merchant history
- High accuracy for frequently seen merchants

**2. Keyword Patterns (Confidence: 0.40)**
- If description contains specific keywords → Category
- Pattern-based detection for new merchants
- Moderate confidence until verified

**3. Fuzzy Merchant Matching (Confidence: 0.40)**
- Partial name matching against known merchants
- Handles slight variations in merchant names
- Moderate confidence until confirmed

**4. Web Search (Confidence: 0.70+)**
- For unknown merchants, search online for context
- Determines merchant type and location
- High confidence when search confirms category

### Confidence Levels

| Level | Confidence | Meaning | Your Action |
|-------|-----------|---------|-------------|
| **HIGH** | 0.70+ | Very likely correct | Review and confirm |
| **MEDIUM** | 0.40-0.69 | Probable, consider context | Review reasoning, confirm or override |
| **LOW** | <0.40 | Uncertain, ambiguous | Manually categorize or skip |

### Example Categorizations

```
Known Supermarket
- Dictionary: YES → Always Groceries (0.60)
- Keywords: "supermarket" pattern (0.40)
- Result: HIGH confidence (0.70+) → Groceries ✓

Restaurant Name
- Dictionary: NO (new merchant)
- Keywords: "restaurant" pattern (0.40)
- Web search: Confirms restaurant (0.70)
- Result: MEDIUM/HIGH confidence (0.65-0.75) → Dining ✓

Vague Merchant Code
- Dictionary: NO
- Keywords: Generic text detected but not specific (0.20)
- Location: Unclear
- Result: LOW confidence (0.30) → Miscellaneous, needs review ⚠️

Known Transit Provider
- Dictionary: YES → Always Transportation (0.60)
- Keywords: "transport" pattern (0.40)
- Result: HIGH confidence (0.70+) → Transportation ✓

Software Service
- Dictionary: NO (new merchant)
- Keywords: Suggests software (0.30)
- Web search: Confirms software service (0.75)
- Result: HIGH confidence (0.75+) → Bills & Subscriptions ✓
```

---

## Transaction Types (v2.1)

### What Are Transaction Types?

Transaction_Type distinguishes different kinds of financial movements:

| Type | Examples | How Detected |
|------|----------|--------------|
| **INCOME** | Salary, interest, gifts, refunds | Income-related keywords in description |
| **EXPENSE** | Regular spending | Default for non-income transactions |
| **TRANSFER** | ATM withdrawal, transfer to another account | Transfer-related keywords in description |
| **INTERNAL** | Movement between own accounts | Internal transfer keywords detected |

### Why It Matters

**Better Reporting**:
```
Total Income: [Amount]
Total Expenses: [Amount]
Net Cash Flow: [Amount]

Expenses by Category:
- Home: [Amount] (EXPENSE only)
- Groceries: [Amount] (EXPENSE only)
- Dining: [Amount] (EXPENSE only)

(ATM withdrawals and transfers excluded from "spending" analysis)
```

Without Transaction_Type, income and expenses would be mixed, making reporting confusing.

---

## Setup Instructions

### 1. Prepare Your Computer
Create the base folder:
```
~/Documents/Coding/Claude/Financial Advisor/
```

Subfolder structure will be created automatically during initialization.

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
4. Copy entire contents of `PROJECT_INSTRUCTIONS.txt`
5. **Update base path with your username** if needed
6. Paste into custom instructions field
7. Click "Add content" → Upload `FINANCIAL_ADVISOR.md` to Project Knowledge
8. Save

### 4. Initialize System
1. Start conversation in Financial Advisor project
2. Type: `"Set up my financial advisor system"`
3. Claude will create all folders and master files
4. Verify structure in Finder:
   ```
   Financial Advisor/
   ├── statements/
   ├── master_files/
   ├── monthly_reports/
   ├── annual_reports/
   └── logs/
   ```

### 5. Add Bank Statements (ALL ACCOUNTS)
1. Download PDF statements from ALL accounts
2. Create month folder: `statements/YYYY-MM/`
3. Move ALL PDFs for that month into the folder
4. Claude auto-detects bank formats for each PDF

---

## Usage Examples

### "Process [Month] [Year]"
Claude will:
- **List all PDFs** in `statements/YYYY-MM/` directory
- **Auto-detect each bank**
- **Load parsing templates** from `bank_parsing_templates.json` once
- **Extract from ALL banks** in single flow
- **Apply intelligent categorization** with confidence scores
- **Generate verification_YYYY-MM.csv** ready for review
- **Show summary**: Banks found, transaction counts, next step

Example output:
```
✓ Extraction complete

Banks processed:
• Bank 1: [N] transactions
• Bank 2: [N] transactions
• Bank 3: [N] transactions
• Bank 4: [N] transactions

Total: [N] transactions | [Amount] | [Breakdown by type]

✓ verification_YYYY-MM.csv ready for review
Next: Open file, review categorizations, return "verified"
```

### After You Verify: "I've reviewed and verified [Month]"
Claude will:
- Read your verified CSV
- Update merchant dictionary with new entries
- Append transactions to master file
- Check for duplicates (handles overlapping statement cycles)
- Generate monthly summary report

### "What are my top spending categories?"
Claude will:
- Load most recent month
- Filter for EXPENSE type only (excludes transfers/income)
- Aggregate by category
- Show top categories with percentages and totals

### "Compare [Month 1] to [Month 2]"
Claude will:
- Load both months
- Show category totals for each month
- Highlight significant changes
- Provide explanations for differences

### "Show me my subscriptions"
Claude will:
- Filter category "Bills and Subscriptions"
- Show all recurring charges
- Flag services with low usage patterns
- Suggest potential optimizations

### "Create a budget for [Month]"
Claude will:
- Calculate average spending by category
- Suggest allocation based on historical patterns
- Identify fixed vs. discretionary spending
- Set up tracking for the month

---

## Troubleshooting

### "PDFs not extracting correctly"
**Check**:
1. Bank name in filename matches bank_parsing_templates.json
2. PDF is not password-protected
3. Parsing template for this bank exists
4. Used Filesystem:list_directory() first to find PDF
5. Batch copied PDF to container with bash_tool before extraction

**Fix**: If new bank, add to bank_parsing_templates.json with regex pattern

### "Only one bank extracted"
**Cause**: Didn't check for all PDFs in directory first

**Fix**:
1. Use Filesystem:list_directory() on statements/YYYY-MM/
2. Verify all banks present in file listing
3. Reprocess - Claude should auto-detect and extract all banks

### "Transactions categorized wrong"
**Review**: Verification CSV shows reasoning for each suggestion
- "Dictionary match" = merchant known, should be accurate
- "Keyword pattern" = word-based guess, may need override
- "Web search" = looked up online, should be accurate

Override in Excel verification file and save as CSV.

### "Can't open CSV after Excel edit"
**Fix**: 
1. In Excel: File → Save As → CSV (not Excel format)
2. Excel will warn about compatibility - click "Yes"
3. Save, close, and use file

### "Duplicates appearing in transactions"
**Cause**: Statement overlap between months

**Solution**: Claude detects duplicates using Transaction_ID + Statement_Period. If still seeing duplicates:
1. Check Transaction_ID generation (should match format)
2. Verify statement period is captured correctly
3. Check for duplicate entries in master file

### "Can't see folders in Finder"
**Cause**: Folders created with bash_tool instead of Filesystem tools

**Fix**: Delete with bash_tool, recreate with `"Set up my financial advisor system"`

---

## Privacy & Data Security

### What Stays Local
**Everything financial**:
- Your PDF statements (never uploaded)
- Transaction amounts and dates
- Account details
- Merchant names and purchase details
- All CSV files

### What Claude Sees (In Conversation Only)
Claude processes your data in real-time but:
- Data is not permanently stored
- Each conversation is isolated (no cross-conversation retention)
- Financial details never added to memory
- When conversation ends, transaction details are forgotten

### Best Practices
1. **Don't sync to cloud** for maximum privacy
2. **Do encrypt your disk** (FileVault on Mac, BitLocker on Windows)
3. **Do use strong password** for user account
4. **Do backup regularly** to encrypted external drive

---

## Version History

### Version 2.1 Updated (November 2025) - Critical Workflow Fixes

**Critical Fixes**:
- ✅ Filesystem first rule: Always use Filesystem tools to discover files, batch copy with bash_tool, then extract
- ✅ Process all banks: List directory first, process EVERY PDF
- ✅ Extraction-only verification: Create verification file ONLY during extraction, defer analysis
- ✅ High-level output: Chat summary limited to 5-6 lines (significant token savings)

**User Impact**:
- No more missed banks or incomplete processing
- Cleaner workflow with clear phases
- Efficient chat output
- Consistent extraction methodology

**Files Updated**:
- `PROJECT_INSTRUCTIONS.txt` - Critical rules and phases
- `FINANCIAL_ADVISOR.md` - Pre-extraction steps and output format
- `v2.1_IMPLEMENTATION_GUIDE.md` - Code examples and common mistakes
- `README.md` - This file with integrated updates

### Version 2.1 (October 2025) - Smart Categorization & Auto-Detection

**Major Features**:
- ✅ Intelligent categorization with confidence scoring
- ✅ Automatic bank format detection
- ✅ Reusable bank parsing templates
- ✅ Transaction_Type field (INCOME/EXPENSE/TRANSFER/INTERNAL)
- ✅ Enhanced verification with reasoning
- ✅ Statement period tracking for overlap detection
- ✅ Context-aware category suggestions
- ✅ Token-efficient documentation

**User Impact**:
- Significantly faster verification (pre-categorized transactions)
- No re-debugging each month (parsing templates)
- Better income vs. expense reporting
- Transparent categorization (shows reasoning)

### Version 2.0 (September 2025) - CSV Simplification

**Features**:
- CSV-based workflow
- Filesystem tools for file operations
- Flexible category system
- Default + override merchant categorization
- Simplified verification workflow
- Enhanced duplicate detection

### Version 1.0 (Initial Release)

**Features**:
- Excel-based storage
- Basic merchant dictionary
- Monthly processing workflow
- Web search for merchant identification

---

## FAQ

**Q: Can I use Google Sheets instead of Excel?**
A: Yes! CSV files open in Google Sheets. Download, edit, upload back as CSV.

**Q: What if my bank isn't supported?**
A: Most banks can be added. Add parsing pattern to `bank_parsing_templates.json` with date/amount regex patterns.

**Q: How accurate is categorization?**
A: Very accurate for common merchants (HIGH confidence 0.70+). Unusual merchants may be MEDIUM confidence and need review. That's why the verification step exists.

**Q: Can I process multiple months at once?**
A: Process one at a time (oldest first) to prevent categorization errors. Each month takes approximately 10-15 minutes.

**Q: What if I have significant historical data?**
A: Process chronologically, one month at a time. First few months slower (building merchant dictionary). After that, very fast.

**Q: Can I reclassify past transactions?**
A: Yes! Edit the CSV file directly or ask Claude to reclassify groups of transactions.

**Q: Is this better than commercial alternatives?**
A: Different trade-offs. This gives complete privacy + AI insights but requires PDF downloads. Commercial alternatives auto-sync but require account linking.

**Q: Why should I download ALL statements instead of just one?**
A: To get complete financial picture in one run. Processing all accounts together is faster and more efficient than separate processing sessions.

**Q: What if my PDF extracts no transactions?**
A: First, verify Filesystem:list_directory found the PDF. Then check if it matches a bank_parsing_templates.json pattern. If new bank, add pattern with regex example from statement.

---

## Conclusion

You now have a powerful, private, AI-driven financial analysis system. Your data stays local, all your bank accounts are processed together, merchants are categorized with confidence scores, and you get insights that used to require expensive financial advisors.

**Next Steps**:
1. Complete setup
2. Download statements from ALL accounts for first month
3. Process full month (Claude auto-detects and extracts all banks)
4. Review and confirm categories (fast!)
5. Get insights
6. Set up monthly routine

**Remember**: This system provides tools for understanding your spending, but consult qualified professionals for personalized financial planning.

**Take control of your finances. One smart categorization at a time.** 💰

---

## Documentation Files

For more details, see:
- `PROJECT_INSTRUCTIONS.txt` - Rules, phases, and implementation rules
- `FINANCIAL_ADVISOR.md` - Methodology and technical details
- `v2.1_IMPLEMENTATION_GUIDE.md` - Code examples and common mistakes
- `bank_parsing_templates.json` - Bank-specific parsing patterns
