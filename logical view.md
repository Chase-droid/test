flowchart LR
  subgraph Clients
    A[Job Seeker App/Web]
    B[Recruiter Portal]
  end

  subgraph Identity & Profiles
    IAM[Identity & Access Manager<br/>(OAuth/OIDC, RBAC)]
    PROF[Profile Service<br/>(User, Connected Accounts)]
  end

  subgraph Data Ingestion
    ORCH[Ingestion Orchestrator]
    NORM[Data Normalizer<br/>(unify schema)]
    Q[Event/Task Queue]
    GH[(GitHub Connector)]
    UP[(Upwork Connector)]
    COU[(Coursera Connector)]
    OTH[(Other Providers...)]
  end

  subgraph AI & Evidence
    EVID[Evidence Store<br/>(Projects/Commits/Badges)]
    AI[AI Narrative Generator<br/>(LLM + templates + guardrails)]
    FEED[User Feedback & Review]
  end

  subgraph Credentialing
    VER[Evidence Verifier<br/>(cross‑check rules)]
    ISSUER[Credential Issuer<br/>(hash+sign)]
    CHAIN[(Blockchain Adapter)]
  end

  subgraph Verification API
    VAPI[Verification API<br/>(claim lookup & proofs)]
  end

  subgraph Shared
    CFG[(Config/Secrets)]
    LOG[(Audit & Logs)]
  end

  %% client -> identity
  A -->|login/consent| IAM
  IAM --> PROF
  A -->|connect account| PROF
  PROF --> ORCH

  %% ingestion
  ORCH --> Q
  Q --> GH
  Q --> UP
  Q --> COU
  Q --> OTH
  GH --> NORM
  UP --> NORM
  COU --> NORM
  OTH --> NORM
  NORM --> EVID

  %% AI pipeline
  EVID --> AI
  AI --> FEED
  FEED --> AI
  FEED --> EVID

  %% credentialing
  EVID --> VER
  VER --> ISSUER
  ISSUER --> CHAIN

  %% recruiter
  B -->|verify claim| VAPI
  VAPI --> CHAIN
  VAPI --> EVID

  %% cross-cutting
  IAM --- LOG
  ORCH --- LOG
  VER --- LOG
  VAPI --- LOG
  CFG --- IAM
  CFG --- ORCH
  CFG --- ISSUER
  CFG --- VAPI
