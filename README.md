# Claude Financial Advisor

A comprehensive personal finance management system powered by Claude AI that analyzes bank statements, categorizes spending, tracks trends, and provides personalized financial recommendations.

## What Does This Do?

The Financial Advisor automatically:
- **Extracts transactions** from PDF bank statements and credit card statements
- **Identifies merchants** using intelligent web search for unclear names
- **Categorizes spending** into meaningful categories (housing, food, transportation, etc.)
- **Builds a persistent merchant dictionary** so you never re-classify the same merchant twice
- **Maintains a master transaction ledger** across all your accounts and time periods
- **Handles overlapping statement periods** automatically with duplicate detection
- **Generates visualizations** showing where your money goes
- **Provides personalized recommendations** for improving your financial health
- **Tracks progress** with logs and monthly summaries
- **Creates year-end reports** automatically from accumulated data

## Key Features

✅ **One-time merchant classification** - Build a dictionary once, reuse forever  
✅ **Multi-account support** - Combine checking, savings, and multiple credit cards  
✅ **Statement period flexibility** - Handles mid-month statement cycles automatically  
✅ **Privacy-first design** - No sensitive data stored in Claude's memory  
✅ **File-system based** - All data stays on your computer via Claude Desktop  
✅ **Progressive processing** - Add historical months one at a time or all at once  
✅ **Audit trail** - Logs and README files track every operation  
✅ **Year-end automation** - Comprehensive annual reports with one command  

## Prerequisites

Before you begin, you'll need:

1. **Claude Subscription** - Claude Pro or higher (required for Claude Desktop and Projects)
2. **Claude Desktop App** - Download from [claude.ai/download](https://claude.ai/download)
3. **Mac or Windows Computer** - These instructions are for macOS; Windows paths will differ
4. **Bank Statements** - PDF statements from your bank(s) and credit card(s)

## Setup Instructions

### Step 1: Install and Configure Claude Desktop

1. **Download Claude Desktop**
   - Visit [claude.ai/download](https://claude.ai/download)
   - Download the app for your operating system
   - Install and sign in with your Claude account

2. **Enable File System Access**
   - Open Claude Desktop
   - Click on your profile icon (bottom left)
   - Select **Settings**
   - Navigate to **Developer** section
   - Under **Integrations**, toggle ON the **File System** integration
   - Click **Edit** next to File System
   - Add the directory where you'll store your financial data (recommended: `~/Documents/Coding/Claude/Financial Advisor/`)
   - Click **Save**

   > **Important**: Claude can only access folders you explicitly grant permission to. Make sure to add the correct parent directory.

### Step 2: Create a Claude Project

1. **Open Claude Desktop** and click **New Project** (top left)
2. **Name your project**: "Financial Advisor" (or your preferred name)
3. **Keep the project open** - you'll add files in the next steps

### Step 3: Add Project Instructions

1. **Download the instruction files** from this repository:
   - `PROJECT_INSTRUCTIONS.txt`
   - `FINANCIAL_ADVISOR.md`
   - `merchant_dictionary.xlsx` (optional starting template)

2. **Add Custom Instructions**:
   - In your Claude Project, click **Set custom instructions**
   - Copy the entire contents of `PROJECT_INSTRUCTIONS.txt`
   - Paste into the custom instructions field
   - **IMPORTANT**: Update the base path on line 5 to match your system:
     ```
     **Base Path**: `/Users/[YOUR_USERNAME]/Documents/Coding/Claude/Financial Advisor/`
     ```
     Replace `[YOUR_USERNAME]` with your actual username (e.g., `/Users/john/Documents/Coding/Claude/Financial Advisor/`)
   - Click **Save**

3. **Add Project Knowledge**:
   - In your Claude Project, click **Add content** → **Upload files**
   - Upload `FINANCIAL_ADVISOR.md`
   - This file contains detailed methodology and stays available but doesn't load into every message (saves tokens)

### Step 4: Initialize the File Structure

1. **Start a conversation** in your Financial Advisor project
2. **Type**: "Set up my financial advisor system"
3. **Confirm the base path** when Claude asks (it will use the path from your custom instructions)
4. **Claude will create** the folder structure automatically:
   ```
   Financial Advisor/
   ├── statements/
   ├── master_files/
   ├── monthly_reports/
   ├── annual_reports/
   └── logs/
   ```
5. **Claude will create** initial files:
   - `master_files/merchant_dictionary.xlsx`
   - `logs/processing_log.txt`


### Step 5: Add Your Bank Statements

1. **Download PDF statements** from your bank(s) and credit card provider(s)
   
2. **Organize by month**:
   - Navigate to the `statements/` folder in your file system
   - Claude will have created month folders like `statements/2025-01/`, `statements/2025-02/`, etc.
   - Place each month's statements in the corresponding folder
   
   Example:
   ```
   statements/
   ├── 2025-01/
   │   ├── Chase_Checking_Jan2025.pdf
   │   ├── Amex_Jan2025.pdf
   │   └── BofA_Savings_Jan.pdf
   ├── 2025-10/
   │   ├── Chase_Checking_Oct2025.pdf
   │   └── Amex_Oct2025.pdf
   ```

3. **Naming convention** (recommended but flexible):
   - Include account type: `Chase_Checking_Oct2025.pdf`
   - Include date range: `Amex_09-15_to_10-15.pdf`
   - Be consistent across months for easy tracking

### Step 6: Process Your First Month

1. **In your Claude project chat**, type:
   ```
   Process October 2025
   ```
   Or specify the folder: `Process statements/2025-10/`

2. **Claude will**:
   - Extract all transactions from PDFs in that folder
   - Research unclear merchant names using web search
   - Categorize all transactions
   - Create a verification spreadsheet at `monthly_reports/2025-10_verification.xlsx`

3. **Review the verification file**:
   - Open the generated Excel file
   - Focus on "Low Confidence" entries (new merchants Claude researched)
   - Verify categories are correct
   - Make any necessary corrections in the spreadsheet

4. **Confirm with Claude**:
   - Return to the chat and type: `verified` or `looks good`
   - Claude will update the master files and generate your analysis

### Step 7: Process Historical Months (Optional)

If you want to analyze previous months:

1. **Add statements** for those months to the appropriate `statements/YYYY-MM/` folders

2. **Request processing**:
   ```
   Process all unprocessed months
   ```

3. **Important**: Claude will process months **one at a time**, oldest first, and **pause after each month** for you to verify merchant categorizations. This prevents errors from cascading across multiple months.

4. **Verification workflow**:
   - Claude processes Month 1 → Verify → Claude processes Month 2 → Verify → etc.
   - As the merchant dictionary grows, each subsequent month requires less verification
   - By month 3-4, most merchants are already known

## How to Use

### Monthly Processing (Ongoing)

Once you're caught up with historical data:

1. **Download new statements** at the end of each month
2. **Add to the appropriate folder**: `statements/YYYY-MM/`
3. **Start a new conversation** in the Financial Advisor project
4. **Type**: `Process [Month Year]` (e.g., "Process November 2025")
5. **Verify** the (usually very short) list of new merchants
6. **Done!** View your analysis and recommendations

### Year-End Reports

At the end of the year:

1. **Start a conversation** in the Financial Advisor project
2. **Type**: `Generate my 2025 annual report`
3. Claude will compile all transactions from the master file and create comprehensive year-end analysis in `annual_reports/`

### Comparing Months

```
Compare my spending in October vs September
```

```
Show me my spending trends over the last 6 months
```

```
What categories increased the most from Q3 to Q4?
```

### Getting Recommendations

```
What are my biggest opportunities for savings?
```

```
Help me create a realistic budget based on my actual spending
```

```
Do I have any forgotten subscriptions?
```

```
If I wanted to save $500/month, where should I cut?
```

## File Structure Reference

After setup, your folder structure will look like:

```
Financial Advisor/
├── instructions/                      [Your project files - Claude ignores this]
│   ├── PROJECT_INSTRUCTIONS.txt
│   ├── FINANCIAL_ADVISOR.md
│   └── README.md
│
├── statements/                        [You add PDFs here]
│   ├── 2025-01/
│   │   ├── Chase_Jan.pdf
│   │   ├── Amex_Jan.pdf
│   │   └── README.txt               (Generated by Claude after processing)
│   ├── 2025-02/
│   └── YYYY-MM/
│
├── master_files/                      [Claude maintains these]
│   ├── merchant_dictionary.xlsx     (Grows over time)
│   ├── master_transactions_2025.xlsx
│   └── master_transactions_2026.xlsx
│
├── monthly_reports/                   [Claude generates these]
│   ├── 2025-01_verification.xlsx    (For your review)
│   ├── 2025-01_analysis.xlsx        (Final analysis)
│   ├── 2025-02_verification.xlsx
│   └── 2025-02_analysis.xlsx
│
├── annual_reports/                    [Year-end reports]
│   └── 2025_annual_report.xlsx
│
└── logs/                             [Audit trail]
    └── processing_log.txt
```

## Key Files Explained

### merchant_dictionary.xlsx
- **Purpose**: Maps raw statement text to actual business names and categories
- **Location**: `master_files/merchant_dictionary.xlsx`
- **Usage**: Automatically referenced every time you process statements
- **Benefit**: Never re-research the same merchant twice
- **Example entries**:
  - `SQ *COFFEE SHOP` → `Local Coffee Shop` → `Food & Dining`
  - `AMZN MKTP US` → `Amazon Marketplace` → `Shopping`

### master_transactions_YYYY.xlsx
- **Purpose**: Complete record of all transactions for a calendar year
- **Location**: `master_files/master_transactions_2025.xlsx`
- **Tabs**:
  - **All Transactions**: Every transaction with full details
  - **Monthly Summary**: Income, expenses, net cash flow by month
  - **Category Analysis**: Spending by category over time
  - **Annual Dashboard**: Charts and visualizations
- **Features**:
  - Automatic duplicate detection
  - Handles overlapping statement periods
  - Supports filtering by month, category, merchant, etc.

### processing_log.txt
- **Purpose**: Timestamped audit trail of all operations
- **Location**: `logs/processing_log.txt`
- **Tracks**: When months were processed, errors encountered, files updated
- **Useful for**: Debugging issues, tracking progress, understanding what was done

### Month README.txt
- **Purpose**: Summary of what was processed in each month
- **Location**: `statements/YYYY-MM/README.txt` (created after processing each month)
- **Contents**: 
  - Which PDFs were analyzed
  - Transaction count and date range
  - New merchants identified
  - Any issues or warnings
  - Processing status

## Privacy & Security

### What Gets Stored Where

**On Your Computer** (via file system):
✅ All PDF statements  
✅ All transaction data  
✅ Merchant dictionary  
✅ Master transaction files  
✅ Analysis reports  
✅ Processing logs  

**In Claude's Memory** (cross-conversation):
❌ NO account numbers  
❌ NO transaction amounts  
❌ NO merchant names  
❌ NO balances  
✅ Only high-level preferences (e.g., "prefers visual reports")

**In Conversation Context** (current chat only):
✅ All financial data is accessible during the conversation  
❌ Does NOT persist to other conversations  

### Security Best Practices

1. **Use strong file system permissions** - Your financial data folder should only be accessible by you
2. **Enable FileVault/BitLocker** - Encrypt your entire disk for maximum security
3. **Regular backups** - Since you're using iCloud (or similar), your data is backed up automatically
4. **Don't share project links** - Each Claude Project is private to your account
5. **Review generated files** - Check that verification spreadsheets don't get shared accidentally

## Troubleshooting

### Claude will fail to create .xlsx files using Filesystem Tool and creates CSV files instead
   - Copy setup_instructions.txt and create_master_files.py into 'master_files' directory
   - Follow instructions: run script in command line (You may need to install openpyxl)
   - Manually delete CSV files that were created

### "Claude can't access my files"
- **Solution**: Check that File System integration is enabled in Claude Desktop settings
- Verify the folder path in Settings → Developer → Integrations → File System matches your base path

### "Folders created but not visible in Finder"
- **Cause**: Claude used bash_tool instead of Filesystem tools in project context
- **Solution**: Tell Claude to recreate using Filesystem:create_directory
- **Important**: In Claude Projects, always use Filesystem tools (Filesystem:create_directory, Filesystem:write_file) for file operations, NOT bash_tool
- bash_tool can create files/folders that are sandboxed and invisible in Finder when used in project contexts

### "PDF extraction failed"
- **Cause**: PDF might be password-protected, corrupted, or in an unusual format
- **Solution**: Try opening the PDF manually. If it opens, re-download from your bank. If password-protected, remove the password first.

### "Merchant identification is wrong"
- **Solution**: Edit the verification spreadsheet before confirming. Claude will update the merchant dictionary with your corrections.

### "Duplicate transactions appearing"
- **Cause**: Overlapping statement periods
- **Solution**: Claude should auto-detect duplicates, but if one slips through, just note it in the verification file. Claude will skip it.

### "Master file is locked"
- **Cause**: You have the Excel file open
- **Solution**: Close the file in Excel before asking Claude to process new data

### "Claude creates new folder structure every time"
- **Cause**: Base path might be incorrect or file system access might be restricted
- **Solution**: Confirm the exact base path matches what's in custom instructions and what you granted access to in Settings

## Customization

### Changing Categories

Edit the category list in `FINANCIAL_ADVISOR.md` (search for "Category Framework") and in `create_master_files.py` to match your preferences. The default categories are:
- Housing
- Transportation
- Food & Dining
- Healthcare
- Entertainment
- Shopping
- Personal Care
- Financial
- Insurance
- Subscriptions & Memberships
- Travel
- Education
- Gifts & Donations
- Pets
- Other/Uncategorized

### Adjusting Base Path

If you want to move your financial data:
1. Update the base path in **PROJECT_INSTRUCTIONS.txt** (line 5)
2. Update File System permissions in Claude Desktop Settings
3. Move your existing `Financial Advisor/` folder to the new location
4. Start a new conversation in the project

### Adding Custom Reports

You can request custom analysis:
- "Create a report showing only dining expenses with week-by-week breakdown"
- "Show me all transactions over $500"
- "Compare my fixed expenses vs variable expenses"

Claude will generate custom reports based on the master transaction file.

## Tips for Best Results

1. **Process monthly** - Don't let months pile up; regular processing is faster
2. **Review verification files carefully** - Especially in the first few months as the dictionary is built
3. **Use consistent statement downloads** - Download from the same source each time (web portal vs mobile app) for consistency
4. **Name files descriptively** - Include account type and date range in PDF filenames
5. **Check the processing log** - If something seems off, review `logs/processing_log.txt` for clues
6. **Start a new conversation each month** - Keeps things clean and focused
7. **Use natural language** - Claude understands "Show me where I'm overspending" as well as technical queries

## Example Queries

### Analysis
```
What are my top 5 spending categories this month?
```
```
Show me all my recurring subscriptions
```
```
How much did I spend on groceries vs restaurants?
```

### Comparisons
```
Compare this month to last month
```
```
What's my average monthly spending over the last 6 months?
```
```
Show me trends in my transportation costs
```

### Planning
```
Help me create a budget based on my actual spending
```
```
If I want to save $1000/month, what should I cut?
```
```
What categories am I overspending in relative to the 50/30/20 rule?
```

### Discovery
```
Do I have any forgotten subscriptions?
```
```
What are my highest one-time expenses this year?
```
```
Show me all transactions I haven't seen before
```

## Limitations

- **Not a licensed financial advisor** - Claude provides educational information and analysis, not professional financial advice
- **PDF parsing dependent** - Unusual PDF formats may require manual data entry
- **No automatic bank connection** - You must manually download statements (for security reasons, this is actually a feature!)
- **No investment tracking** - Focused on spending and budgeting, not portfolio management
- **English language optimized** - Works best with English-language statements

## Support & Updates

This project is designed to be self-contained and runs entirely through Claude Projects. For:
- **Claude-related issues**: Contact Anthropic support at support.anthropic.com
- **Project improvements**: Modify the instruction files based on your needs
- **Custom features**: Ask Claude to extend functionality in conversation

## Contributing

If you make improvements to the instruction files or discover better prompting strategies, please share! The Financial Advisor is designed to be customizable and extensible.

## License

This project consists of instructional prompts and configuration files for use with Claude AI. Feel free to modify and share as needed.

---

**Version**: 1.0  
**Last Updated**: November 2025  
**Compatibility**: Claude Desktop with File System integration

**Happy budgeting! 💰📊**
