# SentinelVault

SentinelVault is a secure digital document management system for legal and investigation records. It combines a Python backend, a React + TypeScript frontend, tamper-evident audit logging, threshold custody for sealed evidence, searchable encrypted metadata, and certificate generation for court-style submissions.

This repository contains:

- `server/` for the FastAPI backend, crypto orchestration, database models, seeding, CLI demo, and Streamlit dashboard
- `client/` for the React frontend

## What problem it solves

Legal and investigation teams need a way to store documents that are:

- access-controlled by role and case assignment
- protected from unauthorized reading even if application checks fail
- traceable through a tamper-evident audit trail
- defensible in front of judges, prosecutors, and auditors
- suitable for sealed evidence workflows where multiple custodians must approve release

SentinelVault addresses those needs by combining encryption, role-based access control, hash-chained audit logs, Merkle-root anchoring, Shamir threshold custody, and certificate export.

## Why this design

The project is built around defense in depth:

- role and case permissions are checked in the service layer
- document payloads are encrypted with a unique data-encryption key
- non-sealed documents require a wrapped key for the specific user before decryption is possible
- sealed documents can only be opened after the required number of custodians approve
- audit entries are hash-chained and can be anchored into signed blocks
- document integrity can be re-verified later, including after tamper simulation

This makes the system useful for demonstrations because it shows both the workflow and the cryptographic guarantees behind it.

## Main features

- Login with JWT authentication
- Role-based and case-based access control
- Document upload, retrieval, listing, and search
- AES-GCM encryption for document blobs
- RSA-wrapped document keys for authorized users
- Sealed document custody using Shamir’s Secret Sharing
- Hash-chained audit trail
- Merkle-based anchoring of audit batches
- Integrity verification and tamper demo
- Hash certificate generation for submissions
- React frontend and Streamlit demo UI

## Project layout

```text
sentinelvault/
├── client/                 # React frontend
├── server/                 # FastAPI backend + service layer + demo scripts
├── README.md               # This file
└── PROJECT_FLOW_AND_VIDEO_SCRIPT.md
```

Inside `server/` the key files are:

- `api/main.py` for REST endpoints
- `api/schemas.py` for request and response models
- `core/services.py` for the main orchestration logic
- `core/access_control.py` for RBAC and case scoping
- `core/crypto_utils.py` for cryptographic primitives
- `core/storage.py` for encrypted blob storage
- `core/hashchain.py` and `core/blockchain.py` for audit integrity
- `core/secret_sharing.py` for custody release
- `core/certificate.py` for certificate generation
- `seed.py` for demo data
- `cli_demo.py` for the terminal walkthrough

## Quick start

### Backend

```bash
cd server
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
python seed.py
uvicorn api.main:app --reload --port 8000
```

### Frontend

```bash
cd client
npm install
npm run dev
```

The frontend runs on `http://localhost:5173` and the backend runs on `http://localhost:8000`.

## Demo accounts

Password for all demo users: `password123`

| Username | Role |
|---|---|
| `admin` | Admin |
| `io_sharma` | InvestigatingOfficer |
| `io_verma` | InvestigatingOfficer |
| `fa_patel` | ForensicAnalyst |
| `forensic_iyer` | ForensicAnalyst |
| `prosecutor_rao` | Prosecutor |
| `judge_mehta` | Judge |
| `clerk_das` | Clerk |

## How to demo it

1. Run `python seed.py` from `server/`.
2. Start the API with `uvicorn api.main:app --reload --port 8000`.
3. Start the frontend with `npm run dev` from `client/`.
4. Log in as a demo user.
5. Upload a document and view the audit trail.
6. Anchor pending entries.
7. Run the tamper demo and verify integrity again.
8. Open the sealed custody flow and show threshold approval.
9. Generate the certificate PDF.

## Run the scripted terminal demo

```bash
cd server
python cli_demo.py
```

This is the fastest way to show the full end-to-end flow without the UI.

## Testing

```bash
cd server
pytest tests/ -v
```
