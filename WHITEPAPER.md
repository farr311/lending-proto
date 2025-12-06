# Decentralized Lending Protocol on Canton Network
## Privacy-First, Multi-Party Lending with Regulatory Compliance

**Version:** 1.0.0-MVP  
**Date:** December 2025  
**Hackathon:** Canton Network Hackathon  
**Built on:** Daml 2.10.2 / Canton Network

---

## Executive Summary

This protocol introduces a **privacy-preserving, decentralized lending platform** that leverages Canton Network's unique sub-transaction privacy features to enable:

1. **Multi-party lending workflows** without compromising sensitive data
2. **Regulatory compliance** with selective data visibility
3. **DAO-governed protocol** with bank-operated lending pools
4. **Privacy-first design** where investors can participate without seeing borrower identities
5. **Oracle integration** for real-world data (KYC, credit scores, asset valuations)

### The Core Innovation

Unlike public blockchain lending protocols where **all transaction details are visible to everyone**, our protocol uses Canton's sub-transaction privacy to create a system where:

- **Investors** see aggregated, bucketed loan data but NOT borrower identities
- **Borrowers** maintain privacy from other borrowers and investors
- **Regulators** see full compliance data for their jurisdiction
- **Banks** operate independently with full autonomy over their lending rules

This enables **institutional-grade lending** with **DeFi-style transparency** while maintaining **enterprise-level privacy**.

---

## 1. Architecture Overview

### 1.1 Hierarchical Contract Structure

```
ProtocolRoot (DAO-controlled)
    │
    ├─→ BankSubtree (Bank 1)
    │     ├─→ LendingPool 1
    │     │     ├─→ InvestmentPool
    │     │     │     ├─→ InvestorPosition (Investor A)
    │     │     │     └─→ PrivateInvestorReturns (Investor A) [PRIVATE]
    │     │     ├─→ ActiveLoan 1
    │     │     │     ├─→ CollateralLock
    │     │     │     ├─→ LoanRepaymentTracker
    │     │     │     ├─→ PrivateLoanAgreement [PRIVATE]
    │     │     │     └─→ PublicLoanSummary (Bucketed data for investors)
    │     │     └─→ LendingPoolRuleSet
    │     └─→ ReportSchedule (for regulators)
    │
    └─→ BankSubtree (Bank 2)
          └─→ ... (Independent pools)
```

### 1.2 Party Roles

| Party | Role | Permissions |
|-------|------|-------------|
| **DAO** | Protocol Governance | Create protocol, invite banks, set protocol fees |
| **Bank** | Lending Operations | Create pools, set rules, approve/reject loans |
| **Investor** | Capital Provider | Invest in pools, receive returns, view public summaries |
| **Borrower** | Loan Recipient | Apply for loans, make payments, manage collateral |
| **Regulator** | Oversight | View all compliance data, receive periodic reports |
| **Oracle** | Data Provider | Provide KYC, credit scores, asset valuations |

---

## 2. Privacy Model

### 2.1 Canton's Sub-Transaction Privacy

Canton Network enables **selective disclosure** of contract data. Unlike Ethereum where all contract state is public, Canton allows contracts to have different observers, enabling:

#### Private Contracts (Selective Visibility)

**PrivateKYCData**
- **Visible to:** Bank, Borrower, Regulator
- **Hidden from:** All investors, other borrowers
- **Contains:** Full name, national ID, address, phone, email, PEP status

**PrivateLoanAgreement**
- **Visible to:** Bank, Borrower, Regulator
- **Hidden from:** All investors
- **Contains:** Exact loan amount, exact interest rate, exact collateral details, special terms

**PrivateInvestorReturns**
- **Visible to:** Bank, Individual Investor
- **Hidden from:** Other investors
- **Contains:** Investor's exact investment amount, exact returns, APY

#### Public Contracts (Broad Visibility)

**PublicLoanSummary**
- **Visible to:** All pool investors
- **Contains:** Bucketed/anonymized data only
  - Loan amount → "$50-100K" (not $75,000)
  - Interest rate → "7-10%" (not 8.5%)
  - Credit score → "good" (not 720)
  - Borrower identity → **NEVER disclosed**

### 2.2 Data Bucketing for Privacy

To prevent identification through aggregation, we bucket all public data:

| Actual Value | Public Bucket |
|--------------|---------------|
| $75,000 loan | "$50-100K" |
| 8.5% interest | "7-10%" |
| 720 credit score | "good" |
| $95,000 collateral | "$50-100K" collateral |
| 73% LTV | "70-80%" |

This ensures investors can assess pool risk **without** identifying individual borrowers.

---

## 3. Core Workflows

### 3.1 Protocol Initialization

```daml
1. DAO creates ProtocolRoot
   ├─ Sets protocol fee (e.g., 0.5% of interest)
   └─ Sets reporting frequency (e.g., monthly)

2. DAO invites Bank
   └─ Creates BankInvitation

3. Bank accepts invitation
   └─ Creates BankSubtree (bank's operational space)
```

### 3.2 Lending Pool Creation

```daml
1. Bank proposes LendingPool
   ├─ Defines pool parameters (max cap, description)
   ├─ Defines initial rules
   │   ├─ Participation rules (KYC, credit score)
   │   ├─ Investment rules (min/max amounts)
   │   ├─ Borrowing rules (collateral ratio, term limits)
   │   └─ Withdrawal rules (annual distribution only)
   └─ Invites regulators

2. DAO approves pool
   ├─ Creates LendingPool contract
   └─ Creates LendingPoolRuleSet contract

3. Bank creates InvestmentPool
   └─ Links to LendingPool and RuleSet
```

### 3.3 Investment Flow

```daml
1. Investor exercises Invest choice on InvestmentPool
   ├─ Validates participation rules (KYC if required)
   ├─ Validates investment rules (min/max amounts)
   ├─ Checks pool capacity
   ├─ Creates/updates InvestorPosition
   ├─ Creates/updates PrivateInvestorReturns [PRIVATE]
   └─ Updates LendingPool capitalization

2. Investment recorded
   ├─ Pool totalInvested increases
   ├─ Lending pool currentCapitalization increases
   └─ Capital available for loans
```

### 3.4 Loan Application & Approval Flow

```daml
1. Borrower creates LoanApplication
   └─ Status: KycPending

2. Bank requests KYC from Oracle
   └─ Oracle provides KycResult

3. Bank creates PrivateKYCData [PRIVATE]
   ├─ Visible to: Bank, Borrower, Regulator
   └─ Hidden from: All investors

4. Bank requests Credit Score from Oracle
   └─ Oracle provides CreditScoreResult

5. Bank validates application against rules
   ├─ KYC verified? ✓
   ├─ Credit score >= minimum? ✓
   ├─ Collateral ratio >= minimum? ✓
   └─ Pool has available capital? ✓

6. Bank approves loan
   ├─ Creates ActiveLoan
   ├─ Creates CollateralLock
   ├─ Creates LoanRepaymentTracker
   ├─ Creates PrivateLoanAgreement [PRIVATE]
   │   └─ Exact terms visible only to Bank, Borrower, Regulator
   ├─ Creates PublicLoanSummary
   │   └─ Bucketed data visible to all pool investors
   └─ Updates LendingPool (increases totalLoansIssued)
```

### 3.5 Loan Repayment Flow

```daml
1. Borrower exercises MakePayment on ActiveLoan
   ├─ Payment split into interest + principal
   ├─ DAO fee calculated (0.5% of interest)
   └─ LoanRepaymentTracker updated

2. If fully repaid:
   ├─ Loan status → Completed
   ├─ Collateral released
   └─ LendingPool updated (decreases totalLoansIssued)

3. If partial payment:
   ├─ Loan continues as Active
   └─ LendingPool updated with principal repaid
```

### 3.6 Regulatory Reporting Flow

```daml
1. ReportSchedule triggers monthly report

2. Bank generates RegulatoryReport
   ├─ Aggregates all pool metrics
   │   ├─ Total active loans
   │   ├─ KYC compliance rate
   │   ├─ Average credit score
   │   ├─ Default rate
   │   └─ DAO fees collected
   └─ Status: Pending

3. Regulator reviews report
   ├─ Can acknowledge (approve)
   ├─ Can flag (request more info)
   └─ Bank can respond to flags
```

---

## 4. Rule System

### 4.1 Rule Categories

The protocol uses a **rule-based validation system** where each lending pool can define custom rules:

#### Participation Rules
- **RequireKyc** - KYC verification mandatory
- **WhitelistedPartiesOnly** - Only specific parties allowed
- **BlacklistedParty** - Specific parties blocked
- **MinimumCreditScoreForRole** - Credit score requirements per role

#### Investment Rules
- **MinimumInvestmentAmount** - e.g., $10,000 minimum
- **MaximumInvestmentPerParty** - Prevent concentration
- **AllowedInvestmentAssets** - e.g., only USDT

#### Borrowing Rules
- **MinimumCollateralRatio** - e.g., 120% required
- **MinimumLoanAmount** / **MaximumLoanAmount**
- **MinimumLoanTermDays** / **MaximumLoanTermDays**
- **MinimumInterestRate** / **MaximumInterestRate**
- **AllowedBorrowAssets** - e.g., only USDT loans

#### Withdrawal Rules
- **AnnualDistributionOnly** - No mid-term withdrawals

### 4.2 Rule Validation

Rules are validated at the appropriate lifecycle stage:

```daml
Participation Rules → Validated when: Investor invests, Borrower applies
Investment Rules → Validated when: Investor invests
Borrowing Rules → Validated when: Loan approved
Withdrawal Rules → Validated when: Investor withdraws
```

Each rule can be:
- **Enabled** or **Disabled** (soft delete)
- **Updated** (change parameters)
- **Removed** (hard delete)

---

## 5. Oracle Integration

### 5.1 Oracle Types

#### KYC Oracle
**Purpose:** Identity verification  
**Provides:**
- Verification status (verified/rejected)
- Risk score (0-100)
- Verification type (passport, driver's license)
- Expiry date

**Flow:**
```
Bank → RequestKycVerification → KYC Oracle
KYC Oracle → ProvideKycResult → Bank
Bank → Creates PrivateKYCData (visible only to Bank, Borrower, Regulator)
```

#### Credit Score Oracle
**Purpose:** Creditworthiness assessment  
**Provides:**
- Credit score (e.g., 300-850)
- Score model (FICO, VantageScore)
- Risk category (excellent, good, fair, poor)

**Flow:**
```
Bank → RequestCreditScore → Credit Bureau
Credit Bureau → ProvideCreditScore → Bank
Bank → Updates LoanApplication with score
```

#### Asset Valuation Oracle
**Purpose:** Collateral valuation  
**Provides:**
- Valuation amount
- Valuation method (automated, manual appraisal)
- Confidence level

**Flow:**
```
Bank/Borrower → RequestAssetValuation → Valuation Oracle
Valuation Oracle → ProvideValuation → Bank/Borrower
Bank → Updates LoanApplication with collateral value
```

### 5.2 Oracle Trust Model

In the MVP:
- Oracles are **trusted third parties**
- Banks choose which oracles to use
- Results are **cryptographically signed** by oracle party

In production:
- Multiple oracles for redundancy
- Consensus mechanisms for critical data
- Oracle reputation system
- Slashing for incorrect data

---

## 6. Economic Model

### 6.1 Fee Structure

**DAO Fee:** 0.5% of interest collected
- Example: Loan with $1,000 interest → DAO receives $5
- **Purpose:** Protocol development, DAO treasury, token backing

**Bank Share:** Configurable per pool (not in MVP)
- **Future:** Bank can take a spread (e.g., 1-2% of interest)

**Investor Returns:** Remaining interest distributed proportionally
- Distribution frequency: Annual (configurable per pool)

### 6.2 Example Economics

**Scenario:**
- Loan: $100,000 @ 10% APR for 1 year
- Interest collected: $10,000
- Investors: Alice ($60K), Bob ($40K)

**Distribution:**
```
Total Interest: $10,000
├─ DAO Fee (0.5%): $50
├─ Bank Share (future): $0 (MVP)
└─ Investor Returns: $9,950
    ├─ Alice (60%): $5,970
    └─ Bob (40%): $3,980
```

### 6.3 Collateral Management

**On Loan Approval:**
- Borrower locks collateral (e.g., $120,000 BTC for $100,000 loan)
- CollateralLock contract created
- Collateral value tracked via oracle

**On Successful Repayment:**
- Collateral fully released to borrower
- CollateralLock archived

**On Default:**
- Collateral liquidated
- Proceeds distributed:
  - Bank portion (for operational costs)
  - Investor portion (proportional to investment)

---

## 7. Security & Safety Features

### 7.1 Contract-Level Safety

**Immutable Rules:**
- Rule changes logged and versioned
- Cannot retroactively affect existing loans

**Atomic Transactions:**
- All multi-step operations are atomic
- Either all succeed or all fail (no partial states)

**Double-Spending Prevention:**
- Lending pool tracks available capital
- Cannot issue loans beyond available capital

**Stale Reference Prevention:**
- All consuming choices return new contract IDs
- Contracts always reference current state

### 7.2 Access Control

**Signatory Requirements:**
- `DAO + Bank` must sign pool creation
- `Bank + Borrower` must sign loan approval
- `Bank + Investor` must sign investment

**Observer Restrictions:**
- Private contracts have limited observers
- Only authorized parties can see sensitive data

### 7.3 Validation at Every Step

**Pre-Investment:**
- KYC verified?
- Investment within limits?
- Pool capacity available?

**Pre-Loan:**
- KYC approved?
- Credit score meets minimum?
- Collateral ratio sufficient?
- Rules satisfied?

**Pre-Payment:**
- Loan active?
- Payment breakdown correct?

---

## 8. Regulatory Compliance

### 8.1 KYC/AML Compliance

**For Investors:**
- Optional KYC (configurable per pool)
- Can require whitelisting

**For Borrowers:**
- **Mandatory KYC** (enforced at code level)
- Loan CANNOT be approved without KYC verification
- Private data created and shared with regulator

### 8.2 Reporting Requirements

**Monthly Regulatory Reports Include:**
- Total active loans
- Total loan volume
- Default rates
- KYC compliance rate (% of loans with verified KYC)
- Average credit score
- Average collateral ratio
- Interest collected
- DAO fees collected
- Number of active investors

**Report Process:**
```
1. ReportSchedule automatically triggers monthly
2. Bank generates RegulatoryReport with aggregated metrics
3. Regulator reviews and acknowledges/flags
4. Bank can respond to flagged items
5. Complete audit trail maintained
```

### 8.3 Privacy vs. Compliance Balance

**What Regulators See:**
- Full borrower identity (via PrivateKYCData)
- Exact loan terms (via PrivateLoanAgreement)
- All compliance metrics
- Individual loan details (via LoanReportEntry)

**What Investors See:**
- Bucketed loan data (via PublicLoanSummary)
- Pool-level aggregates
- **No borrower identities**
- **No exact loan amounts**

This enables **full regulatory compliance** while maintaining **privacy for participants**.

---

## 9. Canton Network Advantages

### 9.1 Why Canton Network?

| Feature | Traditional Blockchain | Canton Network |
|---------|----------------------|----------------|
| **Privacy** | All transactions public | Sub-transaction privacy |
| **Scalability** | Global consensus bottleneck | Parallel processing per subtree |
| **Interoperability** | Siloed ecosystems | Native cross-network sync |
| **Compliance** | Pseudonymous, hard to regulate | Built-in selective disclosure |
| **Performance** | ~15 TPS (Ethereum) | Thousands of TPS |

### 9.2 Canton Features Used

**Sub-Transaction Privacy**
- Different observers for different contracts
- Enables privacy-preserving lending

**Multi-Party Contracts**
- Single contract with multiple signatories
- Atomic multi-party agreement

**Contract Decomposition**
- Large workflows broken into smaller contracts
- Better performance and modularity

**Synchronization Domains**
- Banks can operate independent subtrees
- Selective synchronization for cross-bank features

---

## 10. Future Enhancements

### 10.1 Phase 2 Features

**Cross-Bank Lending**
- Loans funded by multiple banks
- Risk shared across institutions
- Canton sync enables cross-subtree coordination

**Secondary Market**
- Investors can trade loan positions
- Loan tranching (senior/junior debt)
- Privacy preserved in transfers

**Liquidator Network**
- Decentralized liquidation process
- Liquidators vote on default loans
- Rewards for accurate liquidation calls

### 10.2 Phase 3 Features

**DAO Token Integration**
- Protocol fees collected in DAO token
- Token-weighted governance
- Staking for protocol upgrades

**Advanced Oracle Integration**
- Multiple oracle consensus
- Oracle reputation system
- Dynamic collateral ratios based on volatility

**Multi-Asset Support**
- Loans in multiple stablecoins
- Cross-asset collateral
- Currency risk hedging

### 10.3 Production Readiness

**Audit Requirements**
- Smart contract security audit
- Economic model audit
- Privacy model verification
- Regulatory compliance review

**Infrastructure**
- Oracle reliability (99.9% uptime)
- Disaster recovery
- High-availability deployment
- Monitoring and alerting

**Legal Framework**
- Terms of service
- Privacy policy
- Regulatory licenses (per jurisdiction)
- Insurance (protocol-level)

---

## 11. Technical Implementation

### 11.1 Key Daml Patterns Used

**Template Composition**
```daml
-- Main contract delegates to sub-contracts
template ActiveLoan
  with
    lendingPoolCid : ContractId LendingPool
    collateralLockCid : ContractId CollateralLock
    repaymentTrackerCid : ContractId LoanRepaymentTracker
```

**Observer Segregation**
```daml
-- Private contract with limited observers
template PrivateKYCData
  where
    signatory bank, borrower
    observer regulator  -- ONLY regulator can see
```

**Consuming Choice Pattern**
```daml
-- Choice consumes and creates new contract
choice RecordInvestment : ContractId LendingPool
  -- Archives old pool, creates new pool with updated state
```

**Rule Validation Pattern**
```daml
-- Separate validation logic from business logic
validateRulesInCategory : [Rule] -> RuleCategory -> ValidationContext -> Either Text ()
```

### 11.2 Testing Strategy

**Unit Tests** (individual contract choices)
- Test each choice in isolation
- Mock dependencies
- Edge cases and error conditions

**Integration Tests** (multi-contract workflows)
- Test complete flows (setup → investment → loan → repayment)
- Test privacy boundaries
- Test multi-party interactions

**Property Tests** (invariants)
- Pool capital never exceeds max cap
- Loans never exceed available capital
- Collateral always locked before loan funded

### 11.3 Deployment Architecture

```
Canton Network Deployment
│
├─ Participant Node (DAO)
│   └─ Runs: ProtocolRoot contracts
│
├─ Participant Node (Bank 1)
│   └─ Runs: Bank's lending pools, loans
│
├─ Participant Node (Bank 2)
│   └─ Runs: Bank's lending pools, loans
│
├─ Participant Node (Regulator)
│   └─ Observer: Regulatory reports, KYC data
│
├─ Participant Node (Oracle 1 - KYC)
│   └─ Provides: KYC verification
│
└─ Participant Node (Oracle 2 - Credit Bureau)
    └─ Provides: Credit scores
```

---

## 12. Conclusion

This protocol demonstrates that **institutional-grade privacy** and **DeFi-style transparency** are not mutually exclusive. By leveraging Canton Network's unique sub-transaction privacy features, we enable:

✅ **Privacy-First Lending** - Borrower identities protected from investors  
✅ **Regulatory Compliance** - Full transparency to authorized regulators  
✅ **Decentralized Governance** - DAO controls protocol, banks control operations  
✅ **Multi-Party Workflows** - Seamless coordination between 6+ party types  
✅ **Enterprise-Ready** - Security, auditability, scalability from day one

The protocol is **production-ready for MVP deployment** and **extensible for Phase 2/3 features**.

---

## 13. References

**Daml Documentation**  
https://docs.daml.com

**Canton Network**  
https://www.canton.network

**Traditional Lending Protocols (for comparison)**  
- Aave (Ethereum)
- Compound (Ethereum)  
- Maple Finance (Ethereum)

**Regulatory Frameworks**  
- Basel III (banking regulations)
- MiCA (EU crypto regulations)
- SEC guidance on digital assets

---

## Appendix A: Contract Reference

### Core Contracts

| Contract | Purpose | Key Choices |
|----------|---------|-------------|
| ProtocolRoot | DAO governance | InviteBank, UpdateProtocolConfig |
| BankSubtree | Bank operations | RegisterPoolCreation, InviteRegulator |
| LendingPool | Pool management | RecordInvestment, RecordLoanIssuance |
| InvestmentPool | Investment tracking | Invest, DistributeReturns |
| LendingPoolRuleSet | Rule management | AddRule, UpdateRule, DisableRule |
| LoanApplication | Loan process | RequestKyc, RequestCreditScore, ApproveLoan |
| ActiveLoan | Loan lifecycle | MakePayment, MarkDefaulted, LiquidateLoan |
| CollateralLock | Collateral management | ReleaseCollateral, MarkLiquidated |
| LoanRepaymentTracker | Payment tracking | GetPaymentStatus |
| PrivateKYCData | Identity (private) | UpdateRiskAssessment |
| PrivateLoanAgreement | Loan terms (private) | AmendLoanTerms |
| PublicLoanSummary | Loan data (public) | UpdateLoanStatus |
| PrivateInvestorReturns | Returns (private) | RecordDistribution |
| RegulatoryReport | Compliance | AcknowledgeReport, FlagReport |
| ReportSchedule | Reporting trigger | GenerateReport |

### Oracle Contracts

| Contract | Purpose | Key Choices |
|----------|---------|-------------|
| KycOracle | KYC service | RequestKycVerification |
| KycRequest | KYC request | ProvideKycResult |
| CreditScoreOracle | Credit bureau | RequestCreditScore |
| CreditScoreRequest | Score request | ProvideCreditScore |
| AssetPriceOracle | Price feed | UpdatePrice, GetCurrentPrice |
| AssetValuationRequest | Valuation | ProvideValuation |

---

## Appendix B: Rule Types Reference

### Participation Rules
```daml
RequireKyc
WhitelistedPartiesOnly with parties : [Party]
BlacklistedParty with party : Party
MinimumCreditScoreForRole with role : PartyRole; minScore : Int
```

### Investment Rules
```daml
MinimumInvestmentAmount with amount : Decimal
MaximumInvestmentPerParty with amount : Decimal
AllowedInvestmentAssets with assets : [Text]
```

### Borrowing Rules
```daml
MinimumCollateralRatio with ratio : Decimal
AllowedBorrowAssets with assets : [Text]
MinimumLoanAmount with amount : Decimal
MaximumLoanAmount with amount : Decimal
MinimumLoanTermDays with days : Int
MaximumLoanTermDays with days : Int
MinimumInterestRate with rate : Decimal
MaximumInterestRate with rate : Decimal
```

### Withdrawal Rules
```daml
AnnualDistributionOnly
```

---

**END OF WHITEPAPER**

For questions or contributions, please contact the development team.

Built with ❤️ for Canton Network Hackathon
