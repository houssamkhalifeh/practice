```mermaid

flowchart TD

    %% External Entities
    User((Business User))
    Supplier((Supplier))
    SupplierAccount((Accounting))
    Approver((Approver))

    %% Data Stores
    DS_Intake[[Intake Request Data]]
    DS_Sourcing[[Sourcing Event Data]]
    DS_Contract[[Contract Repository]]
    DS_P2P[[P2P / AP Data]]

    %% Processes
    P1[Submit Intake Request]
    P2[Intake Review & Approval]
    P3[Create Sourcing Event]
    P4[Evaluate Bids & Award]
    P5[Create & Sign Contract]
    P6[Send Contract to P2P]
    P7[PO Creation & 3-way Match]
    P8[Issue Payment]

    subgraph SupplierSystem
        Supplier
        SupplierAccount
    end

    subgraph P2P
        P7
        DS_P2P
        P8
    end


    %% Flows
    subgraph Intake
        User --> P1 --> DS_Intake
        DS_Intake --> P2
        Approver --> P2 --> DS_Intake
    end

    subgraph Sourcing
        P2 --> P3 --> DS_Sourcing
        DS_Sourcing --> P4 --> DS_Sourcing
    end

    subgraph CLM
        P4 --> P5 --> DS_Contract
        P5 --> P6 --> DS_P2P
    end


    subgraph SupplierSystem
        Supplier --> P7
        Supplier --> P5
        Supplier --> P4
    end


    subgraph P2P
        User --> P7
        DS_P2P --> P7 --> P8
        P8 --> User
        P8 --> SupplierAccount
    end


