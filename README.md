# Canton Network Decentralized Lending Protocol

[![Daml](https://img.shields.io/badge/Daml-2.10.2-blue)](https://daml.com)
[![Canton](https://img.shields.io/badge/Canton-Network-green)](https://canton.network)
[![License](https://img.shields.io/badge/License-Apache%202.0-yellow)](LICENSE)

A privacy-first, multi-party lending protocol built on Canton Network that enables institutional-grade lending with DeFi-style transparency.

## 🌟 Key Features

- **Sub-Transaction Privacy**: Borrower identities hidden from investors using Canton's privacy features
- **Multi-Party Workflows**: Seamless coordination between DAO, banks, investors, borrowers, regulators, and oracles
- **Rule-Based Lending**: Customizable lending rules per pool (credit scores, collateral ratios, KYC requirements)
- **Oracle Integration**: Real-world data feeds for KYC, credit scores, and asset valuations
- **Regulatory Compliance**: Automated reporting with full transparency to authorized regulators
- **Privacy-Preserving Returns**: Each investor sees only their own returns, not others'

## 📋 Table of Contents

- [Architecture Overview](#architecture-overview)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Project Structure](#project-structure)
- [Running Tests](#running-tests)
- [Running Demo](#running-demo)
- [Key Concepts](#key-concepts)
- [Privacy Model](#privacy-model)
- [Deployment](#deployment)
- [Contributing](#contributing)
- [License](#license)

## 🏗️ Architecture Overview

### Contract Hierarchy

```
ProtocolRoot (DAO)
  ├─ BankSubtree (per bank)
  │   ├─ LendingPool
  │   │   ├─ LendingPoolRuleSet
  │   │   ├─ InvestmentPool
  │   │   │   ├─ InvestorPosition (per investor)
  │   │   │   └─ PrivateInvestorReturns (per investor) [PRIVATE]
  │   │   └─ ActiveLoan (per loan)
  │   │       ├─ CollateralLock
  │   │       ├─ LoanRepaymentTracker
  │   │       ├─ PrivateLoanAgreement [PRIVATE]
  │   │       └─ PublicLoanSummary (bucketed data)
  │   └─ ReportSchedule (regulatory reporting)
  └─ Oracle Contracts (KYC, Credit, Valuation)
```

### Party Roles

| Party | Role | Signatory On | Observer On |
|-------|------|--------------|-------------|
| DAO | Protocol governance | ProtocolRoot, pools | All contracts |
| Bank | Lending operations | Pools, loans | Regulatory reports |
| Investor | Capital provider | Investment | PublicLoanSummary |
| Borrower | Loan recipient | Loans | PrivateLoanAgreement |
| Regulator | Compliance oversight | - | PrivateKYC, reports |
| Oracle | Data provider | Oracle results | Oracle requests |

## 📦 Prerequisites

- [Daml SDK 2.10.2](https://docs.daml.com/getting-started/installation.html)
- Canton Network access (for deployment)
- Basic understanding of Daml and functional programming

## 🚀 Installation

### 1. Clone the Repository

```bash
git clone <url>
cd loan-dapp
```

### 2. Install Daml SDK

```bash
# macOS
curl -sSL https://get.daml.com/ | sh -s 2.10.2

# Windows (PowerShell)
(New-Object System.Net.WebClient).DownloadString('https://get.daml.com/') | powershell

# Linux
wget -q https://get.daml.com/ -O - | sh -s 2.10.2
```

### 3. Verify Installation

```bash
daml version
# Should output: 2.10.2
```

## 📁 Project Structure

```
loan-dapp/
├── daml/
│   ├── LendingProtocol/
│   │   ├── Bank/
│   │   │   ├── LendingPool.daml     # Core lending pool
│   │   │   ├── Pools.daml           # Investment pools
│   │   │   └── Rules.daml           # Rule system
│   │   ├── Borrower/
│   │   │   └── Loan.daml            # Loan lifecycle
│   │   ├── Oracle.daml              # Oracle integration
│   │   ├── Privacy.daml             # Privacy-preserving contracts
│   │   ├── Regulator.daml           # Regulatory reporting
│   │   ├── Root.daml                # Protocol root & governance
│   │   └── Types.daml               # Shared types
│   └── Scripts/
│       ├── Setup.daml               # Basic setup script
│       ├── Tests.daml               # Comprehensive test suite
│       └── Demo.daml                # End-to-end demo
├── daml.yaml                        # Project configuration
└── README.md
```

## 🧪 Running Tests

### Run All Tests

```bash
daml test --show-coverage
```

### Run Specific Test

```bash
# Test 1: Protocol Setup
daml script --dar .daml/dist/loan-dapp-0.0.1.dar \
  --script-name Scripts.Tests:testProtocolSetup

# Test 3: Investor Flow  
daml script --dar .daml/dist/loan-dapp-0.0.1.dar \
  --script-name Scripts.Tests:testInvestorFlow

# Test 5: Complete Loan Cycle
daml script --dar .daml/dist/loan-dapp-0.0.1.dar \
  --script-name Scripts.Tests:testCompleteLoanCycle
```

### Test Coverage

The test suite includes:

1. **Protocol Setup & Bank Onboarding** - DAO creates protocol, invites bank
2. **Lending Pool Creation** - Bank proposes pool with rules, DAO approves
3. **Investor Flow** - Investors contribute capital to pool
4. **Oracle Integration** - KYC and credit score verification
5. **Complete Loan Cycle** - Application → KYC → Credit Check → Approval → Payments
6. **Privacy Features** - Verification that private data is properly hidden
7. **Regulatory Reporting** - Monthly report generation and regulator review

## 🎬 Running Demo

### Interactive Demo

```bash
daml script --dar .daml/dist/loan-dapp-0.0.1.dar \
  --script-name Scripts.Demo:demo
```

The demo showcases:

1. **Protocol Initialization** - DAO creates protocol
2. **Bank Onboarding** - Bank joins and invites regulator
3. **Oracle Setup** - KYC and credit bureau oracles
4. **Pool Creation** - Bank creates lending pool with rules
5. **Investment** - Two investors contribute $250K total
6. **Loan Application** - Borrower applies for $50K loan
7. **KYC Process** - Oracle verifies identity
8. **Credit Check** - Oracle provides credit score
9. **Loan Approval** - Bank funds loan with privacy features
10. **Repayments** - Borrower makes 3 monthly payments
11. **Regulatory Report** - Bank generates monthly compliance report
12. **Regulator Review** - Regulator acknowledges report

### Expected Output

```
============================================
🚀 CANTON LENDING PROTOCOL DEMO
============================================

📋 PHASE 1: Protocol Initialization
-------------------------------------------
✓ DAO created
✓ GlobalBank created
✓ Financial Regulator created
✓ Investors created (Alice, Bob)
✓ Borrower created (Carol)
✓ Oracle services created

🏛️  DAO initializing protocol with 0.5% fee...
✓ Protocol created successfully

[... continues through all phases ...]

============================================
✅ DEMO COMPLETE!
============================================

🎯 Key Features Demonstrated:
  ✓ Multi-party protocol
  ✓ Privacy-preserving KYC
  ✓ Privacy-preserving loan details
  ✓ Rule-based lending
  ✓ Oracle integration
  ✓ Regulatory reporting & oversight
```

## 🔑 Key Concepts

### Privacy Levels

**PRIVATE Contracts** (Limited Observers)
- `PrivateKYCData` - Only bank, borrower, regulator
- `PrivateLoanAgreement` - Only bank, borrower, regulator
- `PrivateInvestorReturns` - Only bank and that specific investor

**PUBLIC Contracts** (Broad Observers)
- `PublicLoanSummary` - All pool investors (but with bucketed/anonymized data)
- `RegulatoryReport` - Regulator (aggregated data)

### Data Bucketing

To preserve privacy, exact values are bucketed:

| Actual | Public |
|--------|--------|
| $75,000 loan | "$50-100K" |
| 8.5% interest | "7-10%" |
| 720 credit score | "good" |

### Rule System

Each lending pool has a customizable rule set:

```daml
-- Example rules
Rule {
  ruleId = "kyc-required",
  category = ParticipationRules,
  ruleType = RequireKyc,
  enabled = True
}

Rule {
  ruleId = "min-credit",
  category = BorrowingRules,
  ruleType = MinimumCreditScoreForRole {
    role = Borrower,
    minScore = 650
  },
  enabled = True
}
```

Rules are validated at appropriate stages:
- **Participation Rules** → When investor invests or borrower applies
- **Investment Rules** → When investor invests
- **Borrowing Rules** → When loan is approved
- **Withdrawal Rules** → When investor withdraws

## 🔐 Privacy Model

### Canton's Sub-Transaction Privacy

Unlike public blockchains where all data is visible to everyone, Canton enables selective disclosure:

```
┌─────────────────────────────────────────────────────────┐
│                     PrivateKYCData                      │
│                                                         │
│  Name: Carol Thompson                                   │
│  SSN: 123-45-6789                                       │
│  Address: 456 Oak Ave                                   │
│                                                         │
│  Visible to: [Bank, Carol, Regulator]                   │
│  Hidden from: [All Investors]                           │
└─────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────┐
│                   PublicLoanSummary                     │
│                                                         │
│  Loan Amount: "$50-100K" (NOT exact)                    │
│  Interest: "7-10%" (NOT exact)                          │
│  Credit: "good" (NOT exact score)                       │
│  Borrower: NEVER DISCLOSED                              │
│                                                         │
│  Visible to: [All Pool Investors]                       │
│  Cannot identify: Individual borrower                   │
└─────────────────────────────────────────────────────────┘
```

### Privacy Guarantees

✅ Investors CANNOT see:
- Borrower identities
- Exact loan amounts
- Exact interest rates
- Other investors' returns

✅ Borrowers CANNOT see:
- Other borrowers' identities
- Other borrowers' loan terms
- Investor identities (unless disclosed)

✅ Regulators CAN see:
- All borrower identities (via PrivateKYCData)
- All exact loan terms (via PrivateLoanAgreement)
- All compliance metrics

## 🚢 Deployment

### Local Testing (Daml Sandbox)

```bash
# Build the project
daml build

# Start Daml Sandbox
daml sandbox --port 6865

# In another terminal, run demo
daml script --dar .daml/dist/loan-dapp-0.0.1.dar \
  --script-name Scripts.Demo:demo \
  --ledger-host localhost \
  --ledger-port 6865
```

### Canton Network Deployment

1. **Setup Canton Participant Nodes**
   ```bash
   # Configure nodes for each party
   - DAO node
   - Bank node(s)
   - Regulator node(s)
   - Oracle node(s)
   ```

2. **Deploy DAR**
   ```bash
   daml build
   # Upload loan-dapp-0.0.1.dar to all participant nodes
   ```

3. **Initialize Protocol**
   ```bash
   # Run setup script to create ProtocolRoot
   daml script --dar .daml/dist/loan-dapp-0.0.1.dar \
     --script-name Scripts.Setup:setup \
     --ledger-host <canton-host> \
     --ledger-port <canton-port>
   ```

4. **Monitor**
   ```bash
   # Use Canton Console to monitor
   # Use Daml Navigator for UI
   ```

## 📊 Metrics & Monitoring

### Key Metrics to Track

**Pool Level:**
- Total capitalization
- Available capital
- Utilization rate (loans / capital)
- Number of active loans
- Default rate

**Loan Level:**
- Payment history
- Days past due
- Collateral ratio (real-time from oracle)
- Health factor

**System Level:**
- Number of active pools
- Total value locked (TVL)
- DAO fees collected
- KYC compliance rate

### Monitoring Tools

- **Canton Console** - Real-time contract inspection
- **Daml Navigator** - Web UI for contract exploration
- **Custom Dashboard** - Build using Daml JSON API

## 🔧 Troubleshooting

### Common Issues

**1. CONTRACT_NOT_FOUND Error**

**Cause:** Stale contract ID reference (contract was consumed and recreated)

**Solution:** Always capture new contract IDs from consuming choices:

```daml
-- ❌ WRONG - discards new CID
exercise poolCid RecordInvestment with amount

-- ✅ CORRECT - captures new CID
newPoolCid <- exercise poolCid RecordInvestment with amount
```

**2. Signatory Mismatch**

**Cause:** Trying to create contract without all required signatories

**Solution:** Use `submitMulti`:

```daml
-- For contract requiring dao and bank as signatories
submitMulti [dao, bank] [] do
  createCmd MyContract with dao; bank; ...
```

**3. Rule Validation Failure**

**Cause:** Transaction violates a pool rule

**Solution:** Check rule requirements:
```daml
-- View pool rules
exercise ruleSetCid GetRulesInCategory with category = BorrowingRules
```

## 🤝 Contributing

We welcome contributions! Here's how:

1. **Fork the repository**
2. **Create a feature branch** (`git checkout -b feature/amazing-feature`)
3. **Commit your changes** (`git commit -m 'Add amazing feature'`)
4. **Push to the branch** (`git push origin feature/amazing-feature`)
5. **Open a Pull Request**

### Development Guidelines

- Follow [Daml best practices](https://docs.daml.com/daml/intro/10_BestPractices.html)
- Add tests for new features
- Update documentation
- Maintain privacy boundaries
- Keep contracts composable

### Testing Checklist

Before submitting PR:
- [ ] All tests pass (`daml test`)
- [ ] New tests added for new features
- [ ] Demo script runs successfully
- [ ] Documentation updated
- [ ] Privacy model verified

## 📄 License

This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- **Canton Network** - For the privacy-preserving infrastructure
- **Digital Asset** - For the Daml language and tooling
- **Hackathon Organizers** - For the opportunity to build this

## 📞 Contact

For questions or support:
- Open an issue on GitHub
- Join Canton Network Discord
- Email: [your-email]

## 🗺️ Roadmap

### Phase 1 (MVP - Current)
- [x] Protocol initialization
- [x] Lending pool creation
- [x] Investment flows
- [x] Loan application & approval
- [x] Loan repayment
- [x] KYC/Credit oracle integration
- [x] Regulatory reporting
- [x] Privacy features

### Phase 2
- [ ] Cross-bank lending
- [ ] Secondary market for loan positions
- [ ] Liquidator network
- [ ] Advanced oracle integration
- [ ] Multi-asset support
- [ ] Token backed DAO governance
- [ ] Early adopter reward program
- [ ] Extended rule system

### Phase 3
- [ ] DAO token governance
- [ ] Staking mechanisms
- [ ] Insurance pools
- [ ] Cross-chain bridges
- [ ] Further extended rule system

---

Built with ❤️ for Canton Network Hackathon

**Happy Lending! 🚀**
