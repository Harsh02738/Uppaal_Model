# Logic in Computer Science Project – Library, Bank, and Human System (UPPAAL Model)

This project models a secure library membership and book issuing system integrated with banking and human interaction layers, using **UPPAAL** to verify safety, liveness, and deadlock properties.

## 🧠 Overview

The system simulates a user interacting with:
- A **Library System**: For signing in, issuing books, and managing memberships.
- A **Banking System**: For validating and processing payments.
- A **Human System**: Representing user behavior and synchronization with other systems.

The model uses three-level **synchronization** and **committed states** to ensure accurate representation of real-time interactions.

---

## 🛠 System Components

### 📚 Library System
- Handles user sign-in, membership management (Gold/Platinum), and book issuing.
- Allows users without membership to browse books, but restricts issuing.
- Supports upgrade flow from Gold → Platinum membership.
- All transitions are explicitly synchronized with user (human system) and bank states.

### 🏦 Bank System
- Verifies credentials and balance (`z`).
- Accepts/declines payments based on sufficiency.
- Notifies the user after successful transactions.
- Balance (`z`) initialized to 100; Gold = 20, Platinum = 40.

### 🧑 Human System
- Simulates user interaction like sign-in, searching, membership selection, payment decisions.
- Provides real-time feedback flow (e.g. accepted/rejected, timeout, logout).

---

## ✅ Verified Properties

### 🔐 Safety Properties

**Bank System**
- `A[] (bs.AccountBalance ≥ 0)`
- `A[] !(bs.TransactionSuccessful ∧ bs.TransactionFailed)`

**Library System**
- `A[] !(ls.SuccessfulPurchase ∧ ls.Purchase_Cancelled)`
- `A[] !(ls.UserSignedIn ∧ ls.Log_out)`

**Human System**
- `A[] !(hs.PaymentAccepted ∧ hs.PaymentRejected)`
- `A[] !(hs.Signed_In ∧ hs.Log_out)`

### 🔄 Liveness Properties

**Bank System**
- `A<>(bs.AccountBalance ≤ 0 ⇒ !bs.TransactionSuccessful)`
- `A<>(bs.AccountBalance ≤ 0 ⇒ bs.TransactionFailed)`

**Library System**
- `A<>(ls.Payment ⇒ ls.SuccessfulPurchase ∨ ls.Purchase_Cancelled)`
- `A<>(ls.SuccessfulPurchase ⇒ ls.Log_out)`

**Human System**
- `A<>(hs.ConfirmMembership ⇒ hs.PaymentAccepted ∨ hs.PaymentRejected)`
- `A<>(hs.PaymentAccepted ⇒ hs.Log_out)`

---

## 🧩 Assumptions

- Sign-in requires email ID.
- Membership purchase is only blocked due to insufficient funds.
- Users can browse books without membership, but issuing is restricted.
- Payment method auto-detects card type (Visa, Mastercard).
- No retry or rollback once transaction initiated.

---

## 🌀 Committed States & Synchronization

Three-level committed synchronization ensures accurate transitions:

- **Bank ↔ Human** (e.g. card validation, balance check)
- **Human ↔ Library** (e.g. book issuing, membership upgrade)
- **Library ↔ Bank** (e.g. final transaction outcome sync)

---

## 🚫 Deadlock Condition

- Verified: `A[] not deadlock`

No deadlocks occur across transitions due to well-defined synchronization and return paths.

---

## 🔧 Invariants Used

```c
int z = 100; // User bank balance
int x = 20;  // Gold membership price
int y = 40;  // Platinum membership price
