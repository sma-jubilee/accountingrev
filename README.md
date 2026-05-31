```react
import React, { useState, useEffect } from 'react';
import { 
  BookOpen, 
  Award, 
  ShoppingBag, 
  Gamepad2, 
  FileSpreadsheet, 
  HelpCircle, 
  Heart, 
  Coins, 
  CheckCircle2, 
  XCircle, 
  ArrowRight, 
  ChevronRight, 
  RotateCcw, 
  User, 
  Sparkles, 
  Clock, 
  ShieldAlert, 
  BookMarked,
  TrendingUp,
  Download,
  Flame,
  Volume2,
  VolumeX,
  Info
} from 'lucide-react';

// SOUND SYNTHESIZER FOR IMmersive ARCADE EXPERIENCE
const playSound = (type, muted) => {
  if (muted) return;
  try {
    const ctx = new (window.AudioContext || window.webkitAudioContext)();
    const osc = ctx.createOscillator();
    const gain = ctx.createGain();
    osc.connect(gain);
    gain.connect(ctx.destination);

    if (type === 'correct') {
      osc.type = 'sine';
      osc.frequency.setValueAtTime(523.25, ctx.currentTime); // C5
      osc.frequency.setValueAtTime(659.25, ctx.currentTime + 0.1); // E5
      osc.frequency.setValueAtTime(783.99, ctx.currentTime + 0.2); // G5
      gain.gain.setValueAtTime(0.15, ctx.currentTime);
      gain.gain.exponentialRampToValueAtTime(0.01, ctx.currentTime + 0.4);
      osc.start();
      osc.stop(ctx.currentTime + 0.4);
    } else if (type === 'wrong') {
      osc.type = 'sawtooth';
      osc.frequency.setValueAtTime(150, ctx.currentTime);
      osc.frequency.linearRampToValueAtTime(80, ctx.currentTime + 0.3);
      gain.gain.setValueAtTime(0.2, ctx.currentTime);
      gain.gain.exponentialRampToValueAtTime(0.01, ctx.currentTime + 0.3);
      osc.start();
      osc.stop(ctx.currentTime + 0.3);
    } else if (type === 'coin') {
      osc.type = 'sine';
      osc.frequency.setValueAtTime(987.77, ctx.currentTime); // B5
      osc.frequency.setValueAtTime(1318.51, ctx.currentTime + 0.08); // E6
      gain.gain.setValueAtTime(0.1, ctx.currentTime);
      gain.gain.exponentialRampToValueAtTime(0.01, ctx.currentTime + 0.25);
      osc.start();
      osc.stop(ctx.currentTime + 0.25);
    } else if (type === 'powerup') {
      osc.type = 'triangle';
      osc.frequency.setValueAtTime(220, ctx.currentTime);
      osc.frequency.exponentialRampToValueAtTime(880, ctx.currentTime + 0.5);
      gain.gain.setValueAtTime(0.15, ctx.currentTime);
      gain.gain.exponentialRampToValueAtTime(0.01, ctx.currentTime + 0.5);
      osc.start();
      osc.stop(ctx.currentTime + 0.5);
    }
  } catch (e) {
    console.log("Audio contexts not allowed yet");
  }
};

export default function App() {
  // USER STATE
  const [userName, setUserName] = useState(() => localStorage.getItem('acc_username') || "Star Pupil");
  const [isEditingName, setIsEditingName] = useState(false);
  const [coins, setCoins] = useState(() => parseInt(localStorage.getItem('acc_coins')) || 150);
  const [lives, setLives] = useState(() => parseInt(localStorage.getItem('acc_lives')) || 5);
  const [streak, setStreak] = useState(() => parseInt(localStorage.getItem('acc_streak')) || 0);
  const [equippedSkin, setEquippedSkin] = useState(() => localStorage.getItem('acc_skin') || 'classic');
  const [ownedSkins, setOwnedSkins] = useState(() => {
    try {
      return JSON.parse(localStorage.getItem('acc_owned_skins')) || ['classic'];
    } catch {
      return ['classic'];
    }
  });
  
  const [activeTab, setActiveTab] = useState('hub'); // hub, notes, deadclic, statement_builder, quiz, master_accounts
  const [soundMuted, setSoundMuted] = useState(false);
  const [selectedChapter, setSelectedChapter] = useState(1);
  const [selectedSubTopic, setSelectedSubTopic] = useState('summary'); // summary, ttable, step_by_step, format_template

  // GAME COIN REWARDS
  const rewardCoins = (amount) => {
    setCoins(prev => {
      const updated = prev + amount;
      localStorage.setItem('acc_coins', updated);
      return updated;
    });
    playSound('coin', soundMuted);
  };

  const deductCoins = (amount) => {
    setCoins(prev => {
      const updated = Math.max(0, prev - amount);
      localStorage.setItem('acc_coins', updated);
      return updated;
    });
  };

  // SAVE STATS
  useEffect(() => {
    localStorage.setItem('acc_username', userName);
    localStorage.setItem('acc_lives', lives);
    localStorage.setItem('acc_streak', streak);
    localStorage.setItem('acc_skin', equippedSkin);
    localStorage.setItem('acc_owned_skins', JSON.stringify(ownedSkins));
  }, [userName, lives, streak, equippedSkin, ownedSkins]);

  // SYSTEM MASCOT DIALOGUES BY MR. MAHDY
  const mascotLines = {
    welcome: `Hello, ${userName}! Ready to smash IGCSE Accounting today? Let's trace every double entry flawlessly!`,
    correct: "Superb! Your double entry logic is solid! Take your coins! đŞ",
    wrong: "Oh no! Remember the DEAD CLIC rule. Don't worry, let's learn and try again!",
    shop: "Spend your earned coins on premium badges, extra lives, or special accountant skins!",
    deadclic: "Remember: Debit Expenses, Assets, Drawings. Credit Liabilities, Income, Capital!",
    builder: "Formulate the standard IGCSE Income Statement and Statement of Financial Position step-by-step!"
  };
  
  const [mascotBubble, setMascotBubble] = useState(mascotLines.welcome);

  const changeMascotBubble = (msg) => {
    setMascotBubble(msg);
  };

  // 1. MASTER LIST OF ACCOUNTS (SYLLABUS COMPLIANT)
  const masterAccounts = [
    { name: "Premises", type: "Non-Current Asset", drCr: "Debit", subcat: "Non-Current Asset", desc: "Long-term land & buildings used for operations." },
    { name: "Fixtures and Fittings", type: "Non-Current Asset", drCr: "Debit", subcat: "Non-Current Asset", desc: "Shelving, lightning systems, and display units." },
    { name: "Office Equipment", type: "Non-Current Asset", drCr: "Debit", subcat: "Non-Current Asset", desc: "Computers, printers, and photocopiers." },
    { name: "Motor Vehicles", type: "Non-Current Asset", drCr: "Debit", subcat: "Non-Current Asset", desc: "Delivery vans, staff cars used in production." },
    { name: "Accumulated Provision for Depreciation", type: "Contra-Asset", drCr: "Credit", subcat: "Contra-Asset", desc: "Accumulated wear & tear offset to reduce asset value." },
    { name: "Inventory", type: "Current Asset", drCr: "Debit", subcat: "Current Asset", desc: "Goods held for resale (never call it Stock)." },
    { name: "Trade Receivables", type: "Current Asset", drCr: "Debit", subcat: "Current Asset", desc: "Amounts owed to us by credit customers." },
    { name: "Bank Balance (Positive)", type: "Current Asset", drCr: "Debit", subcat: "Current Asset", desc: "Cash stored securely in high street bank account." },
    { name: "Cash", type: "Current Asset", drCr: "Debit", subcat: "Current Asset", desc: "Physical currency kept on business premises." },
    { name: "Petty Cash", type: "Current Asset", drCr: "Debit", subcat: "Current Asset", desc: "Small cash float managed using the Imprest System." },
    { name: "Prepaid Expenses", type: "Current Asset", drCr: "Debit", subcat: "Other Receivables", desc: "Expenses paid early that benefit future periods." },
    { name: "Accrued Income", type: "Current Asset", drCr: "Debit", subcat: "Other Receivables", desc: "Income earned but not yet received." },
    { name: "Bank Loan", type: "Non-Current Liability", drCr: "Credit", subcat: "Non-Current Liability", desc: "Long-term loan payable after 12 months." },
    { name: "Mortgage", type: "Non-Current Liability", drCr: "Credit", subcat: "Non-Current Liability", desc: "Long-term secured loan for property purchase." },
    { name: "Trade Payables", type: "Current Liability", drCr: "Credit", subcat: "Current Liability", desc: "Amounts owed to suppliers for credit purchases." },
    { name: "Bank Overdraft", type: "Current Liability", drCr: "Credit", subcat: "Current Liability", desc: "Negative bank account balance payable on demand." },
    { name: "Accrued Expenses", type: "Current Liability", drCr: "Credit", subcat: "Other Payables", desc: "Expenses incurred in the period but not yet paid." },
    { name: "Income Received in Advance", type: "Current Liability", drCr: "Credit", subcat: "Other Payables", desc: "Unearned income received from a customer early." },
    { name: "Capital Account", type: "Capital/Equity", drCr: "Credit", subcat: "Capital", desc: "Owner's initial & ongoing cash investment in the firm." },
    { name: "Drawings Account", type: "Capital/Equity", drCr: "Debit", subcat: "Capital Adjustment", desc: "Owner's cash or stock withdrawals for personal use." },
    { name: "Partner A Capital Account", type: "Capital/Equity", drCr: "Credit", subcat: "Partnership Capital", desc: "Fixed capital balance invested by Partner A." },
    { name: "Partner A Current Account", type: "Capital/Equity", drCr: "Credit/Debit", subcat: "Partnership Capital", desc: "Partner's share of profits, interest on capital less drawings." },
    { name: "Ordinary Share Capital", type: "Capital/Equity", drCr: "Credit", subcat: "Company Equity", desc: "Nominal value of equity shares issued to the public." },
    { name: "Retained Earnings", type: "Capital/Equity", drCr: "Credit", subcat: "Company Equity", desc: "Cumulative profits kept in the business instead of dividends." },
    { name: "General Reserve", type: "Capital/Equity", drCr: "Credit", subcat: "Company Equity", desc: "Profits set aside for strategic expansion needs." },
    { name: "Accumulated Fund", type: "Capital/Equity", drCr: "Credit", subcat: "Clubs/Non-Profit", desc: "The 'capital' equivalent representing accumulated surpluses." },
    { name: "Revenue (Sales)", type: "Revenue/Income", drCr: "Credit", subcat: "Operating Revenue", desc: "Earnings generated from core business trade sales." },
    { name: "Sales Returns", type: "Revenue/Income", drCr: "Debit", subcat: "Revenue Contra", desc: "Customer returned goods (Returns Inwards)." },
    { name: "Discount Received", type: "Revenue/Income", drCr: "Credit", subcat: "Other Income", desc: "Savings given by credit suppliers for early cash payment." },
    { name: "Rent Received", type: "Revenue/Income", drCr: "Credit", subcat: "Other Income", desc: "Income earned by sub-letting business premises." },
    { name: "Commission Received", type: "Revenue/Income", drCr: "Credit", subcat: "Other Income", desc: "Earnings generated acting as sales agent." },
    { name: "Subscriptions", type: "Revenue/Income", drCr: "Credit", subcat: "Clubs/Non-Profit", desc: "Annual/monthly member fees for clubs & societies." },
    { name: "Purchases", type: "Expense", drCr: "Debit", subcat: "Trading Expenses", desc: "Cost of raw materials or stock bought for resale." },
    { name: "Purchases Returns", type: "Expense", drCr: "Credit", subcat: "Expense Contra", desc: "Goods returned to our suppliers (Returns Outwards)." },
    { name: "Carriage Inwards", type: "Expense", drCr: "Debit", subcat: "Trading Expenses", desc: "Freight/shipping cost of importing purchases. Added to Cost of Sales." },
    { name: "Carriage Outwards", type: "Expense", drCr: "Debit", subcat: "General Operating Expenses", desc: "Delivery costs to transport goods to customer. Operating expense." },
    { name: "Wages and Salaries", type: "Expense", drCr: "Debit", subcat: "General Operating Expenses", desc: "Employee compensation for hours worked." },
    { name: "Rent and Rates", type: "Expense", drCr: "Debit", subcat: "General Operating Expenses", desc: "Leasing fees for retail and corporate premises." },
    { name: "Insurance", type: "Expense", drCr: "Debit", subcat: "General Operating Expenses", desc: "Risk coverage expense, often prepaid at year-end." },
    { name: "Irrecoverable Debts", type: "Expense", drCr: "Debit", subcat: "General Operating Expenses", desc: "Debts written off as uncollectible (Do not say Bad Debts)." },
    { name: "Depreciation Expense", type: "Expense", drCr: "Debit", subcat: "General Operating Expenses", desc: "The current financial year's calculated asset wear-and-tear." },
    { name: "Direct Factory Wages", type: "Manufacturing Cost", drCr: "Debit", subcat: "Factory Cost", desc: "Wages paid to factory operators making the goods directly." },
    { name: "Factory Overheads", type: "Manufacturing Cost", drCr: "Debit", subcat: "Factory Cost", desc: "Indirect factory expenses like factory heating & supervision." }
  ];

  // 2. 22 CHAPTERS DETAILED CURRICULUM DATA
  const chaptersData = [
    {
      id: 1,
      title: "The Purpose of Accounting",
      syllabusCode: "Section 1.1 - 1.2",
      summary: "Accounting is the art of recording, classifying, summarizing, and interpreting financial transactions. It provides stakeholders with reliable financial information. Bookkeeping is the mechanical process of recording data, while Accounting is the analytical process of interpreting, adjusting, and presenting final statements.",
      keyTerms: [
        { term: "Bookkeeping", definition: "The mechanical recording of financial transactions in books of prime entry." },
        { term: "Accounting", definition: "The process of preparing, interpreting, and analyzing financial statements from recorded bookkeeping entries." },
        { term: "Stakeholder", definition: "Any individual or group (e.g., bank, owner, supplier, employee) with an interest in the business's performance." }
      ],
      ttableGuide: "Transactions flow from Source Documents â Books of Prime Entry â Ledger Accounts â Trial Balance â Financial Statements.",
      stepByStep: [
        "Step 1: Identify the economic event (e.g., owner introduces capital of $10,000).",
        "Step 2: Trace the source document (Receipt / Bank voucher).",
        "Step 3: Journalize or enter in Cash Book (Debit Bank, Credit Capital).",
        "Step 4: Prepare Ledger T-Accounts and check cumulative figures.",
        "Step 5: Run a Trial Balance to verify double-entry math, and prepare Year-End Financial Statements."
      ],
      template: `
+-------------------------------------------------------------+
|               BOOKKEEPING vs ACCOUNTING                     |
+------------------------------+------------------------------+
|         Bookkeeping          |          Accounting          |
+------------------------------+------------------------------+
| - Mechanical recording       | - Analyzing ratios & profit  |
| - Daily journals & ledgers   | - Year-end adjustments       |
| - Foundation of statements   | - Aids managerial decisions  |
+------------------------------+------------------------------+
      `
    },
    {
      id: 2,
      title: "Sources of Information",
      syllabusCode: "Section 1.3",
      summary: "Transactions are initiated by source documents. Every ledger entry must have an audit trail linked to a document: Invoice (credit sales/purchases), Credit Note (returns inwards/outwards), Debit Note (requesting refund/adjustment), Statement of Account (monthly outstanding list), or Petty Cash Voucher.",
      keyTerms: [
        { term: "Invoice", definition: "A document issued by a seller to a buyer detailing goods sold on credit." },
        { term: "Credit Note", definition: "A document issued by a seller to a buyer to reduce the invoice value due to damaged goods or price discrepancies." },
        { term: "Statement of Account", definition: "A summary sent monthly by the supplier showing all transactions and balance outstanding." }
      ],
      ttableGuide: "No double entry is made from a Statement of Account; it is used only to cross-check records.",
      stepByStep: [
        "Step 1: Identify if goods are sold on credit. Prepare Sales Invoice.",
        "Step 2: Enter Sales Invoice total in Sales Journal (Book of Prime Entry).",
        "Step 3: Post the ledger: Debit Trade Receivable Customer Account (Ledger) and Credit Sales Account.",
        "Step 4: If buyer returns damaged goods, send a Credit Note to acknowledge the decrease in customer balance owed."
      ],
      template: `
   +---------------------------------------------------------+
   |                  INVOICE TEMPLATE                       |
   | Supplier: Mahdy Accounting Supplies                      |
   | Sold to: Star Pupil Trading Ltd                         |
   | Date: 12 May 2026                                       |
   +---------------------------------------+-----------------+
   | Description                           | Amount ($)      |
   +---------------------------------------+-----------------+
   | 50 Units of Ledger Textbooks          |          500.00 |
   | Less: Trade Discount (20%)            |         (100.00)|
   | TOTAL NET AMOUNT DUE                  |          400.00 |
   +---------------------------------------+-----------------+
      `
    },
    {
      id: 3,
      title: "Books of Prime Entry",
      syllabusCode: "Section 1.4",
      summary: "Before posting transactions to ledger accounts, we sort them into specific books of prime entry to keep ledgers uncluttered. The major books are: Sales Journal, Purchases Journal, Sales Returns Journal, Purchases Returns Journal, General Journal, Cash Book (two/three-column), and Petty Cash Book.",
      keyTerms: [
        { term: "Cash Book", definition: "A dual-purpose book acting as both a book of prime entry and a ledger account for bank & cash balances." },
        { term: "Petty Cash Book", definition: "A ledger book keeping track of low-value cash expenses, operated on an Imprest system." },
        { term: "Imprest System", definition: "A system where the petty cashier starts with a fixed float and is reimbursed for actual expenditures at the end of each period." }
      ],
      ttableGuide: "Books of prime entry feed directly into ledger accounts. For example, Sales Journal totals are credited to the Sales Account at the end of the month.",
      stepByStep: [
        "Step 1: Record individual credit sales invoices in the Sales Journal.",
        "Step 2: Post the daily totals to the individual customers' accounts (Debit customer account in Sales Ledger).",
        "Step 3: At the end of the month, transfer the total of the Sales Journal to the credit side of the Sales Account in the General Ledger.",
        "Step 4: Audit check: Total of individual customer accounts must match trade receivables summary balances."
      ],
      template: `
+---------------------------------------------------------------------------------+
|                               THREE-COLUMN CASH BOOK                            |
+------------+-------------+---------+--------+---------+------------+----+---+---+
| Date       | Details     | Disc.Al | Cash   | Bank    | Date       |... |...|...|...|
+------------+-------------+---------+--------+---------+------------+----+---+---+
| 2026       |             |   ($)   |  ($)   |   ($)   | 2026       |    |   |   |   |
| June 1     | Bal b/d     |    -    | 150    | 2,400   | June 5     |Wag | - | 80| - |
| June 4     | T.Receivable|   10    |   -    |   190   | June 10    |Bank| - | - |120|
+------------+-------------+---------+--------+---------+------------+----+---+---+
      `
    },
    {
      id: 4,
      title: "Double Entry System",
      syllabusCode: "Section 2.1",
      summary: "The bedrock of modern accounting: every transaction has a dual effect, impacting at least two accounts. This is modeled on the accounting equation: Assets = Capital + Liabilities. The DEAD CLIC mnemonic ensures perfect balance in account entries.",
      keyTerms: [
        { term: "DEAD", definition: "Debit: Expenses, Assets, Drawings. (Increase on Debit, Decrease on Credit)" },
        { term: "CLIC", definition: "Credit: Liabilities, Income, Capital. (Increase on Credit, Decrease on Debit)" },
        { term: "Ledger", definition: "A book or database containing individual T-accounts classified by type." }
      ],
      ttableGuide: "To increase a 'DEAD' account, Debit it. To increase a 'CLIC' account, Credit it.",
      stepByStep: [
        "Step 1: Identify the two accounts affected by the transaction.",
        "Step 2: Classify each account (e.g., Motor Vehicle is an Asset, Cash is an Asset).",
        "Step 3: Determine if they increase or decrease (e.g., buying a car: Motor Vehicle increases, Cash decreases).",
        "Step 4: Apply DEAD CLIC: Debit Motor Vehicle (Asset increase) and Credit Cash (Asset decrease)."
      ],
      template: `
         +---------------------------------------------------------+
         |                      T-ACCOUNT STRUCTURE                |
         | Dr.                       [Account Name]            Cr. |
         +------------+-------------+-----+------------+----+------+
         | Date       | Details     | ($) | Date       |Det.|  ($) |
         +------------+-------------+-----+------------+----+------+
         | 2026       |             |     | 2026       |    |      |
         | Jan 1      | Balance b/d | 500 | Jan 15     |Cash|  100 |
         |            |             |     | Jan 31     |b/c |  400 |
         +------------+-------------+-----+------------+----+------+
         | Feb 1      | Balance b/d | 400 |            |    |      |
         +------------+-------------+-----+------------+----+------+
      `
    },
    {
      id: 5,
      title: "Division of Ledgers & Control Accounts",
      syllabusCode: "Section 2.2 - 2.3",
      summary: "Ledgers are divided into Sales Ledger (individual customer accounts), Purchases Ledger (individual supplier accounts), and General Ledger (all other accounts). Control Accounts (Sales Ledger Control Account / Purchase Ledger Control Account) summarize these individual accounts to detect errors and prevent fraud.",
      keyTerms: [
        { term: "Sales Ledger Control Account", definition: "An account kept in the General Ledger that summarizes the trade receivables balances to double-check accuracy." },
        { term: "Contra Entry", definition: "An offset entry transferring a balance between Sales Ledger and Purchases Ledger when a person is both a customer and supplier." }
      ],
      ttableGuide: "Debits in SLCA match increases in trade receivables (e.g., credit sales). Credits in SLCA match decreases (e.g., cash received, sales returns, irrecoverable debts, and discounts allowed).",
      stepByStep: [
        "Step 1: Obtain totals of sales, sales returns, cash received, etc. from books of prime entry.",
        "Step 2: Debit SLCA with opening balances, Credit Sales, and Accrued Interest.",
        "Step 3: Credit SLCA with Cash/Bank receipts, Sales Returns, Discounts Allowed, and Irrecoverable Debts.",
        "Step 4: Check if the ending SLCA balance matches the sum of the individual Sales Ledger account balances."
      ],
      template: `
+---------------------------------------------------------------------------------+
|                          SALES LEDGER CONTROL ACCOUNT (SLCA)                     |
+---------------------------+-------------+---------------------------+-----------+
| Details                   |  Amount ($) | Details                   | Amount ($)|
+---------------------------+-------------+---------------------------+-----------+
| Balance b/d               |       3,000 | Bank (Receipts from customers)  2,200 |
| Credit Sales (Sales Journal)      4,500 | Discount Allowed          |       100 |
| Interest charged on overdue       50    | Sales Returns             |       200 |
|                           |             | Irrecoverable Debts       |        50 |
|                           |             | Contra (Purchases Ledger) |       150 |
|                           |             | Balance c/d               |     4,850 |
+---------------------------+-------------+---------------------------+-----------+
| Balance b/d               |       4,850 |                           |           |
+---------------------------+-------------+---------------------------+-----------+
      `
    },
    {
      id: 6,
      title: "Bank Reconciliation Statements",
      syllabusCode: "Section 2.4",
      summary: "The balance in our bank ledger account rarely matches the balance shown on our bank statement due to timing differences and errors. A bank reconciliation identifies these items (unpresented checks, uncleared deposits, bank charges) to ensure the cash book and bank match.",
      keyTerms: [
        { term: "Unpresented Check", definition: "A check issued by our business to a supplier that has not yet been processed by the bank." },
        { term: "Uncleared Deposit", definition: "Cash or checks paid into the bank by the business but not yet credited on the bank statement." }
      ],
      ttableGuide: "Always update the Cash Book first with newly discovered items (e.g. bank charges, direct debits) before drafting the Bank Reconciliation Statement.",
      stepByStep: [
        "Step 1: Compare Cash Book Bank Column with Bank Statement line-by-line.",
        "Step 2: Update Cash Book with Bank Statement items not yet recorded (bank charges, direct debits, credit transfers). Calculate the new corrected balance.",
        "Step 3: Prepare the Reconciliation Statement starting with the balance from the Bank Statement.",
        "Step 4: Add uncleared deposits and subtract unpresented checks to reconcile with the updated cash book balance."
      ],
      template: `
+---------------------------------------------------------------------------------+
|                       BANK RECONCILIATION STATEMENT TEMPLATE                    |
+------------------------------------------------------------------+--------------+
| Details                                                          |   Amount ($) |
+------------------------------------------------------------------+--------------+
| Balance as per Bank Statement                                    |     1,500.00 |
| Add: Uncleared Deposits (not yet on statement)                   |       450.00 |
| Less: Unpresented Checks (not yet presented)                     |     (300.00) |
| BALANCE AS PER UPDATED CASH BOOK                                 |     1,650.00 |
+------------------------------------------------------------------+--------------+
      `
    },
    {
      id: 7,
      title: "Trial Balance",
      syllabusCode: "Section 2.5",
      summary: "A statement listing all ledger balances on a specific date to verify that total debits equal total credits. While it tests arithmetic accuracy, it cannot detect non-arithmetic errors like complete omission, principle errors, or compensating errors.",
      keyTerms: [
        { term: "Trial Balance", definition: "A document prepared at year-end to verify that total debit balances equal total credit balances." },
        { term: "Compensating Error", definition: "An error where two separate errors offset each other exactly, leaving total debit/credit columns equal." }
      ],
      ttableGuide: "If the Trial Balance balances, it does not guarantee the accounts are error-free; it only checks mathematical symmetry.",
      stepByStep: [
        "Step 1: Balance all ledger accounts and carry down (c/d) their balances.",
        "Step 2: Extract all debit balances (DEAD accounts) to the debit column of the Trial Balance.",
        "Step 3: Extract all credit balances (CLIC accounts) to the credit column of the Trial Balance.",
        "Step 4: Cast both columns. If they are equal, double-entry mathematical tracking is verified."
      ],
      template: `
+---------------------------------------------------------------------------------+
|                            TRIAL BALANCE TEMPLATE                               |
+-------------------------------------------+-------------------+-----------------+
| Account Name                              |    Debit ($)      |   Credit ($)    |
+-------------------------------------------+-------------------+-----------------+
| Non-Current Assets                        |            20,000 |                 |
| Trade Receivables                         |             5,500 |                 |
| Trade Payables                            |                   |           3,200 |
| Bank (Debit)                              |             1,200 |                 |
| Capital                                   |                   |          23,500 |
| Revenue                                   |                   |          15,000 |
| Purchases                                 |            12,000 |                 |
| Expenses                                  |             3,000 |                 |
+-------------------------------------------+-------------------+-----------------+
| TOTALS                                    |            41,700 |          41,700 |
+-------------------------------------------+-------------------+-----------------+
      `
    },
    {
      id: 8,
      title: "Correction of Errors & Suspense",
      syllabusCode: "Section 2.6",
      summary: "Errors that do not affect trial balance agreement are corrected via the General Journal. Errors that do affect trial balance agreement require a Suspense Account to hold the discrepancy temporarily until the error is identified and resolved.",
      keyTerms: [
        { term: "Suspense Account", definition: "A temporary account used to balance the debit and credit totals of a trial balance until error correction entries are made." },
        { term: "Error of Commission", definition: "Entering the correct amount in the wrong ledger account, but in the correct class of account (e.g., customer account of A. Smith instead of B. Smith)." }
      ],
      ttableGuide: "Double entry is restored by debiting or crediting the Suspense Account alongside the corrected ledger account.",
      stepByStep: [
        "Step 1: Identify if the error causes a trial balance discrepancy (e.g., credit column is $100 lower).",
        "Step 2: Set up a Suspense Account with the initial difference.",
        "Step 3: Identify the single-sided errors (e.g. Sales Journal was undercast by $100).",
        "Step 4: Prepare the correcting General Journal: Debit Suspense $100, Credit Sales $100. The Suspense Account balance should reduce to zero."
      ],
      template: `
+---------------------------------------------------------------------------------+
|                             JOURNAL CORRECTION TEMPLATE                         |
+-------------+-------------------------------------+--------------+--------------+
| Date        | Details                             |   Debit ($)  |  Credit ($)  |
+-------------+-------------------------------------+--------------+--------------+
| 2026        | Suspense                            |       100.00 |              |
| June 30     |   Sales                             |              |       100.00 |
|             | (Correction of sales undercast error)              |              |
+-------------+-------------------------------------+--------------+--------------+
      `
    },
    {
      id: 9,
      title: "Capital & Revenue Expenditures",
      syllabusCode: "Section 3.1",
      summary: "Capital expenditures represent long-term investments in non-current assets. Revenue expenditures are short-term operating costs. Capital receipts are cash from long-term capital transactions, whereas Revenue receipts are trading revenues.",
      keyTerms: [
        { term: "Capital Expenditure", definition: "Spending on purchasing, improving, or extending the useful life of non-current assets." },
        { term: "Revenue Expenditure", definition: "The regular, day-to-day costs of running the business, including repairs and general maintenance." }
      ],
      ttableGuide: "Incorrect classification (e.g., treating a motor van repair as buying a new van) distorts both the profit figure and asset valuations on the Statement of Financial Position.",
      stepByStep: [
        "Step 1: Evaluate if the cost provides a benefit lasting longer than one year (e.g., buying a delivery van is Capital, while buying fuel is Revenue).",
        "Step 2: Classify delivery van shipping cost as part of Capital Expenditure (added to the asset's purchase cost).",
        "Step 3: Charge the van repair costs to the Income Statement as Revenue Expenditure (operating expenses).",
        "Step 4: Audit correct figures to ensure no capital items are mistakenly written off to the income statement."
      ],
      template: `
+---------------------------------------------------------------------------------+
|                       CAPITAL vs REVENUE EXPENDITURE COMPARISON                 |
+---------------------------------------+-----------------------------------------+
| Capital Expenditure                   | Revenue Expenditure                     |
+---------------------------------------+-----------------------------------------+
| - Purchase price of Non-Current Asset | - Day-to-day running costs (Repairs)    |
| - Legal fees for acquiring land       | - Motor van fuel & licensing            |
| - Delivery/installation of asset      | - Insurance of factory premises         |
| -> Recorded in Statement of Fin. Pos. | -> Recorded in Income Statement (Exp)   |
+---------------------------------------+-----------------------------------------+
      `
    },
    {
      id: 10,
      title: "Valuation of Inventory",
      syllabusCode: "Section 3.2",
      summary: "Inventory is valued at the lower of cost and net realizable value (NRV). This conforms to the Prudence Concept, which states that we should never overstate assets or profits.",
      keyTerms: [
        { term: "Cost", definition: "All costs incurred in bringing the inventory to its present location and condition." },
        { term: "Net Realizable Value (NRV)", definition: "The estimated selling price of inventory in the ordinary course of business, less costs to complete and sell." }
      ],
      ttableGuide: "Do not use 'Stock' in IGCSE. Use 'Inventory' in all ledgers and financial statements.",
      stepByStep: [
        "Step 1: Compile list of inventory units at year-end.",
        "Step 2: For each item, compare its purchase cost with its Net Realizable Value.",
        "Step 3: Select the lower value of the two for each product line.",
        "Step 4: Sum the selected lower values to compute the total ending Inventory valuation for the Statement of Financial Position."
      ],
      template: `
+---------------------------------------------------------------------------------+
|                          INVENTORY VALUATION MATRIX                             |
+-----------+-------+------------+-------------+------------------+---------------+
| Item Ref  | Units | Cost/U ($) | NRV/Unit ($)| Chosen Value ($) | Total Val ($) |
+-----------+-------+------------+-------------+------------------+---------------+
| Class-A   |  100  |      12.00 |       15.00 |     Cost (12.00) |      1,200.00 |
| Class-B   |   50  |       8.00 |        5.00 |      NRV (5.00)  |        250.00 |
+-----------+-------+------------+-------------+------------------+---------------+
| TOTAL VALUATION FOR FINANCIAL STATEMENTS                        |      1,450.00 |
+-----------------------------------------------------------------+---------------+
      `
    },
    {
      id: 11,
      title: "Depreciation of Non-Current Assets",
      syllabusCode: "Section 3.3",
      summary: "Depreciation is the systematic allocation of the depreciable amount of a non-current asset over its useful life. It ensures we apply the matching concept by pairing the asset's cost with the revenue it generates. The main methods are straight-line, reducing-balance, and revaluation.",
      keyTerms: [
        { term: "Straight-Line Method", definition: "A method that charges an equal amount of depreciation each year: (Cost - Scrap Value) / Useful Life." },
        { term: "Reducing-Balance Method", definition: "A method that applies a fixed percentage to the net book value (cost less accumulated depreciation) of the asset each year." }
      ],
      ttableGuide: "Debit Depreciation Expense (Income Statement), Credit Accumulated Provision for Depreciation (Statement of Financial Position reduction).",
      stepByStep: [
        "Step 1: Choose the appropriate depreciation method based on the asset's expected pattern of use.",
        "Step 2: Calculate the depreciation charge for the current financial year.",
        "Step 3: Record the adjusting journal entry: Debit Depreciation Expense (Profit and Loss), Credit Accumulated Provision for Depreciation Account.",
        "Step 4: Deduct the updated Accumulated Provision from original Cost to determine Net Book Value."
      ],
      template: `
+---------------------------------------------------------------------------------+
|                       REDUCING BALANCE METHOD EXAMPLE                           |
| Cost: $10,000 | Depreciation Rate: 20%                                          |
+------+---------------------------+----------------------+-----------------------+
| Year | Net Book Value (Start) ($)| Year Charge (20%) ($)| Accum. Depr (End) ($) |
+------+---------------------------+----------------------+-----------------------+
| Year1|                    10,000 |                2,000 |                 2,000 |
| Year2|                     8,000 |                1,600 |                 3,600 |
+------+---------------------------+----------------------+-----------------------+
      `
    },
    {
      id: 12,
      title: "Disposal of Non-Current Assets",
      syllabusCode: "Section 3.4",
      summary: "When a non-current asset is sold or scrapped, we must record the disposal through a temporary Disposal Account. This helps us calculate the net gain or loss on the transaction by comparing the asset's net book value with its disposal proceeds.",
      keyTerms: [
        { term: "Disposal Account", definition: "A ledger account used to calculate the gain or loss when a non-current asset is sold, exchanged, or scrapped." },
        { term: "Gain on Disposal", definition: "The excess of disposal proceeds over the net book value of the asset sold." }
      ],
      ttableGuide: "Debit the Disposal Account with the asset's original cost, and Credit it with its accumulated depreciation and disposal proceeds.",
      stepByStep: [
        "Step 1: Transfer the asset's original cost: Debit Disposal Account, Credit Non-Current Asset Account.",
        "Step 2: Transfer the accumulated depreciation: Debit Accumulated Provision for Depreciation, Credit Disposal Account.",
        "Step 3: Record the sale proceeds: Debit Bank (or Trade Receivable), Credit Disposal Account.",
        "Step 4: Balance the Disposal Account. A debit balance represents a loss on disposal, while a credit balance represents a gain. Transfer this net balance to the Income Statement."
      ],
      template: `
+---------------------------------------------------------------------------------+
|                             ASSET DISPOSAL ACCOUNT                              |
+---------------------------+-------------+---------------------------+-----------+
| Details                   |  Amount ($) | Details                   | Amount ($)|
+---------------------------+-------------+---------------------------+-----------+
| Motor Vehicles Cost       |      15,000 | Accum. Depr on Disposal   |    10,000 |
| Gain on Disposal (P&L)    |       1,000 | Bank (Sale Proceeds)      |     6,000 |
+---------------------------+-------------+---------------------------+-----------+
|                           |      16,000 |                           |    16,000 |
+---------------------------+-------------+---------------------------+-----------+
      `
    },
    {
      id: 13,
      title: "Bad & Doubtful Debts",
      syllabusCode: "Section 3.5",
      summary: "Customer accounts can become uncollectible. Irrecoverable debts must be written off immediately to avoid overstating trade receivables. We also maintain a Provision for Doubtful Debts to anticipate future uncollectible accounts, complying with the prudence concept.",
      keyTerms: [
        { term: "Irrecoverable Debt", definition: "An outstanding balance from a credit customer that is written off as uncollectible." },
        { term: "Provision for Doubtful Debts", definition: "An estimate of potential credit losses on trade receivables, calculated as a percentage of outstanding customer balances." }
      ],
      ttableGuide: "An increase in the Provision is treated as an Expense. A decrease is treated as Income.",
      stepByStep: [
        "Step 1: Write off any known irrecoverable debts: Debit Irrecoverable Debts Account, Credit customer's individual account.",
        "Step 2: Calculate the required Provision based on the remaining trade receivables balance.",
        "Step 3: Compare this with the existing Provision balance from the previous period.",
        "Step 4: Record the adjustment: Debit Provision for Doubtful Debts Expense (if increasing) or Credit (if decreasing)."
      ],
      template: `
+---------------------------------------------------------------------------------+
|                       PROVISION FOR DOUBTFUL DEBTS ADJUSTMENT                   |
| Trade Receivables: $50,000 | Required Prov: 2% ($1,000) | Old Prov: $800        |
+---------------------------+-------------+---------------------------+-----------+
| Details                   |  Amount ($) | Details                   | Amount ($)|
+---------------------------+-------------+---------------------------+-----------+
| Balance c/d               |       1,000 | Balance b/d               |       800 |
|                           |             | Income Statement (increase)       200 |
+---------------------------+-------------+---------------------------+-----------+
|                           |       1,000 |                           |     1,000 |
+---------------------------+-------------+---------------------------+-----------+
      `
    },
    {
      id: 14,
      title: "Sole Trader Income Statements",
      syllabusCode: "Section 4.1",
      summary: "An Income Statement is prepared at the end of each financial year to calculate the business's profit or loss. It is divided into two sections: the Trading Account (which calculates Gross Profit by subtracting Cost of Sales from Revenue) and the Profit and Loss Account (which calculates Profit for the Year by accounting for operating income and expenses).",
      keyTerms: [
        { term: "Gross Profit", definition: "The profit made on trading goods, calculated as Revenue less Cost of Sales." },
        { term: "Cost of Sales", definition: "The direct cost of the goods sold during the year: Opening Inventory + Purchases + Carriage Inwards - Closing Inventory." }
      ],
      ttableGuide: "Carriage Inwards is added to Purchases, while Carriage Outwards is included as an operating expense in the Profit and Loss section.",
      stepByStep: [
        "Step 1: Record Revenue (Sales) and subtract any Sales Returns to determine Net Revenue.",
        "Step 2: Calculate Cost of Sales: Opening Inventory + Purchases + Carriage Inwards - Purchases Returns - Closing Inventory.",
        "Step 3: Subtract Cost of Sales from Net Revenue to calculate Gross Profit.",
        "Step 4: Add other operating incomes (discounts received, rent received) and subtract operating expenses to calculate the final Profit for the Year."
      ],
      template: `
+---------------------------------------------------------------------------------+
|                         SOLE TRADER INCOME STATEMENT TEMPLATE                   |
+--------------------------------------------+-------------------+----------------+
| Details                                    |     Sub ($)       |   Total ($)    |
+--------------------------------------------+-------------------+----------------+
| Revenue (Sales)                            |                   |         50,000 |
| Less: Sales Returns                        |                   |        (1,000) |
| Net Revenue                                |                   |         49,000 |
| Less: Cost of Sales                        |                   |                |
|   Opening Inventory                        |             5,000 |                |
|   Purchases                                |            30,000 |                |
|   Carriage Inwards                         |               500 |                |
|                                            |            35,500 |                |
|   Less: Closing Inventory                  |           (6,000) |       (29,500) |
| GROSS PROFIT                               |                   |         19,500 |
| Add: Discount Received                     |                   |            300 |
| Less: Wages, Rent, Depreciation, Insurances|                   |       (12,000) |
| PROFIT FOR THE YEAR                        |                   |          7,800 |
+--------------------------------------------+-------------------+----------------+
      `
    },
    {
      id: 15,
      title: "Sole Trader Statement of Financial Position",
      syllabusCode: "Section 4.2",
      summary: "A Statement of Financial Position (SFP) displays the assets, liabilities, and capital of a business at a specific point in time. It shows how the business's resources are funded, following the accounting equation: Assets = Capital + Liabilities.",
      keyTerms: [
        { term: "Non-Current Asset", definition: "A long-term resource owned by the business that is expected to be used for more than one year (e.g., machinery)." },
        { term: "Current Asset", definition: "A short-term resource expected to be converted into cash within one year (e.g., inventory, receivables)." }
      ],
      ttableGuide: "Always arrange current assets in order of liquidity, starting with the least liquid (Inventory) and ending with the most liquid (Cash).",
      stepByStep: [
        "Step 1: List Non-Current Assets, showing their original Cost, Accumulated Depreciation, and Net Book Value.",
        "Step 2: Group and list Current Assets in order of liquidity (Inventory, Trade Receivables, Bank, Cash).",
        "Step 3: List Current Liabilities (Trade Payables, Accruals) and Non-Current Liabilities (Long-term bank loans).",
        "Step 4: Present the Capital section, reconciling opening capital with profit and drawings (Capital + Profit - Drawings) to show the closing capital balance."
      ],
      template: `
+---------------------------------------------------------------------------------+
|                  SOLE TRADER STATEMENT OF FINANCIAL POSITION TEMPLATE           |
+------------------------------------+---------------+-------------+--------------+
| Assets                             |    Cost ($)   | Accum.Dep($)|   NBV ($)    |
+------------------------------------+---------------+-------------+--------------+
| Non-Current Assets                 |               |             |              |
|   Premises                         |        80,000 |           - |       80,000 |
|   Motor Vehicles                   |        15,000 |       5,000 |       10,000 |
| Total Non-Current Assets           |        95,000 |       5,000 |       90,000 |
| Current Assets                     |               |             |              |
|   Inventory                        |               |             |        4,000 |
|   Trade Receivables                |               |             |        3,500 |
|   Bank Balance                     |               |             |        1,200 |
| Total Assets                       |               |             |       98,700 |
+------------------------------------+---------------+-------------+--------------+
| Capital and Liabilities            |               |             |              |
|   Opening Capital                  |               |             |       85,000 |
|   Add: Profit for the Year         |               |             |        7,800 |
|   Less: Drawings                   |               |             |      (2,000) |
| Closing Capital                    |               |             |       90,800 |
| Current Liabilities                |               |             |              |
|   Trade Payables                   |               |             |        7,900 |
| Total Capital and Liabilities      |               |             |       98,700 |
+------------------------------------+---------------+-------------+--------------+
      `
    },
    {
      id: 16,
      title: "Adjustments in Financial Statements",
      syllabusCode: "Section 4.3",
      summary: "To ensure our financial statements present a true and fair view of performance, we must make year-end adjustments for accruals and prepayments. This ensures expenses and incomes are matched to the correct accounting period.",
      keyTerms: [
        { term: "Accrued Expense", definition: "An expense that has been incurred during the period but has not yet been paid. It is classified as a Current Liability." },
        { term: "Prepaid Expense", definition: "An expense that has been paid during the period but relates to a future accounting period. It is classified as a Current Asset." }
      ],
      ttableGuide: "Add Accrued Expenses to the year's payments in the Income Statement. Subtract Prepaid Expenses from payments.",
      stepByStep: [
        "Step 1: Check year-end adjustment notes for accrued or prepaid items.",
        "Step 2: Add any accruals to the respective ledger expense to determine the total expense for the year.",
        "Step 3: Subtract any prepayments from the respective ledger expense.",
        "Step 4: Include any outstanding prepayments as Current Assets and accruals as Current Liabilities on the Statement of Financial Position."
      ],
      template: `
+---------------------------------------------------------------------------------+
|                        ACCUALS AND PREPAYMENTS LEDGER TREATMENT                 |
| Paid Insurance: $1,200 | Prepaid at year-end: $200                               |
+---------------------------+-------------+---------------------------+-----------+
| Details                   |  Amount ($) | Details                   | Amount ($)|
+---------------------------+-------------+---------------------------+-----------+
| Bank                      |       1,200 | Income Statement (adj)    |     1,000 |
|                           |             | Balance c/d (Prepaid)     |       200 |
+---------------------------+-------------+---------------------------+-----------+
| Balance b/d               |         200 |                           |           |
+---------------------------+-------------+---------------------------+-----------+
      `
    },
    {
      id: 17,
      title: "Partnership Accounts",
      syllabusCode: "Section 5.1",
      summary: "A partnership is a business owned and operated by two or more people. In addition to an Income Statement, partnerships prepare a Profit and Loss Appropriation Account to distribute profits among partners according to their agreement (salaries, interest on capital, and profit-sharing ratios).",
      keyTerms: [
        { term: "Appropriation Account", definition: "A financial statement prepared by a partnership to show how profits are distributed among partners." },
        { term: "Current Account", definition: "A ledger account used to track a partner's share of profits, drawings, and interest, separate from their fixed capital." }
      ],
      ttableGuide: "Fixed Capital Accounts record the partners' initial capital investments. Current Accounts track their day-to-day transactions and profit shares.",
      stepByStep: [
        "Step 1: Calculate the Profit for the Year in the general Income Statement.",
        "Step 2: Prepare the Appropriation Account: Add Interest on Drawings to the profit.",
        "Step 3: Subtract Interest on Capital and any Partner Salaries to determine the residual profit.",
        "Step 4: Divide the remaining residual profit among the partners based on their agreed profit-sharing ratios."
      ],
      template: `
+---------------------------------------------------------------------------------+
|                       PARTNERSHIP APPROPRIATION ACCOUNT TEMPLATE                |
+--------------------------------------------+-------------------+----------------+
| Details                                    |     Sub ($)       |   Total ($)    |
+--------------------------------------------+-------------------+----------------+
| Profit for the Year                        |                   |         25,000 |
| Add: Interest on Drawings                  |                   |                |
|   Partner A                                |               200 |                |
|   Partner B                                |               300 |            500 |
|                                            |                   |         25,500 |
| Less: Interest on Capital                  |                   |                |
|   Partner A (5% of $10k)                   |             (500) |                |
|   Partner B (5% of $20k)                   |           (1,000) |        (1,500) |
| Less: Partner Salary (Partner A)           |                   |        (4,000) |
| RESIDUAL PROFIT FOR DISTRIBUTION           |                   |         20,000 |
| Share of Profit (60% / 40%):               |                   |                |
|   Partner A                                |            12,000 |                |
|   Partner B                                |             8,000 |         20,000 |
+--------------------------------------------+-------------------+----------------+
      `
    },
    {
      id: 18,
      title: "Limited Company Accounts",
      syllabusCode: "Section 5.2",
      summary: "Limited companies are owned by shareholders, who enjoy limited liability. A company's equity capital is divided into shares. Profits are retained in the business or distributed to shareholders as dividends.",
      keyTerms: [
        { term: "Ordinary Shares", definition: "Equity shares that carry voting rights and receive variable dividends based on company performance." },
        { term: "Retained Earnings", definition: "The portion of net profits kept in the company to fund future growth, rather than distributed as dividends." }
      ],
      ttableGuide: "Dividends paid are shown in the Statement of Changes in Equity, not as an operating expense in the Income Statement.",
      stepByStep: [
        "Step 1: Calculate the company's operating profit and subtract finance costs (like debenture interest).",
        "Step 2: Subtract income tax expense to determine the profit for the year.",
        "Step 3: Prepare the Statement of Changes in Equity (SOCE) to show movements in share capital, reserves, and retained earnings.",
        "Step 4: Present the Equity section on the Statement of Financial Position, listing Share Capital, Share Premium, and Retained Earnings."
      ],
      template: `
+---------------------------------------------------------------------------------+
|                     STATEMENT OF CHANGES IN EQUITY (SOCE) TEMPLATE              |
+-----------------------+---------------+----------------+-----------------+------+
| Details               | Share Cap ($) | Share Prem ($) | Ret.Earnings($) | Total|
+-----------------------+---------------+----------------+-----------------+------+
| Balance b/f           |        50,000 |          5,000 |          12,000 |67,000|
| Profit for the Year   |             - |              - |           8,500 | 8,500|
| Dividends Paid        |             - |              - |         (3,000) |(3,000|
| Balance c/f           |        50,000 |          5,000 |          17,500 |72,500|
+-----------------------+---------------+----------------+-----------------+------+
      `
    },
    {
      id: 19,
      title: "Non-Profit Clubs & Societies",
      syllabusCode: "Section 5.3",
      summary: "Clubs and societies do not trade for profit. Instead of an Income Statement, they prepare a Receipts and Payments Account (a cash summary) and an Income and Expenditure Account (which calculates their surplus or deficit). Capital is replaced by the Accumulated Fund.",
      keyTerms: [
        { term: "Receipts and Payments", definition: "A summary of the club's cash and bank transactions over the financial period." },
        { term: "Income and Expenditure", definition: "The equivalent of an Income Statement for non-profits, prepared on an accruals basis to calculate surplus or deficit." },
        { term: "Accumulated Fund", definition: "The equivalent of capital, representing the accumulated surpluses over the years." }
      ],
      ttableGuide: "Subscriptions received are adjusted for accruals and prepayments to determine the subscription income for the current year.",
      stepByStep: [
        "Step 1: Reconcile cash and bank balances in the Receipts and Payments Account.",
        "Step 2: Prepare a Subscription Account to calculate the subscription income earned during the year.",
        "Step 3: Calculate trading profit or loss from any social activities (like a club bar or cafĂŠ) and transfer the net profit to the Income and Expenditure Account.",
        "Step 4: Prepare the Income and Expenditure Account, subtracting operating expenses from incomes to determine the surplus or deficit for the year."
      ],
      template: `
+---------------------------------------------------------------------------------+
|                       SUBSCRIPTIONS LEDGER ACCOUNT                              |
+---------------------------+-------------+---------------------------+-----------+
| Details                   |  Amount ($) | Details                   | Amount ($)|
+---------------------------+-------------+---------------------------+-----------+
| Subs Owed b/f             |         150 | Subs Prepaid b/f          |       100 |
| Income Statement (earned) |       2,000 | Bank (received during yr) |     1,950 |
| Subs Prepaid c/f          |          80 | Subs Owed c/f             |       180 |
+---------------------------+-------------+---------------------------+-----------+
|                           |       2,230 |                           |     2,230 |
+---------------------------+-------------+---------------------------+-----------+
      `
    },
    {
      id: 20,
      title: "Manufacturing Accounts",
      syllabusCode: "Section 5.4",
      summary: "Manufacturing businesses prepare a Manufacturing Account to calculate the cost of production before preparing their general Income Statement. This helps them monitor and control manufacturing costs (raw materials, direct labor, factory overheads).",
      keyTerms: [
        { term: "Prime Cost", definition: "The sum of direct materials, direct wages, and direct expenses." },
        { term: "Factory Overheads", definition: "Indirect manufacturing costs, such as factory rent, supervisor salaries, and factory equipment depreciation." }
      ],
      ttableGuide: "Always adjust for Work-in-Progress (WIP) inventories to determine the actual cost of goods completed.",
      stepByStep: [
        "Step 1: Calculate the cost of raw materials consumed: Opening Raw Materials + Purchases - Closing Raw Materials.",
        "Step 2: Add direct wages and direct expenses to calculate the Prime Cost.",
        "Step 3: Add factory overheads to the Prime Cost.",
        "Step 4: Adjust for opening and closing Work-in-Progress (WIP) inventories to determine the final Cost of Production."
      ],
      template: `
+---------------------------------------------------------------------------------+
|                          MANUFACTURING ACCOUNT TEMPLATE                         |
+--------------------------------------------+-------------------+----------------+
| Details                                    |     Sub ($)       |   Total ($)    |
+--------------------------------------------+-------------------+----------------+
| Cost of Raw Materials Consumed:            |                   |                |
|   Opening Inventory of Raw Materials       |             8,000 |                |
|   Purchases of Raw Materials               |            40,000 |                |
|                                            |            48,000 |                |
|   Less: Closing Inventory of Raw Materials  |           (9,000) |         39,000 |
| Direct Factory Wages (Direct Labor)        |                   |         15,000 |
| PRIME COST                                 |                   |         54,000 |
| Factory Overheads:                         |                   |                |
|   Factory Rent                             |             4,000 |                |
|   Depreciation of Factory Machinery        |             2,500 |          6,500 |
|                                            |                   |         60,500 |
| Add: Opening Work-in-Progress (WIP)        |                   |          3,000 |
| Less: Closing Work-in-Progress (WIP)       |                   |        (2,500) |
| COST OF PRODUCTION                         |                   |         61,000 |
+--------------------------------------------+-------------------+----------------+
      `
    },
    {
      id: 21,
      title: "Incomplete Records",
      syllabusCode: "Section 5.5",
      summary: "When a business does not keep a complete set of double-entry books, we must use alternative methods to calculate profit and construct financial statements. We use opening and closing capital comparisons to determine profit, and reconstruction techniques to calculate sales and purchases.",
      keyTerms: [
        { term: "Statement of Affairs", definition: "A summary of assets and liabilities prepared to estimate a business's capital when records are incomplete." },
        { term: "Mark-up", definition: "Profit expressed as a percentage of cost: (Gross Profit / Cost of Sales) * 100." },
        { term: "Margin", definition: "Profit expressed as a percentage of selling price: (Gross Profit / Revenue) * 100." }
      ],
      ttableGuide: "Reconstruct the Sales Ledger Control Account to determine total sales, and the Purchases Ledger Control Account to determine total purchases.",
      stepByStep: [
        "Step 1: Prepare Statements of Affairs at the start and end of the year to estimate opening and closing capital.",
        "Step 2: Use the capital comparison formula to calculate profit: Closing Capital + Drawings - Capital Introduced - Opening Capital = Profit.",
        "Step 3: Reconstruct Cash and Bank accounts to identify unrecorded cash transactions.",
        "Step 4: Use mark-up or margin percentages to estimate missing sales, purchases, or inventory figures."
      ],
      template: `
+---------------------------------------------------------------------------------+
|                       INCOMPLETE RECORDS RECONSTRUCTION FORMULAS                |
+---------------------------------------------------------------------------------+
| Profit Formula:                                                                 |
|   Profit = Closing Capital + Drawings - Capital Introduced - Opening Capital     |
|                                                                                 |
| Sales Reconstruction (SLCA):                                                    |
|   Credit Sales = Payments received + Closing Receivables - Opening Receivables  |
|                                                                                 |
| Mark-Up vs Margin Conversions:                                                  |
|   If Mark-Up is 25% (1/4 of Cost), then Margin is 20% (1/5 of Selling Price)    |
+---------------------------------------------------------------------------------+
      `
    },
    {
      id: 22,
      title: "Analysis & Interpretation",
      syllabusCode: "Section 6.1 - 6.2",
      summary: "Financial performance is analyzed using accounting ratios. These ratios help stakeholders evaluate profitability, liquidity, and efficiency, allowing them to make informed decisions and compare performance over time.",
      keyTerms: [
        { term: "Current Ratio", definition: "A liquidity ratio that measures a business's ability to pay short-term debts: Current Assets / Current Liabilities." },
        { term: "Liquid Ratio (Acid Test)", definition: "A more stringent liquidity measure that excludes inventory: (Current Assets - Inventory) / Current Liabilities." },
        { term: "Rate of Inventory Turnover", definition: "Measures how many times a business sells and replaces its inventory over a year: Cost of Sales / Average Inventory." }
      ],
      ttableGuide: "The liquid ratio is a better measure of immediate liquidity than the current ratio because inventory can take time to sell.",
      stepByStep: [
        "Step 1: Calculate Gross Profit Margin: (Gross Profit / Revenue) * 100.",
        "Step 2: Calculate Profit for the Year Margin: (Profit for the Year / Revenue) * 100.",
        "Step 3: Calculate the Current Ratio and Liquid Ratio to evaluate liquidity.",
        "Step 4: Interpret the results, identifying trends, potential cash flow issues, or areas for efficiency improvements."
      ],
      template: `
+---------------------------------------------------------------------------------+
|                            KEY IGCSE RATIOS SUMMARY                             |
+------------------------------+---------------------------+----------------------+
| Ratio Name                   | Formula                   | Ideal Standard       |
+------------------------------+---------------------------+----------------------+
| Gross Profit Margin          | (Gross Profit/Rev) * 100  | High as possible     |
| Profit for the Year Margin   | (Net Profit/Rev) * 100    | High as possible     |
| Current Ratio                | Current Assets/Curr Liab  | 1.5 : 1 to 2 : 1     |
| Liquid Ratio (Acid Test)     | (CA - Inventory)/CL       | 1 : 1                |
| Inventory Turnover Rate      | Cost of Sales/Avg Inv     | High turnover rate   |
+------------------------------+---------------------------+----------------------+
      `
    }
  ];

  // 3. MASTER MCQ PAST-PAPER-STYLE DATABASE (20 Questions for demo, expandable to 200+)
  const mcqQuestions = [
    {
      id: 1,
      chapter: 1,
      question: "Which task is a bookkeeping function rather than an accounting function?",
      options: [
        "Analyzing financial ratios to evaluate business performance",
        "Writing detailed notes explaining the depreciation policy",
        "Recording invoice totals in the Sales Journal Book of Prime Entry",
        "Preparing the year-end statement of financial position"
      ],
      answer: 2,
      explanation: "Bookkeeping is the mechanical recording of daily financial transactions in journals and ledgers, whereas accounting involves analyzing and interpreting financial statements."
    },
    {
      id: 2,
      chapter: 2,
      question: "A credit note is sent to a credit customer for returned goods. In which journal is this transaction recorded?",
      options: [
        "Sales Journal",
        "Sales Returns Journal",
        "Purchases Returns Journal",
        "General Journal"
      ],
      answer: 1,
      explanation: "When a credit customer returns goods, the transaction is recorded in the Sales Returns Journal using the issued credit note as the source document."
    },
    {
      id: 3,
      chapter: 3,
      question: "Which item is entered in the general journal rather than a specialized journal?",
      options: [
        "The purchase of inventory on credit",
        "The correction of an error in the ledger accounts",
        "The payment of wages to staff by bank transfer",
        "The cash received from a trade customer"
      ],
      answer: 1,
      explanation: "The General Journal is used to record non-regular transactions, such as opening entries, the purchase/sale of non-current assets on credit, depreciation adjustments, and the correction of errors."
    },
    {
      id: 4,
      chapter: 4,
      question: "Using the DEAD CLIC rule, how should an increase in Drawings and an increase in Trade Payables be recorded?",
      options: [
        "Drawings: Debit | Trade Payables: Credit",
        "Drawings: Credit | Trade Payables: Debit",
        "Drawings: Debit | Trade Payables: Debit",
        "Drawings: Credit | Trade Payables: Credit"
      ],
      answer: 0,
      explanation: "DEAD (Debit: Expenses, Assets, Drawings) means drawings increase on the debit side. CLIC (Credit: Liabilities, Income, Capital) means payables (a liability) increase on the credit side."
    },
    {
      id: 5,
      chapter: 5,
      question: "A supplier's account in the purchases ledger shows a debit balance of $150. What does this balance indicate?",
      options: [
        "We owe the supplier $150.",
        "The supplier owes us $150, possibly due to an overpayment or return.",
        "The supplier has allowed us a cash discount.",
        "We have written off the debt as irrecoverable."
      ],
      answer: 1,
      explanation: "A supplier's account usually has a credit balance (representing an amount we owe). A debit balance indicates an overpayment or that the supplier owes us money, often due to returned goods."
    },
    {
      id: 6,
      chapter: 6,
      question: "Which item will require an adjustment in the cash book before preparing a bank reconciliation statement?",
      options: [
        "An unpresented check issued to a supplier",
        "An uncleared deposit paid into the bank at year-end",
        "A bank error that overcharged interest on our account",
        "A direct debit paid from our bank account that we had not recorded"
      ],
      answer: 3,
      explanation: "Direct debits shown on the bank statement must be updated in the Cash Book first. Unpresented checks and uncleared deposits are timing differences reconciled in the bank reconciliation statement itself."
    },
    {
      id: 7,
      chapter: 7,
      question: "If a Trial Balance balances, does it prove that the books are completely correct?",
      options: [
        "Yes, it proves every double-entry transaction was recorded flawlessly.",
        "No, because errors like errors of commission or complete omission do not affect trial balance agreement.",
        "Yes, provided the math was verified by an auditor.",
        "No, because it only checks cash and bank account balances."
      ],
      answer: 1,
      explanation: "A trial balance only proves that total debits equal total credits. It does not detect errors of omission, commission, principle, original entry, reversing entry, or compensating errors."
    },
    {
      id: 8,
      chapter: 8,
      question: "To correct an error where a purchase of office equipment was debited to the Purchases Account, which entry is required?",
      options: [
        "Debit Purchases | Credit Office Equipment",
        "Debit Office Equipment | Credit Purchases",
        "Debit Suspense | Credit Purchases",
        "Debit Office Equipment | Credit Suspense"
      ],
      answer: 1,
      explanation: "This is an error of principle. To correct it, we debit Office Equipment (to record the asset purchase) and credit Purchases (to remove the incorrect debit entry from expenses)."
    },
    {
      id: 9,
      chapter: 9,
      question: "The cost of installing a new machine is treated as a revenue expenditure. What is the effect of this error on the financial statements?",
      options: [
        "Assets are overstated and profit is understated.",
        "Assets are understated and profit is understated.",
        "Assets are overstated and profit is overstated.",
        "Assets are understated and profit is overstated."
      ],
      answer: 1,
      explanation: "Installing a machine is a capital expenditure (added to asset cost). Treating it as a revenue expenditure (expense) understates the asset value and overstates expenses, understating the year's profit."
    },
    {
      id: 10,
      chapter: 10,
      question: "A business has inventory that cost $2,000. It can be sold for $2,500 after paying $600 in delivery costs. What is the correct inventory valuation?",
      options: [
        "Cost: $2,000",
        "Net Realizable Value: $1,900",
        "Selling Price: $2,500",
        "Net Realizable Value: $2,500"
      ],
      answer: 1,
      explanation: "Inventory is valued at the lower of cost and NRV. Net Realizable Value = Selling Price ($2,500) - Costs to Complete & Sell ($600) = $1,900. Since $1,900 is lower than cost ($2,000), inventory is valued at $1,900."
    },
    {
      id: 11,
      chapter: 11,
      question: "Which depreciation method is most appropriate for hand tools or loose machinery where individual valuations are more practical than cost-tracking?",
      options: [
        "Straight-Line Method",
        "Reducing-Balance Method",
        "Revaluation Method",
        "Amortization Method"
      ],
      answer: 2,
      explanation: "The revaluation method is ideal for low-value, high-volume assets like loose tools, packing cases, or small office equipment, where individual cost-tracking is impractical."
    },
    {
      id: 12,
      chapter: 12,
      question: "An asset costing $8,000 with accumulated depreciation of $5,000 is sold for $4,500. What is the gain or loss on disposal?",
      options: [
        "Loss on disposal of $500",
        "Gain on disposal of $1,500",
        "Gain on disposal of $500",
        "Loss on disposal of $1,500"
      ],
      answer: 2,
      explanation: "Net Book Value (NBV) = Cost ($8,000) - Accum. Depr ($5,000) = $3,000. Sales proceeds are $4,500. Gain on disposal = Proceeds ($4,500) - NBV ($3,000) = $1,500 gain."
    },
    {
      id: 13,
      chapter: 13,
      question: "A business wants to increase its provision for doubtful debts from $400 to $650. What is the correct ledger entry?",
      options: [
        "Debit Provision for Doubtful Debts $250 | Credit Income Statement $250",
        "Debit Income Statement $250 | Credit Provision for Doubtful Debts $250",
        "Debit Income Statement $650 | Credit Provision for Doubtful Debts $650",
        "Debit Provision for Doubtful Debts $650 | Credit Trade Receivables $650"
      ],
      answer: 1,
      explanation: "To increase the provision, record a debit entry of $250 (the difference) to the Income Statement as an expense, and a credit entry of $250 to the Provision for Doubtful Debts Account."
    },
    {
      id: 14,
      chapter: 14,
      question: "In an income statement, where should Carriage Inwards and Carriage Outwards be classified?",
      options: [
        "Both are added to cost of purchases.",
        "Carriage Inwards is added to Purchases; Carriage Outwards is an operating expense.",
        "Carriage Outwards is added to Purchases; Carriage Inwards is an operating expense.",
        "Both are treated as general operating expenses."
      ],
      answer: 1,
      explanation: "Carriage Inwards is direct cost of bringing purchases in and is added to Purchases in the Cost of Sales section. Carriage Outwards is the cost of delivering goods to customers and is treated as an operating expense."
    },
    {
      id: 15,
      chapter: 15,
      question: "How should current assets be listed in the Statement of Financial Position?",
      options: [
        "Alphabetical order by asset name",
        "In order of liquidity, starting with the least liquid (Inventory)",
        "In order of cost, starting with the highest-value asset",
        "In chronological order based on their purchase date"
      ],
      answer: 1,
      explanation: "Under IGCSE standards, current assets are listed in reverse order of liquidity: Inventory (least liquid) â Trade Receivables â Bank â Cash (most liquid)."
    },
    {
      id: 16,
      chapter: 16,
      question: "At year-end, electricity expense paid was $2,400. There is an outstanding invoice of $300 for December's usage. What is the electricity expense for the year and its SFP classification?",
      options: [
        "Expense: $2,100 | SFP: Prepaid Current Asset $300",
        "Expense: $2,700 | SFP: Accrued Current Liability $300",
        "Expense: $2,400 | SFP: Accrued Current Liability $300",
        "Expense: $2,700 | SFP: Prepaid Current Asset $300"
      ],
      answer: 1,
      explanation: "The total expense is $2,700 ($2,400 paid + $300 accrued). The unpaid $300 is a current liability (accrued expense) on the Statement of Financial Position."
    },
    {
      id: 17,
      chapter: 17,
      question: "In a partnership, why are drawings recorded in the partners' Current Accounts rather than their Capital Accounts?",
      options: [
        "Because drawings are short-term transactions that should not affect their fixed capital investment.",
        "Because drawings represent long-term capital investments.",
        "Because Capital Accounts are only used to record partner salaries.",
        "To ensure drawings are kept secret from other partners."
      ],
      answer: 0,
      explanation: "Partnerships maintain fixed Capital Accounts to record long-term capital investments. Day-to-day transactions, such as drawings, interest, and profit shares, are recorded in the Current Accounts to avoid altering capital balances."
    },
    {
      id: 18,
      chapter: 18,
      question: "Which of the following is classified as equity rather than a liability in a limited company?",
      options: [
        "6% Debentures (redeemable in 2030)",
        "Retained Earnings",
        "Trade Payables",
        "Bank Overdraft"
      ],
      answer: 1,
      explanation: "Retained Earnings are part of the shareholders' equity (reserves). Debentures are long-term liabilities, and trade payables/overdrafts are current liabilities."
    },
    {
      id: 19,
      chapter: 19,
      question: "A youth club received $5,000 in subscriptions during the year. This included $400 for the next financial year. How is this subscription handled?",
      options: [
        "Income: $5,400 | SFP: Current Liability $400",
        "Income: $4,600 | SFP: Current Liability $400",
        "Income: $5,000 | SFP: Current Asset $400",
        "Income: $4,600 | SFP: Current Asset $400"
      ],
      answer: 1,
      explanation: "Subscriptions received in advance ($400) represent unearned income and must be subtracted from the year's income ($5,000 - $400 = $4,600 subscription income). The $400 is classified as a current liability."
    },
    {
      id: 20,
      chapter: 20,
      question: "Which item is included in the calculation of the Prime Cost in a Manufacturing Account?",
      options: [
        "Factory Supervisor Salary",
        "Depreciation of Factory Machinery",
        "Direct Factory Wages",
        "Factory Insurance"
      ],
      answer: 2,
      explanation: "Prime Cost is the sum of direct materials, direct wages, and direct expenses. Factory overheads, such as supervisor salaries, depreciation, and insurance, are added after calculating the Prime Cost."
    }
  ];

  // 4. DEAD CLIC LEDGER MATCH GAME STATE
  const deadclicScenarios = [
    { text: "Purchased delivery van for $12,000 on credit from Active Motors", dr: "Motor Vehicles", cr: "Trade Payables", coin: 15 },
    { text: "Paid factory employee wages of $1,200 by bank cheque", dr: "Wages and Salaries", cr: "Bank Balance (Positive)", coin: 10 },
    { text: "Received commission of $450 in cash", dr: "Cash", cr: "Rent Received", coin: 12 }, // simplified options
    { text: "Owner withdrew $500 cash for personal use", dr: "Drawings Account", cr: "Cash", coin: 15 },
    { text: "Credit customer paid outstanding invoice of $300 via bank transfer", dr: "Bank Balance (Positive)", cr: "Trade Receivables", coin: 10 },
    { text: "Paid insurance premium of $200 by bank cheque", dr: "Insurance", cr: "Bank Balance (Positive)", coin: 12 },
    { text: "Purchased raw materials for $800 on credit from Timber Supplies", dr: "Purchases", cr: "Trade Payables", coin: 15 }
  ];

  const [gameIndex, setGameIndex] = useState(0);
  const [selectedDr, setSelectedDr] = useState("");
  const [selectedCr, setSelectedCr] = useState("");
  const [gameMessage, setGameMessage] = useState("");

  const handleMatchCheck = () => {
    const scenario = deadclicScenarios[gameIndex];
    // Find matching criteria or simplified evaluation
    if (selectedDr === scenario.dr && selectedCr === scenario.cr) {
      setGameMessage(`Correct! You earned ${scenario.coin} Coins! đ`);
      rewardCoins(scenario.coin);
      setStreak(prev => prev + 1);
      changeMascotBubble(mascotLines.correct);
      setTimeout(() => {
        setGameIndex((prev) => (prev + 1) % deadclicScenarios.length);
        setSelectedDr("");
        setSelectedCr("");
        setGameMessage("");
      }, 2500);
    } else {
      setGameMessage("Oops! Incorrect matching. Check DEAD CLIC rules and try again.");
      setStreak(0);
      playSound('wrong', soundMuted);
      changeMascotBubble(mascotLines.wrong);
      if (lives > 1) {
        setLives(prev => prev - 1);
      } else {
        setGameMessage("Out of lives! Visit the shop to purchase more.");
      }
    }
  };

  // 5. LIVE FINANCIAL STATEMENT BUILDER STATE
  const [builderTab, setBuilderTab] = useState('income'); // income, balance
  // Income Statement items state
  const [incRevenue, setIncRevenue] = useState(10000);
  const [incReturns, setIncReturns] = useState(500);
  const [incOpeningInv, setIncOpeningInv] = useState(2000);
  const [incPurchases, setIncPurchases] = useState(6000);
  const [incClosingInv, setIncClosingInv] = useState(2500);
  const [incExpenses, setIncExpenses] = useState(1500);

  // Computed Values
  const computedNetRevenue = incRevenue - incReturns;
  const computedCostOfSales = incOpeningInv + incPurchases - incClosingInv;
  const computedGrossProfit = computedNetRevenue - computedCostOfSales;
  const computedProfitForYear = computedGrossProfit - incExpenses;

  // SFP Builder Items
  const [sfpPremises, setSfpPremises] = useState(50000);
  const [sfpMachinery, setSfpMachinery] = useState(15000);
  const [sfpInventory, setSfpInventory] = useState(incClosingInv);
  const [sfpReceivables, setSfpReceivables] = useState(4000);
  const [sfpBank, setSfpBank] = useState(2500);
  const [sfpPayables, setSfpPayables] = useState(3000);
  const [sfpCapital, setSfpCapital] = useState(60000);
  
  const computedTotalNCA = sfpPremises + sfpMachinery;
  const computedTotalCA = sfpInventory + sfpReceivables + sfpBank;
  const computedTotalAssets = computedTotalNCA + computedTotalCA;
  
  const computedClosingCapital = sfpCapital + computedProfitForYear - 1000; // less drawings mock $1000
  const computedTotalLiabCapital = computedClosingCapital + sfpPayables;

  // 6. SHOP ITEMS
  const shopItems = [
    { id: 'life', name: "Extra Life (Heart)", cost: 50, desc: "Restores 1 life for games & quiz.", icon: Heart },
    { id: 'gold_skin', name: "Gold Scholar Skin", cost: 100, desc: "A prestigious gold theme for your character companion.", icon: Sparkles },
    { id: 'pro_badge', name: "Pro Accountant Badge", cost: 150, desc: "Unlocks the 'Chartered Student' title next to your name.", icon: Award }
  ];

  const buyShopItem = (item) => {
    if (coins < item.cost) {
      changeMascotBubble("Not enough coins! Keep studying and answering correctly to earn more.");
      playSound('wrong', soundMuted);
      return;
    }
    
    deductCoins(item.cost);
    playSound('powerup', soundMuted);

    if (item.id === 'life') {
      setLives(prev => prev + 1);
      changeMascotBubble("Purchased 1 extra life successfully! â¤ď¸");
    } else if (item.id === 'gold_skin') {
      setOwnedSkins(prev => [...prev, 'gold']);
      setEquippedSkin('gold');
      changeMascotBubble("Looking fancy! Gold Scholar Skin equipped.");
    } else if (item.id === 'pro_badge') {
      setOwnedSkins(prev => [...prev, 'pro']);
      setEquippedSkin('pro');
      changeMascotBubble("Chartered status unlocked! Wear it with pride!");
    }
  };

  // 7. QUIZ HUB STATE
  const [quizChapter, setQuizChapter] = useState('all');
  const [currentQuizIndex, setCurrentQuizIndex] = useState(0);
  const [selectedOption, setSelectedOption] = useState(null);
  const [isAnswered, setIsAnswered] = useState(false);
  const [quizScore, setQuizScore] = useState(0);

  const activeQuestions = quizChapter === 'all' 
    ? mcqQuestions 
    : mcqQuestions.filter(q => q.chapter === parseInt(quizChapter));

  const handleAnswerSubmit = (optionIdx) => {
    if (isAnswered) return;
    setSelectedOption(optionIdx);
    setIsAnswered(true);

    const questionObj = activeQuestions[currentQuizIndex];
    if (optionIdx === questionObj.answer) {
      playSound('correct', soundMuted);
      setQuizScore(prev => prev + 1);
      rewardCoins(10);
      setStreak(prev => prev + 1);
      changeMascotBubble("Awesome answer! Correct accounting principle applied! đŞ");
    } else {
      playSound('wrong', soundMuted);
      setStreak(0);
      changeMascotBubble("That's incorrect. Read the explanation below to master this topic.");
      if (lives > 1) {
        setLives(prev => prev - 1);
      } else {
        changeMascotBubble("You've run out of lives! Visit the Shop to purchase more.");
      }
    }
  };

  const handleNextQuiz = () => {
    setSelectedOption(null);
    setIsAnswered(false);
    setCurrentQuizIndex(prev => (prev + 1) % activeQuestions.length);
  };

  return (
    <div className={`min-h-screen font-sans flex flex-col transition-colors duration-300 ${
      equippedSkin === 'gold' 
        ? 'bg-gradient-to-br from-amber-950 via-yellow-900 to-amber-950 text-yellow-50' 
        : equippedSkin === 'pro'
        ? 'bg-gradient-to-br from-indigo-950 via-slate-900 to-indigo-950 text-slate-100'
        : 'bg-gradient-to-br from-slate-950 via-indigo-950 to-slate-900 text-slate-100'
    }`}>
      
      {/* GLAM HEADER */}
      <header className="border-b border-indigo-500/20 bg-slate-900/60 backdrop-blur-md sticky top-0 z-40 px-4 py-3">
        <div className="max-w-7xl mx-auto flex flex-col md:flex-row items-center justify-between gap-4">
          
          {/* Brand Logo & Character Title */}
          <div className="flex items-center gap-3">
            <div className="bg-indigo-600 text-white p-2 rounded-xl shadow-lg shadow-indigo-500/20">
              <Award className="w-8 h-8 text-amber-300 animate-pulse" />
            </div>
            <div>
              <h1 className="text-xl md:text-2xl font-black tracking-tight text-white flex items-center gap-2">
                MR. MAHDY'S <span className="text-indigo-400 font-extrabold text-transparent bg-clip-text bg-gradient-to-r from-indigo-400 via-amber-400 to-emerald-400">IGCSE Accounting Suite</span>
              </h1>
              <p className="text-xs text-slate-400">Syllabus-Compliant Interactive Portal (0452)</p>
            </div>
          </div>

          {/* User Profile & Economy Status */}
          <div className="flex flex-wrap items-center gap-3">
            
            {/* Streak */}
            <div className="bg-slate-800/80 px-3 py-1.5 rounded-full flex items-center gap-1.5 border border-amber-500/20 text-amber-400 text-xs font-bold">
              <Flame className="w-4 h-4 fill-amber-500" />
              <span>{streak} Day Streak</span>
            </div>

            {/* Coins */}
            <div className="bg-amber-500/10 px-3 py-1.5 rounded-full flex items-center gap-1.5 border border-amber-400/30 text-amber-400 text-xs font-bold">
              <Coins className="w-4 h-4 animate-spin" />
              <span>{coins} Coins</span>
            </div>

            {/* Lives */}
            <div className="bg-rose-500/10 px-3 py-1.5 rounded-full flex items-center gap-1.5 border border-rose-400/30 text-rose-400 text-xs font-bold">
              <Heart className="w-4 h-4 fill-rose-500 text-rose-500" />
              <span>{lives} Lives</span>
            </div>

            {/* Sound Toggle */}
            <button 
              onClick={() => setSoundMuted(!soundMuted)}
              className="p-1.5 rounded-full bg-slate-800 hover:bg-slate-700 text-slate-400 hover:text-white"
              title={soundMuted ? "Unmute Sound" : "Mute Sound"}
            >
              {soundMuted ? <VolumeX className="w-4 h-4" /> : <Volume2 className="w-4 h-4" />}
            </button>

            {/* User Custom Name */}
            <div className="bg-slate-800/80 px-3 py-1.5 rounded-lg border border-indigo-500/20 flex items-center gap-2">
              <User className="w-4 h-4 text-indigo-400" />
              {isEditingName ? (
                <input 
                  type="text" 
                  value={userName} 
                  onChange={(e) => setUserName(e.target.value)}
                  onBlur={() => setIsEditingName(false)}
                  onKeyDown={(e) => { if (e.key === 'Enter') setIsEditingName(false); }}
                  className="bg-slate-900 border border-indigo-400 px-1 rounded text-xs focus:outline-none text-white w-24"
                  autoFocus
                />
              ) : (
                <span 
                  onClick={() => setIsEditingName(true)} 
                  className="text-xs font-semibold cursor-pointer hover:underline text-indigo-200"
                >
                  {userName} âď¸
                </span>
              )}
            </div>

          </div>
        </div>
      </header>

      {/* COMPANION MASCOT BANNER */}
      <div className="bg-indigo-900/40 border-b border-indigo-500/20 py-3 px-4">
        <div className="max-w-7xl mx-auto flex items-center gap-4">
          <div className="relative">
            <div className={`w-12 h-12 rounded-full flex items-center justify-center font-black text-2xl shadow-md border-2 ${
              equippedSkin === 'gold' 
                ? 'bg-amber-400 border-yellow-200 text-amber-950' 
                : equippedSkin === 'pro'
                ? 'bg-indigo-500 border-indigo-300 text-slate-950'
                : 'bg-emerald-500 border-emerald-300 text-slate-950'
            }`}>
              đŚ
            </div>
            <span className="absolute -bottom-1 -right-1 bg-indigo-600 text-[9px] px-1 rounded-full text-white font-bold">Ledger</span>
          </div>
          <div className="flex-1">
            <span className="text-[10px] uppercase tracking-wider font-extrabold text-amber-400">MR. MAHDY's Companion Says</span>
            <p className="text-sm italic text-slate-200">"{mascotBubble}"</p>
          </div>
        </div>
      </div>

      {/* MAIN LAYOUT */}
      <main className="flex-grow max-w-7xl mx-auto w-full p-4 grid grid-cols-1 lg:grid-cols-4 gap-6">
        
        {/* SIDE BAR NAVIGATION */}
        <aside className="lg:col-span-1 flex flex-col gap-3">
          <div className="bg-slate-900/60 p-4 rounded-2xl border border-slate-800 backdrop-blur-md">
            <h3 className="text-xs font-bold text-slate-400 uppercase tracking-widest mb-3">Study Navigation</h3>
            <div className="flex flex-col gap-1.5">
              
              <button 
                onClick={() => { setActiveTab('hub'); changeMascotBubble(mascotLines.welcome); }}
                className={`w-full flex items-center gap-3 px-4 py-3 rounded-xl transition text-left text-sm font-semibold ${
                  activeTab === 'hub' ? 'bg-indigo-600 text-white' : 'hover:bg-slate-800 text-slate-300'
                }`}
              >
                <Award className="w-5 h-5" />
                <span>Dashboard Hub</span>
              </button>

              <button 
                onClick={() => { setActiveTab('notes'); changeMascotBubble("Study each of our 22 syllabus chapters thoroughly with standard templates."); }}
                className={`w-full flex items-center gap-3 px-4 py-3 rounded-xl transition text-left text-sm font-semibold ${
                  activeTab === 'notes' ? 'bg-indigo-600 text-white' : 'hover:bg-slate-800 text-slate-300'
                }`}
              >
                <BookOpen className="w-5 h-5" />
                <span>22 Chapters Revision</span>
              </button>

              <button 
                onClick={() => { setActiveTab('deadclic'); changeMascotBubble(mascotLines.deadclic); }}
                className={`w-full flex items-center gap-3 px-4 py-3 rounded-xl transition text-left text-sm font-semibold ${
                  activeTab === 'deadclic' ? 'bg-indigo-600 text-white' : 'hover:bg-slate-800 text-slate-300'
                }`}
              >
                <Gamepad2 className="w-5 h-5 animate-bounce" />
                <span>DEAD CLIC Matcher Game</span>
              </button>

              <button 
                onClick={() => { setActiveTab('statement_builder'); changeMascotBubble(mascotLines.builder); }}
                className={`w-full flex items-center gap-3 px-4 py-3 rounded-xl transition text-left text-sm font-semibold ${
                  activeTab === 'statement_builder' ? 'bg-indigo-600 text-white' : 'hover:bg-slate-800 text-slate-300'
                }`}
              >
                <FileSpreadsheet className="w-5 h-5" />
                <span>Financial Statement Builder</span>
              </button>

              <button 
                onClick={() => { setActiveTab('quiz'); changeMascotBubble("Test your progress with official past-paper standard MCQs."); }}
                className={`w-full flex items-center gap-3 px-4 py-3 rounded-xl transition text-left text-sm font-semibold ${
                  activeTab === 'quiz' ? 'bg-indigo-600 text-white' : 'hover:bg-slate-800 text-slate-300'
                }`}
              >
                <HelpCircle className="w-5 h-5" />
                <span>Syllabus Quiz Master</span>
              </button>

              <button 
                onClick={() => { setActiveTab('master_accounts'); changeMascotBubble("Browse the master list of accounts required by IGCSE."); }}
                className={`w-full flex items-center gap-3 px-4 py-3 rounded-xl transition text-left text-sm font-semibold ${
                  activeTab === 'master_accounts' ? 'bg-indigo-600 text-white' : 'hover:bg-slate-800 text-slate-300'
                }`}
              >
                <BookMarked className="w-5 h-5" />
                <span>Master Accounts List</span>
              </button>

            </div>
          </div>

          {/* MINI AD/TUTORIAL INSIGHT CARD */}
          <div className="bg-gradient-to-br from-indigo-900/60 to-purple-950/40 p-4 rounded-2xl border border-indigo-500/20 text-xs">
            <h4 className="font-extrabold text-amber-400 mb-2 flex items-center gap-1">
              <TrendingUp className="w-4 h-4" /> Exam Tip by MR. MAHDY
            </h4>
            <p className="text-slate-300 leading-relaxed">
              "Never confuse Carriage Inwards and Carriage Outwards. One goes to Cost of Sales, the other directly to Operating Expenses! Simple errors can cost you the top grade."
            </p>
          </div>
        </aside>

        {/* WORKSPACE CENTRAL COLUMN */}
        <section className="lg:col-span-3 flex flex-col gap-6">

          {/* 1. DASHBOARD HUB TAB */}
          {activeTab === 'hub' && (
            <div className="flex flex-col gap-6">
              
              {/* Welcome banner */}
              <div className="bg-gradient-to-r from-indigo-900 to-purple-950 p-6 rounded-3xl border border-indigo-400/20 relative overflow-hidden">
                <div className="relative z-10 max-w-lg">
                  <span className="bg-indigo-500/20 border border-indigo-400 text-indigo-300 text-xs px-2.5 py-1 rounded-full font-bold">
                    IGCSE ACCOUNTING EXAM ACCELERATOR
                  </span>
                  <h2 className="text-2xl md:text-3xl font-black mt-3 mb-2">
                    Level Up Your Ledger Mastery
                  </h2>
                  <p className="text-slate-300 text-sm leading-relaxed mb-4">
                    Welcome to the most complete interactive portal built to score an A*! Complete chapters, earn coins, protect your lives in tests, and unlock professional ranks.
                  </p>
                  <button 
                    onClick={() => setActiveTab('notes')}
                    className="bg-indigo-600 hover:bg-indigo-500 text-white text-xs font-bold px-4 py-2.5 rounded-lg inline-flex items-center gap-2 shadow-lg"
                  >
                    <span>Begin Revision Journey</span> <ArrowRight className="w-4 h-4" />
                  </button>
                </div>
                <div className="absolute right-4 bottom-4 text-8xl opacity-10">đ</div>
              </div>

              {/* Coins and Cosmetics Shop */}
              <div>
                <h3 className="text-lg font-extrabold text-white mb-4 flex items-center gap-2">
                  <ShoppingBag className="w-5 h-5 text-indigo-400" /> Coin Exchange & Character Customization
                </h3>
                <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
                  {shopItems.map((item) => {
                    const Icon = item.icon;
                    return (
                      <div key={item.id} className="bg-slate-950 border border-slate-800 p-4 rounded-2xl flex flex-col justify-between">
                        <div>
                          <div className="p-2.5 bg-slate-900 rounded-xl w-fit mb-3">
                            <Icon className="w-6 h-6 text-yellow-400" />
                          </div>
                          <h4 className="font-bold text-sm text-slate-200">{item.name}</h4>
                          <p className="text-xs text-slate-400 mt-1 leading-relaxed">{item.desc}</p>
                        </div>
                        <button 
                          onClick={() => buyShopItem(item)}
                          className="mt-4 w-full bg-yellow-500 hover:bg-yellow-400 text-yellow-950 text-xs font-black py-2 rounded-lg flex items-center justify-center gap-1.5 transition"
                        >
                          <Coins className="w-4 h-4" /> Buy for {item.cost} Coins
                        </button>
                      </div>
                    );
                  })}
                </div>
              </div>

              {/* Progress Achievements */}
              <div className="bg-slate-900/60 border border-slate-800 p-5 rounded-2xl">
                <h3 className="text-sm font-extrabold uppercase tracking-wider text-slate-400 mb-4">Your Achievements Portfolio</h3>
                <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                  <div className="bg-slate-950 p-4 rounded-xl flex items-center gap-3">
                    <span className="text-3xl">đŻ</span>
                    <div>
                      <h4 className="font-bold text-xs text-slate-200">First Ledger Correct</h4>
                      <p className="text-[11px] text-slate-400">Match double-entry scenario correctly in games.</p>
                      <span className="text-[10px] text-emerald-400 font-bold">Completed</span>
                    </div>
                  </div>
                  <div className="bg-slate-950 p-4 rounded-xl flex items-center gap-3 opacity-60">
                    <span className="text-3xl">đ</span>
                    <div>
                      <h4 className="font-bold text-xs text-slate-200">22 Chapter Conqueror</h4>
                      <p className="text-[11px] text-slate-400">Review detailed definitions of all 22 chapters.</p>
                      <span className="text-[10px] text-slate-400 font-bold">In Progress</span>
                    </div>
                  </div>
                </div>
              </div>

            </div>
          )}

          {/* 2. REVISION NOTES - CHAPTERS 1-22 */}
          {activeTab === 'notes' && (
            <div className="flex flex-col gap-6">
              
              {/* Chapter Selection Header */}
              <div className="bg-slate-900/60 border border-slate-800 p-4 rounded-2xl">
                <label className="block text-xs font-extrabold uppercase text-slate-400 mb-2">Select Syllabus Chapter</label>
                <select 
                  value={selectedChapter} 
                  onChange={(e) => setSelectedChapter(parseInt(e.target.value))}
                  className="w-full bg-slate-950 border border-indigo-500/30 text-white rounded-xl py-3 px-4 text-sm font-bold focus:outline-none focus:ring-2 focus:ring-indigo-500 transition"
                >
                  {chaptersData.map((chap) => (
                    <option key={chap.id} value={chap.id}>
                      Chapter {chap.id}: {chap.title}
                    </option>
                  ))}
                </select>
              </div>

              {/* Detailed Materials Section */}
              {(() => {
                const chap = chaptersData.find(c => c.id === selectedChapter) || chaptersData[0];
                return (
                  <div className="bg-slate-900/60 border border-slate-800 rounded-3xl p-6 flex flex-col gap-6">
                    
                    {/* Chapter Title Badge */}
                    <div className="flex flex-wrap items-center justify-between gap-4 border-b border-slate-800 pb-4">
                      <div>
                        <span className="text-xs font-bold text-indigo-400 uppercase tracking-widest">{chap.syllabusCode}</span>
                        <h2 className="text-xl md:text-2xl font-black text-white mt-1">
                          Chapter {chap.id}: {chap.title}
                        </h2>
                      </div>
                      <button 
                        onClick={() => rewardCoins(10)} 
                        className="bg-amber-500/10 hover:bg-amber-500/20 border border-amber-400/30 px-3 py-1.5 rounded-lg text-amber-400 text-xs font-bold flex items-center gap-1.5 transition"
                      >
                        <Coins className="w-4 h-4" />
                        <span>Claim Read Coins (+10)</span>
                      </button>
                    </div>

                    {/* Sub-tab Navigation for Notes */}
                    <div className="flex flex-wrap gap-2 border-b border-slate-800 pb-2">
                      {['summary', 'ttable', 'step_by_step', 'format_template'].map((sub) => (
                        <button
                          key={sub}
                          onClick={() => setSelectedSubTopic(sub)}
                          className={`px-3 py-1.5 rounded-lg text-xs font-bold transition ${
                            selectedSubTopic === sub 
                              ? 'bg-indigo-600 text-white' 
                              : 'bg-slate-950 text-slate-400 hover:text-slate-200'
                          }`}
                        >
                          {sub === 'summary' && 'Syllabus Summary'}
                          {sub === 'ttable' && 'Ledger Guide'}
                          {sub === 'step_by_step' && 'How to Solve Step-by-Step'}
                          {sub === 'format_template' && 'Standard Accounting Format'}
                        </button>
                      ))}
                    </div>

                    {/* Sub-tab Content Render */}
                    <div className="min-h-[250px] leading-relaxed">
                      
                      {selectedSubTopic === 'summary' && (
                        <div className="flex flex-col gap-4">
                          <p className="text-sm text-slate-300 leading-relaxed bg-slate-950 p-4 rounded-xl border border-slate-800">
                            {chap.summary}
                          </p>
                          <h4 className="font-extrabold text-xs text-indigo-400 uppercase tracking-wider mt-2">Key Exam Definitions Required:</h4>
                          <div className="grid grid-cols-1 md:grid-cols-2 gap-3">
                            {chap.keyTerms.map((item, idx) => (
                              <div key={idx} className="bg-slate-950/80 p-3.5 rounded-xl border border-slate-800">
                                <span className="font-bold text-xs text-indigo-300 block mb-1">{item.term}</span>
                                <p className="text-xs text-slate-400">{item.definition}</p>
                              </div>
                            ))}
                          </div>
                        </div>
                      )}

                      {selectedSubTopic === 'ttable' && (
                        <div className="flex flex-col gap-4">
                          <h4 className="font-extrabold text-sm text-white mb-1">Double-Entry Routing Rules (DEAD CLIC):</h4>
                          <div className="bg-indigo-900/10 border border-indigo-500/20 p-4 rounded-xl">
                            <p className="text-xs text-slate-300 leading-relaxed">
                              {chap.ttableGuide}
                            </p>
                          </div>
                          
                          {/* Classic Ledger Account T-Table Showcase */}
                          <div className="bg-slate-950 p-4 rounded-xl border border-slate-800">
                            <span className="text-[10px] text-slate-400 uppercase tracking-widest block mb-2">Ledger Visual Guide</span>
                            <pre className="font-mono text-[11px] text-indigo-300 overflow-x-auto whitespace-pre leading-relaxed">
{`          Debit side (Left)                 Credit side (Right)
   +---------------------------------+---------------------------------+
   |  Increases: Expense, Asset,     |  Increases: Liability, Income,  |
   |             Drawings            |             Capital           |
   |                                 |                                 |
   |  Decreases: Liability, Income,  |  Decreases: Expense, Asset,     |
   |             Capital             |             Drawings            |
   +---------------------------------+---------------------------------+`}
                            </pre>
                          </div>
                        </div>
                      )}

                      {selectedSubTopic === 'step_by_step' && (
                        <div className="flex flex-col gap-3">
                          <h4 className="font-extrabold text-sm text-white mb-2">Step-by-Step Problem-Solving Methodology</h4>
                          {chap.stepByStep.map((step, i) => (
                            <div key={i} className="flex gap-3 items-start bg-slate-950 p-3 rounded-xl border border-slate-800">
                              <span className="bg-indigo-600 text-white w-5 h-5 rounded-full flex items-center justify-center text-xs font-bold shrink-0">{i+1}</span>
                              <p className="text-xs text-slate-300 leading-relaxed">{step}</p>
                            </div>
                          ))}
                        </div>
                      )}

                      {selectedSubTopic === 'format_template' && (
                        <div className="flex flex-col gap-3">
                          <h4 className="font-extrabold text-sm text-white mb-2">Standard Accounting Layout Format:</h4>
                          <div className="bg-slate-950 p-4 rounded-xl border border-slate-800">
                            <pre className="font-mono text-[11px] text-emerald-400 overflow-x-auto whitespace-pre leading-relaxed">
                              {chap.template}
                            </pre>
                          </div>
                        </div>
                      )}

                    </div>

                  </div>
                );
              })()}

            </div>
          )}

          {/* 3. DEAD CLIC JOURNAL MATCH GAME */}
          {activeTab === 'deadclic' && (
            <div className="bg-slate-900/60 border border-slate-800 p-6 rounded-3xl flex flex-col gap-6">
              
              <div>
                <span className="bg-indigo-500/20 border border-indigo-400 text-indigo-300 text-xs px-2.5 py-1 rounded-full font-bold">
                  DEAD CLIC ARCADE MATCH
                </span>
                <h2 className="text-xl md:text-2xl font-black text-white mt-3 mb-1">
                  Journal-to-Ledger Double Entry Game
                </h2>
                <p className="text-xs text-slate-400">
                  Analyze the transaction below. Determine which account receives the Debit entry and which receives the Credit entry based on DEAD CLIC.
                </p>
              </div>

              {/* Scenario card */}
              <div className="bg-slate-950 p-6 rounded-2xl border border-indigo-500/20 relative">
                <span className="text-[10px] uppercase font-black text-indigo-400 tracking-wider">Transaction Scenario</span>
                <p className="text-md md:text-lg font-bold text-white mt-2 leading-relaxed">
                  "{deadclicScenarios[gameIndex].text}"
                </p>
                <div className="absolute right-4 top-4 text-xs bg-amber-500/10 px-2 py-1 rounded border border-amber-400/20 text-amber-400 font-extrabold">
                  + {deadclicScenarios[gameIndex].coin} Coins Reward
                </div>
              </div>

              {/* Match Select Inputs */}
              <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                
                {/* Debit Select */}
                <div className="bg-slate-950 p-4 rounded-xl border border-slate-800">
                  <label className="block text-xs font-black text-red-400 uppercase tracking-wider mb-2">Debit Account Name (increases DEAD)</label>
                  <select 
                    value={selectedDr}
                    onChange={(e) => setSelectedDr(e.target.value)}
                    className="w-full bg-slate-900 border border-red-500/20 text-slate-200 text-xs py-2 px-3 rounded-lg focus:outline-none"
                  >
                    <option value="">-- Choose Account --</option>
                    {masterAccounts.map((acc, idx) => (
                      <option key={idx} value={acc.name}>{acc.name} ({acc.type})</option>
                    ))}
                  </select>
                </div>

                {/* Credit Select */}
                <div className="bg-slate-950 p-4 rounded-xl border border-slate-800">
                  <label className="block text-xs font-black text-emerald-400 uppercase tracking-wider mb-2">Credit Account Name (increases CLIC)</label>
                  <select 
                    value={selectedCr}
                    onChange={(e) => setSelectedCr(e.target.value)}
                    className="w-full bg-slate-900 border border-emerald-500/20 text-slate-200 text-xs py-2 px-3 rounded-lg focus:outline-none"
                  >
                    <option value="">-- Choose Account --</option>
                    {masterAccounts.map((acc, idx) => (
                      <option key={idx} value={acc.name}>{acc.name} ({acc.type})</option>
                    ))}
                  </select>
                </div>

              </div>

              {/* Submit and Game Stats */}
              <div className="flex flex-col md:flex-row items-center justify-between gap-4 border-t border-slate-800 pt-4">
                <div className="text-xs text-slate-400">
                  {gameMessage && <span className="font-bold text-indigo-300 block mb-1">{gameMessage}</span>}
                  <span>Keep practicing to master Ledger postings!</span>
                </div>
                <button 
                  onClick={handleMatchCheck}
                  disabled={!selectedDr || !selectedCr}
                  className="bg-indigo-600 hover:bg-indigo-500 disabled:opacity-50 text-white font-bold text-xs py-3 px-6 rounded-xl transition flex items-center gap-2 shadow-lg"
                >
                  <span>Post to Ledger Account</span> <ArrowRight className="w-4 h-4" />
                </button>
              </div>

            </div>
          )}

          {/* 4. LIVE FINANCIAL STATEMENT BUILDER */}
          {activeTab === 'statement_builder' && (
            <div className="bg-slate-900/60 border border-slate-800 p-6 rounded-3xl flex flex-col gap-6">
              
              <div>
                <span className="bg-indigo-500/20 border border-indigo-400 text-indigo-300 text-xs px-2.5 py-1 rounded-full font-bold">
                  INTERACTIVE PRACTICE TEMPLATES
                </span>
                <h2 className="text-xl md:text-2xl font-black text-white mt-3 mb-1">
                  Live IGCSE Statement Formulator
                </h2>
                <p className="text-xs text-slate-400">
                  Fill out raw trial balance values into standard statements below. Watch how net profits and final balance sheets adjust dynamically.
                </p>
              </div>

              {/* Tab Selector inside Builder */}
              <div className="flex gap-2 border-b border-slate-800 pb-2">
                <button 
                  onClick={() => setBuilderTab('income')}
                  className={`px-4 py-2 rounded-lg text-xs font-bold transition ${
                    builderTab === 'income' ? 'bg-indigo-600 text-white' : 'bg-slate-950 text-slate-400'
                  }`}
                >
                  1. Income Statement Builder
                </button>
                <button 
                  onClick={() => setBuilderTab('balance')}
                  className={`px-4 py-2 rounded-lg text-xs font-bold transition ${
                    builderTab === 'balance' ? 'bg-indigo-600 text-white' : 'bg-slate-950 text-slate-400'
                  }`}
                >
                  2. Statement of Financial Position (SFP)
                </button>
              </div>

              {builderTab === 'income' ? (
                <div className="flex flex-col gap-6">
                  
                  {/* Inputs Column */}
                  <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
                    <div className="bg-slate-950 p-3 rounded-xl border border-slate-800">
                      <label className="block text-[10px] font-bold text-slate-400 uppercase mb-1">Revenue ($)</label>
                      <input 
                        type="number" 
                        value={incRevenue} 
                        onChange={(e) => setIncRevenue(parseInt(e.target.value) || 0)}
                        className="bg-slate-900 border border-slate-800 p-2 rounded w-full text-xs text-white"
                      />
                    </div>
                    <div className="bg-slate-950 p-3 rounded-xl border border-slate-800">
                      <label className="block text-[10px] font-bold text-slate-400 uppercase mb-1">Sales Returns ($)</label>
                      <input 
                        type="number" 
                        value={incReturns} 
                        onChange={(e) => setIncReturns(parseInt(e.target.value) || 0)}
                        className="bg-slate-900 border border-slate-800 p-2 rounded w-full text-xs text-white"
                      />
                    </div>
                    <div className="bg-slate-950 p-3 rounded-xl border border-slate-800">
                      <label className="block text-[10px] font-bold text-slate-400 uppercase mb-1">Opening Inventory ($)</label>
                      <input 
                        type="number" 
                        value={incOpeningInv} 
                        onChange={(e) => setIncOpeningInv(parseInt(e.target.value) || 0)}
                        className="bg-slate-900 border border-slate-800 p-2 rounded w-full text-xs text-white"
                      />
                    </div>
                    <div className="bg-slate-950 p-3 rounded-xl border border-slate-800">
                      <label className="block text-[10px] font-bold text-slate-400 uppercase mb-1">Purchases ($)</label>
                      <input 
                        type="number" 
                        value={incPurchases} 
                        onChange={(e) => setIncPurchases(parseInt(e.target.value) || 0)}
                        className="bg-slate-900 border border-slate-800 p-2 rounded w-full text-xs text-white"
                      />
                    </div>
                    <div className="bg-slate-950 p-3 rounded-xl border border-slate-800">
                      <label className="block text-[10px] font-bold text-slate-400 uppercase mb-1">Closing Inventory ($)</label>
                      <input 
                        type="number" 
                        value={incClosingInv} 
                        onChange={(e) => setIncClosingInv(parseInt(e.target.value) || 0)}
                        className="bg-slate-900 border border-slate-800 p-2 rounded w-full text-xs text-white"
                      />
                    </div>
                    <div className="bg-slate-950 p-3 rounded-xl border border-slate-800">
                      <label className="block text-[10px] font-bold text-slate-400 uppercase mb-1">Total Operating Expenses ($)</label>
                      <input 
                        type="number" 
                        value={incExpenses} 
                        onChange={(e) => setIncExpenses(parseInt(e.target.value) || 0)}
                        className="bg-slate-900 border border-slate-800 p-2 rounded w-full text-xs text-white"
                      />
                    </div>
                  </div>

                  {/* Rendered live sheet */}
                  <div className="bg-slate-950 p-5 rounded-2xl border border-indigo-500/10">
                    <span className="text-[10px] text-indigo-400 font-bold uppercase tracking-wider">Computed Standard Statement Layout</span>
                    <div className="mt-4 flex flex-col gap-3 font-mono text-xs">
                      <div className="flex justify-between border-b border-slate-800 pb-2 text-slate-400">
                        <span>Line Item Particulars</span>
                        <span>Debit ($)</span>
                        <span>Credit ($)</span>
                      </div>
                      <div className="flex justify-between">
                        <span>Revenue</span>
                        <span></span>
                        <span>{incRevenue.toLocaleString()}</span>
                      </div>
                      <div className="flex justify-between text-rose-400">
                        <span>Less: Sales Returns</span>
                        <span></span>
                        <span>({incReturns.toLocaleString()})</span>
                      </div>
                      <div className="flex justify-between border-b border-slate-800 pb-2 text-emerald-400 font-bold">
                        <span>Net Revenue</span>
                        <span></span>
                        <span>{computedNetRevenue.toLocaleString()}</span>
                      </div>
                      <div className="text-indigo-300">Less: Cost of Sales</div>
                      <div className="flex justify-between pl-4">
                        <span>Opening Inventory</span>
                        <span>{incOpeningInv.toLocaleString()}</span>
                        <span></span>
                      </div>
                      <div className="flex justify-between pl-4">
                        <span>Add: Purchases</span>
                        <span>{incPurchases.toLocaleString()}</span>
                        <span></span>
                      </div>
                      <div className="flex justify-between pl-4 text-rose-400">
                        <span>Less: Closing Inventory</span>
                        <span>({incClosingInv.toLocaleString()})</span>
                        <span></span>
                      </div>
                      <div className="flex justify-between border-b border-slate-800 pb-2 pl-4">
                        <span>Cost of Sales Subtotal</span>
                        <span></span>
                        <span>({computedCostOfSales.toLocaleString()})</span>
                      </div>
                      <div className="flex justify-between border-b border-slate-800 pb-2 text-emerald-400 font-bold">
                        <span>GROSS PROFIT</span>
                        <span></span>
                        <span>{computedGrossProfit.toLocaleString()}</span>
                      </div>
                      <div className="flex justify-between text-rose-400">
                        <span>Less: Operating Expenses</span>
                        <span></span>
                        <span>({incExpenses.toLocaleString()})</span>
                      </div>
                      <div className="flex justify-between border-t-2 border-indigo-500 pt-2 text-amber-400 font-extrabold text-sm">
                        <span>PROFIT FOR THE YEAR</span>
                        <span></span>
                        <span>{computedProfitForYear.toLocaleString()}</span>
                      </div>
                    </div>
                  </div>

                </div>
              ) : (
                <div className="flex flex-col gap-6">
                  
                  {/* SFP Inputs */}
                  <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
                    <div className="bg-slate-950 p-3 rounded-xl border border-slate-800">
                      <label className="block text-[10px] font-bold text-slate-400 uppercase mb-1">Premises NBV ($)</label>
                      <input 
                        type="number" 
                        value={sfpPremises} 
                        onChange={(e) => setSfpPremises(parseInt(e.target.value) || 0)}
                        className="bg-slate-900 border border-slate-800 p-2 rounded w-full text-xs text-white"
                      />
                    </div>
                    <div className="bg-slate-950 p-3 rounded-xl border border-slate-800">
                      <label className="block text-[10px] font-bold text-slate-400 uppercase mb-1">Machinery NBV ($)</label>
                      <input 
                        type="number" 
                        value={sfpMachinery} 
                        onChange={(e) => setSfpMachinery(parseInt(e.target.value) || 0)}
                        className="bg-slate-900 border border-slate-800 p-2 rounded w-full text-xs text-white"
                      />
                    </div>
                    <div className="bg-slate-950 p-3 rounded-xl border border-slate-800">
                      <label className="block text-[10px] font-bold text-slate-400 uppercase mb-1">Trade Receivables ($)</label>
                      <input 
                        type="number" 
                        value={sfpReceivables} 
                        onChange={(e) => setSfpReceivables(parseInt(e.target.value) || 0)}
                        className="bg-slate-900 border border-slate-800 p-2 rounded w-full text-xs text-white"
                      />
                    </div>
                    <div className="bg-slate-950 p-3 rounded-xl border border-slate-800">
                      <label className="block text-[10px] font-bold text-slate-400 uppercase mb-1">Bank Balance ($)</label>
                      <input 
                        type="number" 
                        value={sfpBank} 
                        onChange={(e) => setSfpBank(parseInt(e.target.value) || 0)}
                        className="bg-slate-900 border border-slate-800 p-2 rounded w-full text-xs text-white"
                      />
                    </div>
                    <div className="bg-slate-950 p-3 rounded-xl border border-slate-800">
                      <label className="block text-[10px] font-bold text-slate-400 uppercase mb-1">Trade Payables ($)</label>
                      <input 
                        type="number" 
                        value={sfpPayables} 
                        onChange={(e) => setSfpPayables(parseInt(e.target.value) || 0)}
                        className="bg-slate-900 border border-slate-800 p-2 rounded w-full text-xs text-white"
                      />
                    </div>
                    <div className="bg-slate-950 p-3 rounded-xl border border-slate-800">
                      <label className="block text-[10px] font-bold text-slate-400 uppercase mb-1">Opening Capital ($)</label>
                      <input 
                        type="number" 
                        value={sfpCapital} 
                        onChange={(e) => setSfpCapital(parseInt(e.target.value) || 0)}
                        className="bg-slate-900 border border-slate-800 p-2 rounded w-full text-xs text-white"
                      />
                    </div>
                  </div>

                  {/* SFP Render sheet */}
                  <div className="bg-slate-950 p-5 rounded-2xl border border-indigo-500/10">
                    <span className="text-[10px] text-indigo-400 font-bold uppercase tracking-wider">Computed SFP Statement Layout</span>
                    <div className="mt-4 flex flex-col gap-3 font-mono text-xs">
                      <div className="text-indigo-300 font-bold">Non-Current Assets</div>
                      <div className="flex justify-between pl-4">
                        <span>Premises (NBV)</span>
                        <span>{sfpPremises.toLocaleString()}</span>
                      </div>
                      <div className="flex justify-between pl-4">
                        <span>Machinery (NBV)</span>
                        <span>{sfpMachinery.toLocaleString()}</span>
                      </div>
                      <div className="flex justify-between border-b border-slate-800 pb-1 text-slate-300 font-bold">
                        <span>Total Non-Current Assets</span>
                        <span>{computedTotalNCA.toLocaleString()}</span>
                      </div>

                      <div className="text-indigo-300 font-bold mt-2">Current Assets</div>
                      <div className="flex justify-between pl-4">
                        <span>Inventory</span>
                        <span>{sfpInventory.toLocaleString()}</span>
                      </div>
                      <div className="flex justify-between pl-4">
                        <span>Trade Receivables</span>
                        <span>{sfpReceivables.toLocaleString()}</span>
                      </div>
                      <div className="flex justify-between pl-4">
                        <span>Bank</span>
                        <span>{sfpBank.toLocaleString()}</span>
                      </div>
                      <div className="flex justify-between border-b border-slate-800 pb-1 text-slate-300 font-bold">
                        <span>Total Current Assets</span>
                        <span>{computedTotalCA.toLocaleString()}</span>
                      </div>

                      <div className="flex justify-between text-emerald-400 font-extrabold text-sm border-t-2 border-indigo-500 pt-2">
                        <span>TOTAL ASSETS</span>
                        <span>{computedTotalAssets.toLocaleString()}</span>
                      </div>

                      <div className="text-indigo-300 font-bold mt-4">Capital & Liabilities</div>
                      <div className="flex justify-between pl-4">
                        <span>Opening Capital</span>
                        <span>{sfpCapital.toLocaleString()}</span>
                      </div>
                      <div className="flex justify-between pl-4 text-emerald-400">
                        <span>Add: Net Profit (from Income statement)</span>
                        <span>{computedProfitForYear.toLocaleString()}</span>
                      </div>
                      <div className="flex justify-between pl-4 text-rose-400">
                        <span>Less: Drawings (Mock)</span>
                        <span>(1,000)</span>
                      </div>
                      <div className="flex justify-between border-b border-slate-800 pb-1 text-slate-300 font-bold">
                        <span>Closing Capital Balance</span>
                        <span>{computedClosingCapital.toLocaleString()}</span>
                      </div>

                      <div className="text-indigo-300 font-bold mt-2">Current Liabilities</div>
                      <div className="flex justify-between pl-4">
                        <span>Trade Payables</span>
                        <span>{sfpPayables.toLocaleString()}</span>
                      </div>

                      <div className="flex justify-between text-emerald-400 font-extrabold text-sm border-t-2 border-indigo-500 pt-2">
                        <span>TOTAL CAPITAL AND LIABILITIES</span>
                        <span>{computedTotalLiabCapital.toLocaleString()}</span>
                      </div>

                      {computedTotalAssets === computedTotalLiabCapital ? (
                        <div className="bg-emerald-500/10 border border-emerald-400/30 p-2.5 rounded-lg text-emerald-400 text-center font-bold text-[11px] mt-2 animate-bounce">
                          đ Perfect Match! Your accounting equation is fully balanced!
                        </div>
                      ) : (
                        <div className="bg-rose-500/10 border border-rose-400/30 p-2.5 rounded-lg text-rose-400 text-center font-bold text-[11px] mt-2">
                          â ď¸ Unbalanced Balance Sheet. Check assets and capital balances to match equation.
                        </div>
                      )}

                    </div>
                  </div>

                </div>
              )}

            </div>
          )}

          {/* 5. MULTIPLE CHOICE TEST EXAM SIMULATOR */}
          {activeTab === 'quiz' && (
            <div className="bg-slate-900/60 border border-slate-800 p-6 rounded-3xl flex flex-col gap-6">
              
              <div className="flex flex-col md:flex-row md:items-center justify-between gap-4">
                <div>
                  <span className="bg-indigo-500/20 border border-indigo-400 text-indigo-300 text-xs px-2.5 py-1 rounded-full font-bold">
                    PAST PAPER MCQ MASTER
                  </span>
                  <h2 className="text-xl md:text-2xl font-black text-white mt-3 mb-1">
                    Past Paper Revision Quiz
                  </h2>
                </div>

                {/* Filter chapter */}
                <div>
                  <label className="block text-[10px] font-bold text-slate-400 uppercase mb-1">Filter by Chapter</label>
                  <select 
                    value={quizChapter}
                    onChange={(e) => { setQuizChapter(e.target.value); setCurrentQuizIndex(0); setIsAnswered(false); setSelectedOption(null); }}
                    className="bg-slate-950 border border-slate-800 text-xs text-slate-200 p-2 rounded-lg"
                  >
                    <option value="all">Show All Chapters</option>
                    {chaptersData.map(c => (
                      <option key={c.id} value={c.id}>Ch {c.id}: {c.title}</option>
                    ))}
                  </select>
                </div>
              </div>

              {activeQuestions.length > 0 ? (
                (() => {
                  const q = activeQuestions[currentQuizIndex];
                  return (
                    <div className="flex flex-col gap-5">
                      
                      {/* Quest progress */}
                      <div className="flex justify-between items-center text-xs text-slate-400">
                        <span>Question {currentQuizIndex + 1} of {activeQuestions.length}</span>
                        <span className="bg-slate-950 px-2.5 py-1 rounded-full border border-slate-800 font-semibold text-slate-300">
                          Topic Focus: Chapter {q.chapter}
                        </span>
                      </div>

                      {/* Question box */}
                      <div className="bg-slate-950 p-5 rounded-2xl border border-slate-800">
                        <p className="text-sm md:text-base font-bold text-white leading-relaxed">
                          {q.question}
                        </p>
                      </div>

                      {/* Options stack */}
                      <div className="flex flex-col gap-3">
                        {q.options.map((opt, idx) => {
                          let optStyle = "bg-slate-950/80 border-slate-800 text-slate-200 hover:bg-slate-900";
                          if (isAnswered) {
                            if (idx === q.answer) {
                              optStyle = "bg-emerald-500/10 border-emerald-500 text-emerald-400 font-bold";
                            } else if (idx === selectedOption) {
                              optStyle = "bg-rose-500/10 border-rose-500 text-rose-400 line-through";
                            } else {
                              optStyle = "bg-slate-950/40 border-slate-900 text-slate-500 opacity-50";
                            }
                          }
                          return (
                            <button
                              key={idx}
                              onClick={() => handleAnswerSubmit(idx)}
                              disabled={isAnswered}
                              className={`w-full p-4 rounded-xl border text-left text-xs transition leading-relaxed flex items-start gap-3 ${optStyle}`}
                            >
                              <span className="bg-slate-900 text-[10px] font-bold px-2 py-1 rounded border border-slate-800 shrink-0">
                                {String.fromCharCode(65 + idx)}
                              </span>
                              <span>{opt}</span>
                            </button>
                          );
                        })}
                      </div>

                      {/* Explanation Reveal */}
                      {isAnswered && (
                        <div className="bg-indigo-950/30 border border-indigo-500/20 p-4 rounded-xl text-xs leading-relaxed text-indigo-200">
                          <div className="font-extrabold text-amber-400 uppercase tracking-widest mb-1.5 flex items-center gap-1.5">
                            <Info className="w-4 h-4" /> Explanation Worksheet
                          </div>
                          <p>{q.explanation}</p>
                        </div>
                      )}

                      {/* Navigation buttons */}
                      {isAnswered && (
                        <button 
                          onClick={handleNextQuiz}
                          className="bg-indigo-600 hover:bg-indigo-500 text-white font-bold text-xs py-3 px-5 rounded-xl transition ml-auto flex items-center gap-1.5"
                        >
                          <span>Next Revision Question</span> <ChevronRight className="w-4 h-4" />
                        </button>
                      )}

                    </div>
                  );
                })()
              ) : (
                <div className="text-center py-12 text-slate-400 text-xs font-semibold">
                  No questions match your current chapter filter. Try changing the filter parameters!
                </div>
              )}

            </div>
          )}

          {/* 6. MASTER ACCOUNTS DICTIONARY LIST */}
          {activeTab === 'master_accounts' && (
            <div className="bg-slate-900/60 border border-slate-800 p-6 rounded-3xl flex flex-col gap-4">
              
              <div>
                <span className="bg-indigo-500/20 border border-indigo-400 text-indigo-300 text-xs px-2.5 py-1 rounded-full font-bold">
                  LEDGER DIRECTORY
                </span>
                <h2 className="text-xl md:text-2xl font-black text-white mt-3 mb-1">
                  Master List of Accounting Variables
                </h2>
                <p className="text-xs text-slate-400">
                  Comprehensive standard catalog of ledger classifications required for IGCSE double-entry tracking.
                </p>
              </div>

              {/* Listing accounts in grid */}
              <div className="grid grid-cols-1 md:grid-cols-2 gap-4 max-h-[500px] overflow-y-auto pr-2">
                {masterAccounts.map((acc, idx) => (
                  <div key={idx} className="bg-slate-950 p-4 rounded-xl border border-slate-800 hover:border-indigo-500/30 transition">
                    <div className="flex justify-between items-start gap-2">
                      <span className="font-bold text-xs text-indigo-300">{acc.name}</span>
                      <span className={`text-[9px] uppercase font-bold px-1.5 py-0.5 rounded ${
                        acc.drCr === 'Debit' ? 'bg-red-500/10 text-red-400' : 'bg-emerald-500/10 text-emerald-400'
                      }`}>
                        {acc.drCr}
                      </span>
                    </div>
                    <span className="text-[10px] text-slate-400 font-semibold block mt-1">Classification: {acc.type}</span>
                    <p className="text-[11px] text-slate-400 mt-2 leading-relaxed">{acc.desc}</p>
                  </div>
                ))}
              </div>

            </div>
          )}

        </main>
      </main>

      {/* FOOTER */}
      <footer className="border-t border-indigo-500/10 bg-slate-950 py-6 px-4 mt-12">
        <div className="max-w-7xl mx-auto flex flex-col md:flex-row items-center justify-between gap-4 text-xs text-slate-400">
          <div>
            <p className="font-extrabold text-slate-300">MR. MAHDY'S IGCSE Accounting Suite ÂŠ 2026</p>
            <p className="mt-1">Specifically formatted to match Cambridge Assessment International Education Syllabus standards.</p>
          </div>
          <div className="flex gap-4">
            <button onClick={() => { setActiveTab('notes'); setSelectedChapter(1); }} className="hover:underline">Syllabus Overview</button>
            <span>â˘</span>
            <button onClick={() => setActiveTab('master_accounts')} className="hover:underline">Accounts Directory</button>
          </div>
        </div>
      </footer>

    </div>
  );
}

```
