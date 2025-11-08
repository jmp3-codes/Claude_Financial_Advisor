# Financial Advisor - Quick Start Guide

## 5-Minute Setup

### 1. Install Claude Desktop
Download from: https://claude.ai/download

### 2. Enable File System
Settings → Developer → Integrations → File System → ON
Add folder: `~/Documents/Coding/Claude/Financial Advisor/`

### 3. Create Project
New Project → Name: "Financial Advisor"

### 4. Add Instructions
- Custom Instructions: Copy from `PROJECT_INSTRUCTIONS.txt` (update base path with your username)
- Upload to Knowledge: `FINANCIAL_ADVISOR.md`

### 5. Initialize System
**In project chat, type:** "Set up my financial advisor system"

### 6. Add Statements
Manually add PDF statements to: `statements/YYYY-MM/` folders

### 7. Process First Month
**In project chat, type:** "Process October 2025"

---

## Folder Structure (Auto-Created)

```
Financial Advisor/
├── statements/         → You add PDFs here
├── master_files/       → Claude maintains these
├── monthly_reports/    → Claude generates these
├── annual_reports/     → Year-end reports
└── logs/              → Processing history
```

---

## Monthly Workflow

1. Download statements → Add to `statements/YYYY-MM/`
2. New chat → "Process [Month Year]"
3. Review verification file
4. Confirm → "verified"
5. Done!

---

## Key Features

✅ One-time merchant classification  
✅ Automatic duplicate detection  
✅ Multi-account support  
✅ Privacy-first (data stays local)  
✅ Year-end reports with one command  
✅ Personalized recommendations  

---

## Example Queries

```
What are my top spending categories?
Compare this month to last month
Show me forgotten subscriptions
Help me create a budget
Generate my 2025 annual report
```

---

## Files You'll Receive

- `PROJECT_INSTRUCTIONS.txt` - Paste into Custom Instructions
- `FINANCIAL_ADVISOR.md` - Upload to Project Knowledge  
- `merchant_dictionary.xlsx` - Template (optional)
- `README.md` - Full documentation (this expands on everything)

---

## Need Help?

See full README.md for:
- Detailed setup instructions
- Troubleshooting guide
- Privacy & security info
- Customization options
- Example queries

---

**That's it! You're ready to take control of your finances with Claude.** 💰
