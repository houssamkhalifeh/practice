```mermaid
flowchart TD
    User[Cashier]
    POS[POS / API]
    App[Cash Register Application]
    Storage[(Storage)]

    User --> POS
    POS --> App
    App --> Storage

```
```mermaid
flowchart TD
    %% External Actor
    User[User / POS UI]

    %% Application Layer
    API[API / UI Layer]
    MediatR[MediatR Dispatcher]

    %% Handlers
    OpenH[OpenRegisterHandler]
    AddTxH[AddTransactionHandler]
    BalanceH[GetBalanceHandler]

    %% Domain
    Register[CashRegister Aggregate]
    Tx[CashTransaction]

    %% Infrastructure
    Repo[ICashRegisterRepository]
    Store[(In-Memory Storage)]

    %% Flows
    User -->|Open Register| API
    User -->|Add Transaction| API
    User -->|Get Balance| API

    API --> MediatR

    MediatR --> OpenH
    MediatR --> AddTxH
    MediatR --> BalanceH

    OpenH -->|Create| Register
    OpenH --> Repo

    AddTxH --> Repo
    Repo --> Register
    AddTxH -->|Add| Tx
    Tx --> Register

    BalanceH --> Repo
    Repo --> Register
    Register -->|Calculate Balance| BalanceH

    Repo --> Store
    Store --> Repo

```
```mermaid
sequenceDiagram
    autonumber

    participant User as User / POS UI
    participant API as API / UI Layer
    participant Mediator as MediatR
    participant Handler as AddTransactionHandler
    participant Repo as ICashRegisterRepository
    participant Register as CashRegister

    User->>API: Enter amount & description
    API->>Mediator: Send AddTransactionCommand
    Mediator->>Handler: Dispatch command
    Handler->>Repo: Get(registerId)
    Repo-->>Handler: CashRegister
    Handler->>Register: AddTransaction(amount, description)
    Register-->>Handler: Transaction added
    Handler-->>Mediator: Command handled
    Mediator-->>API: Success
    API-->>User: Confirmation
```
```
1. User enters a cash amount and description in the POS UI.
2. The UI sends an AddTransactionCommand to the API layer.
3. The API forwards the command to MediatR.
4. MediatR dispatches the command to AddTransactionHandler.
5. The handler retrieves the CashRegister from the repository.
6. The repository returns the CashRegister aggregate.
7. The handler calls AddTransaction on the CashRegister.
8. The CashRegister validates and stores the transaction.
9. The handler completes successfully.
10. MediatR returns success to the API.
11. The API confirms the operation to the user.
