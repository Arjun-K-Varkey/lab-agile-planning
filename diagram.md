flowchart TD
    %% Define Styling Classes (Matching your Eraser diagram theme)
    classDef startEnd fill:#14532d,stroke:#22c55e,color:#fff,stroke-width:2px;
    classDef decision fill:#78350f,stroke:#f59e0b,color:#fff,stroke-width:2px;
    classDef error fill:#7f1d1d,stroke:#ef4444,color:#fff,stroke-width:2px;
    classDef process fill:#1e3a8a,stroke:#3b82f6,color:#fff,stroke-width:2px;
    classDef hardware fill:#4c1d95,stroke:#8b5cf6,color:#fff,stroke-width:2px;
    classDef vendor fill:#0f172a,stroke:#eab308,color:#fff,stroke-width:3px,stroke-dasharray: 5 5;

    %% Row 1: Request & Payment
    A([Applicant Login via CCN]):::startEnd
    B{Pre-Validation Checks}:::decision
    C[Block Request]:::error
    D[Submit Tanker Request and Documents]:::process
    E{Scarcity Validation by JE or SE}:::decision
    F[Notify Applicant and Close]:::error
    G[System Calculates Tankers and Charges]:::process
    H{Payment Processing}:::decision
    I[Retry or Cancel Payment]:::error
    J[Configure Schedule and Allocate]:::process
    K[Generate Trip-Specific QR Code]:::process

    %% NEW: Independent Vendor Node
    VR[Vendor Registration]:::vendor

    %% Row 2: Vendor Assignment & Filling
    L{Vendor Accepts Request?}:::decision
    M[Re-assign Vendor]:::error
    
    %% UPDATED: MOH Integration and Decision
    N[Access MOH Dept for Verification]:::hardware
    N_Dec{Verification Status}:::decision
    
    O[Suspend Vendor Assignment]:::error
    P[Vendor Dispatched to Filling Point]:::process
    Q[Authorized Staff Scans QR]:::process
    R{QR Validation}:::decision
    S[Block Filling]:::error
    T[Manual Upload of Tanker Image or Number]:::process
    U[Water Flow Meter Integration]:::hardware
    V{Volume Check}:::decision
    W[Halt Flow and Alert]:::error
    X[System Logs Fill Time and Auto-Sends OTP]:::process

    %% Row 3: Transit, Delivery & Audit
    Y[Tanker in Transit]:::process
    Z[GPS or VTS Tracking]:::hardware
    AA[Vendor Arrives at Consumer]:::process
    AB{OTP Validation}:::decision
    AC[Log Failed or Partial Delivery]:::process
    AD{Delivery Resolution Rule}:::decision
    AE[Admin Resolve]:::process
    AF[Status Partial or Failed Kept Open]:::error
    AG[System Records Delivery Timestamp]:::process
    AH[Auto-Update Delivery Count]:::process
    AI([Transaction Closed and Audit Logged]):::startEnd

    %% Connections - Top Row
    A --> B
    B -- Pending Dues or Active Request --> C
    B -- Clear --> D
    D --> E
    E -- Rejected --> F
    E -- Approved --> G
    G --> H
    H -- Failed or Timeout --> I
    I --> H
    H -- Success --> J
    J --> K
    K --> L

    %% Connections - Vendor Logic
    VR -- Active Pool --> L
    L -- No or Timeout --> M
    M --> L
    L -- Yes --> N
    
    %% Connections - MOH Integration
    N --> N_Dec
    N_Dec -- Failed Health or Regulatory Check --> O
    N_Dec -- Pass --> P

    %% Connections - Filling
    P --> Q
    Q --> R
    R -- Expired, Wrong Trip, or Invalid --> S
    R -- Valid --> T
    T --> U
    U --> V
    V -- Overfill Attempt --> W
    V -- Correct Volume --> X

    %% Connections - Delivery
    X --> Y
    Y --> Z
    Z --> AA
    AA --> AB
    
    %% OTP Failure Path
    AB -- Wrong OTP or Unavailable --> AC
    AC --> AD
    AD -- Admin Resolve --> AE
    AD -- Auto-Close Rules --> AF
    
    %% OTP Success Path
    AB -- Valid OTP --> AG
    AG --> AH
    AH --> AI
