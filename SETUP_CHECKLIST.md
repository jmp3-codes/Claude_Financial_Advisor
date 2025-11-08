# Financial Advisor Setup Checklist

Use this checklist to track your setup progress:

## Prerequisites
- [ ] Claude Pro or higher subscription active
- [ ] Claude Desktop app downloaded and installed
- [ ] Signed into Claude Desktop with your account
- [ ] Bank statements downloaded as PDFs

## File System Configuration
- [ ] Opened Claude Desktop Settings (click profile icon)
- [ ] Navigated to Developer → Integrations
- [ ] Toggled File System integration ON
- [ ] Added folder path: `/Users/[YOUR_USERNAME]/Documents/Coding/Claude/Financial Advisor/`
- [ ] Confirmed folder permissions saved

## Project Setup
- [ ] Created new Claude Project
- [ ] Named project (e.g., "Financial Advisor")
- [ ] Downloaded instruction files:
  - [ ] PROJECT_INSTRUCTIONS.txt
  - [ ] FINANCIAL_ADVISOR.md
  - [ ] merchant_dictionary.xlsx (optional)

## Custom Instructions
- [ ] Clicked "Set custom instructions" in project
- [ ] Copied contents of PROJECT_INSTRUCTIONS.txt
- [ ] **UPDATED base path** with your actual username (line 5)
  - Original: `/Users/jackpage/Documents/Coding/Claude/Financial Advisor/`
  - Changed to: `/Users/[YOUR_USERNAME]/Documents/Coding/Claude/Financial Advisor/`
- [ ] Pasted into custom instructions field
- [ ] Clicked Save

## Project Knowledge
- [ ] Clicked "Add content" in project
- [ ] Uploaded FINANCIAL_ADVISOR.md
- [ ] Confirmed file uploaded successfully

## System Initialization
- [ ] Started new conversation in project
- [ ] Typed: "Set up my financial advisor system"
- [ ] Confirmed base path when Claude asked
- [ ] Verified folder structure was created
- [ ] Checked that these folders now exist:
  - [ ] statements/
  - [ ] master_files/
  - [ ] monthly_reports/
  - [ ] annual_reports/
  - [ ] logs/
- [ ] Confirmed initial files created:
  - [ ] master_files/merchant_dictionary.xlsx
  - [ ] logs/processing_log.txt

## Adding Statements
- [ ] Navigated to Financial Advisor/statements/ folder in Finder
- [ ] Identified which months to process (e.g., October 2025)
- [ ] Created month folders if needed (e.g., 2025-10/)
- [ ] Downloaded bank statements from all accounts
- [ ] Moved PDF files into appropriate month folders
- [ ] Confirmed files are in correct locations

## First Month Processing
- [ ] Returned to Claude project chat
- [ ] Typed: "Process [Month Year]" (e.g., "Process October 2025")
- [ ] Waited for Claude to extract and categorize transactions
- [ ] Received verification file in monthly_reports/
- [ ] Opened verification file in Excel
- [ ] Reviewed all categorizations (focus on "Low Confidence" items)
- [ ] Made any necessary corrections
- [ ] Returned to Claude and typed: "verified"
- [ ] Received analysis report
- [ ] Reviewed spending breakdown and recommendations

## Historical Processing (if applicable)
- [ ] Added statements for previous months to their folders
- [ ] Requested: "Process all unprocessed months"
- [ ] Processed Month 1 → Verified → Continued
- [ ] Processed Month 2 → Verified → Continued
- [ ] (Repeat for each month...)
- [ ] Confirmed all desired months are processed

## Verification & Testing
- [ ] Opened master_files/merchant_dictionary.xlsx
- [ ] Confirmed merchants are being added
- [ ] Opened master_files/master_transactions_2025.xlsx
- [ ] Confirmed transactions are present in "All Transactions" tab
- [ ] Checked logs/processing_log.txt
- [ ] Confirmed processing events are logged
- [ ] Tested a query: "What are my top spending categories?"
- [ ] Received meaningful response with data

## Ongoing Setup
- [ ] Bookmarked Financial Advisor project in Claude Desktop
- [ ] Set calendar reminder for monthly statement processing
- [ ] Organized bank/card websites for easy statement downloads
- [ ] Shared README.md with family/friends if desired

## Optional Enhancements
- [ ] Customized category list in FINANCIAL_ADVISOR.md
- [ ] Added custom analysis preferences
- [ ] Created backup strategy (if not using iCloud)
- [ ] Set up automated statement downloads (if available from bank)

---

## Troubleshooting Reference

**If Claude can't access files:**
→ Check Settings → Developer → Integrations → File System
→ Confirm folder path matches custom instructions

**If PDF won't parse:**
→ Try re-downloading from bank
→ Check if password-protected
→ Verify file isn't corrupted

**If merchant categorization seems wrong:**
→ That's why verification step exists!
→ Correct in verification file before confirming
→ Corrections update the merchant dictionary

**If you see duplicate transactions:**
→ Usually auto-detected, but flag during verification
→ Claude will skip duplicates in future processing

---

## Success Criteria

You've successfully set up the Financial Advisor when:
✅ Claude can access your file system
✅ Folder structure is created and populated
✅ At least one month is fully processed and verified
✅ Master files contain your transaction data
✅ You can ask questions and get meaningful analysis
✅ Monthly processing workflow is clear

---

## Next Steps

1. **Process remaining historical months** (if desired)
2. **Set up monthly routine** (download statements → process → review)
3. **Explore analysis capabilities** (try different queries)
4. **Customize recommendations** (tell Claude about your goals)
5. **Plan for year-end** (looking forward to that annual report!)

---

**Setup complete? Start asking questions about your spending!** 🎉

Example first questions:
- "Give me an overview of my finances"
- "What categories am I spending the most on?"
- "Show me any subscriptions I might have forgotten about"
- "Help me understand where my money is going"
