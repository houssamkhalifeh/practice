# Cash Register POS Application

A WPF-based Cash Register application built using **MVVM**, **SQLite**, and **C#**.  
Supports item-based transactions, federal/provincial taxes, partial and full refunds, and full audit tracking.

---

## Table of Contents

1. [Features](#features)  
2. [Architecture](#architecture)  
3. [Installation](#installation)  
4. [Database](#database)  
5. [Usage](#usage)  
6. [Refund Rules](#refund-rules)  
7. [Developer Notes](#developer-notes)  
8. [Contributing](#contributing)  
9. [License](#license)  

---

## Features

- MVVM-based WPF architecture  
- Item categories loaded from JSON  
- Tabbed category view with buttons for items  
- Real-time transaction total and tax calculation  
- Supports **Federal (5%)** and **Provincial (9.975%)** taxes  
- Transactions persisted in **SQLite**  
- Individual and full transaction refunds  
- Refund safety rules prevent double refunds  
- Transaction history with visual differentiation for refunds  
- Unit testing support

---

## Architecture

### Component Diagram

```mermaid
graph TD
    subgraph UI
        MW[MainWindow] --> TW[TransactionsWindow]
        TW --> TDW[TransactionDetailsWindow]
    end

    subgraph ViewModels
        MVM[MainViewModel]
        TVM[TransactionsViewModel]
        TDVM[TransactionDetailsViewModel]
    end

    subgraph Services
        STS[SQLiteTransactionService]
        SS[SQLiteService]
    end

    subgraph Models
        T[Transaction]
        TI[TransactionItem]
        I[Item]
        C[Category]
    end

    MW --> MVM
    TW --> TVM
    TDW --> TDVM

    MVM -->|Load Items / Categories| SS
    TVM -->|Load Transactions| STS
    TDVM -->|Load Transaction Details| STS
    TDVM -->|Create Refund Transaction| STS

    STS --> T
    STS --> TI
    SS --> I
    SS --> C
```
```mermaid
sequenceDiagram
    participant Cashier as Cashier (UI)
    participant TDVM as TransactionDetailsViewModel
    participant STS as SQLiteTransactionService
    participant DB as SQLite Database

    Cashier->>TDVM: Click "Refund Selected" / "Refund All"
    TDVM->>STS: Validate refundable items
    alt No refundable items
        STS-->>TDVM: Throw exception
        TDVM-->>Cashier: Show message "No refundable items"
    else Refund items available
        STS->>DB: Start DB transaction
        loop For each item to refund
            STS->>DB: Check if RefundedItemId exists
            DB-->>STS: Already refunded? (Yes/No)
            alt Already refunded
                STS-->>TDVM: Error for this item
            else Not refunded
                STS->>DB: Insert new TransactionItem with negative amounts
                STS->>DB: Link RefundedItemId to original
            end
        end
        STS->>DB: Insert refund transaction row
        DB-->>STS: Commit transaction
        STS-->>TDVM: Refund successful
        TDVM-->>Cashier: Show message "Refund completed successfully"
        TDVM->>STS: Reload transaction details
        STS-->>TDVM: Updated transaction + items
    end

    
```
