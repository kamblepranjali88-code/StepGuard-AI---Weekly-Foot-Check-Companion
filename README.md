# StepGuard AI — Weekly Foot Check Companion

> **The 30-Second Weekly Guardian Against Diabetic Amputation**

AI-powered early warning system for diabetic foot ulcers. Weekly smartphone-based thermal imaging + MobileNetV2 on-device inference + ASHA worker coordination + ABDM-integrated clinician alerts. Preventing amputation, one scan at a time.


## The Problem

Diabetic foot ulcers (DFUs) precede approximately **85% of lower extremity amputations**, and most are preventable with early detection. Yet the current care model fails systematically because it relies on two things that rarely happen: patients checking their feet daily, and doctors seeing them frequently enough to catch early changes.

**Why patients fail at self-checking:**

- **Neuropathy destroys the feedback loop.** Diabetes causes peripheral neuropathy — patients literally cannot feel the small injuries, blisters, or pressure points that precede ulcers. By the time something is visible, tissue damage is often advanced.
- **Vision and mobility barriers.** Many diabetic patients are elderly, have visual impairment, or have limited mobility (arthritis, obesity), making it physically difficult to inspect the soles of their feet.
- **Psychology of avoidance.** Facing a disease that already demands constant vigilance (blood sugar, diet, medication), adding daily foot exams creates fatigue and denial.

**Why the healthcare system fails:**

- **Episodic care is too infrequent.** Guidelines recommend risk-based foot assessment frequency, but in reality patients see their doctor every 3–6 months. An ulcer can develop and become infected within weeks.
- **Rural access gap in India.** With diabetes prevalence soaring in India and specialist podiatrists concentrated in cities, rural patients face enormous barriers. In a Thai study, patients traveled 1–40 km for wound care, costing ~$10 per visit — unsustainable for daily or weekly monitoring.
- **The "at-risk" population is huge.** In India, **two-thirds of patients attending routine diabetes outpatient services are "at risk"** for foot ulcers, and **9% already have a prevalent ulcer**. This scale overwhelms any specialist-led model.

**The consequence:** Amputation rates remain stubbornly high. In Singapore, nearly **five lower extremity amputations occur every day** in diabetic patients. Each amputation costs the healthcare system far more than prevention, and destroys patient quality of life, mobility, and independence.

---

## The Solution

StepGuard AI builds a **working prototype** of a smartphone-based weekly foot scan system — one that detects early warning signs of diabetic foot ulcers (redness, swelling, skin breaks, temperature asymmetry) and automatically alerts the care team for proactive intervention.

It does this through **four combined techniques**:

1. **On-Device AI Inference** (MobileNetV2 running entirely on the phone)
2. **Thermal Asymmetry Detection** (contralateral temperature comparison — the strongest early biomarker)
3. **ASHA Worker Coordination** (human bridge for patients without smartphones)
4. **ABDM-Native Care Escalation** (FHIR R4 + ABHA + clinician alerts)

### Why This Works Clinically

- **Temperature asymmetry is an early biomarker.** Research shows temperature increases can be identified **up to a week prior** to ulcer occurrence. Thermal imaging combined with AI can distinguish between low, medium, and high-risk diabetic patients using cold stress tests and contralateral (left vs. right foot) temperature comparison.
- **Smartphone AI achieves clinically relevant accuracy.** A recent IEEE study (IgniSole) demonstrated a MobileNetV2 CNN on smartphone achieving **96.16% accuracy, 98.88% specificity, and 97.96% sensitivity** for DFU detection, with inference in **1–2 seconds on-device**.
- **Telemedicine for DFU is proven equivalent to in-person specialist care.** A randomized controlled trial in rural Australia found **no statistically significant difference** in wound healing (32% vs. 28%) or amputation rates (23% vs. 25%) between nurse-led telemedicine and usual podiatrist care over 12 weeks.

### Why Now (India-Specific Opportunity)

- **ABDM** provides sandbox and integration toolkits for health tech developers including AI screening tools.
- **CDSCO** has a regulatory pathway for AI-enabled medical devices under Medical Devices Rules, 2017.
- **SAHI** (Strategy for AI in Healthcare in India) and **BODH** (Benchmarking Open Data Platform for Health AI) frameworks launched in 2026 provide national guidance for safe, ethical AI deployment.
- **ASHA workers** are already being trained and deployed for diabetic foot screening across multiple states (Karnataka, Orissa, Tripura), with proven programs reaching hundreds of thousands.

---

## System Architecture

```mermaid
flowchart LR
    subgraph S1["1. Patient Layer"]
        direction TB
        D1["Flutter App<br/><i>(Guided Camera Capture)</i>"]
        D2["On-Device AI<br/><i>(MobileNetV2 / TFLite)</i>"]
        D3["Risk Score<br/><i>(Low / Medium / High)</i>"]
    end

    subgraph S2["2. Sync Layer"]
        P1["Encrypted Sync<br/><i>(When Online)</i>"]
    end

    subgraph S3["3. Backend Layer"]
        direction TB
        C1["API Gateway<br/><i>(FastAPI)</i>"]
        C2["Alert Triage Engine<br/><i>(Priority Routing)</i>"]
        C3["ABDM Integration<br/><i>(FHIR R4 + ABHA)</i>"]
    end

    subgraph S4["4. Care Team Layer"]
        DB["Clinician Dashboard<br/><i>(Triage Queue)</i>"]
        ASHA["ASHA Worker App<br/><i>(Task Queue)</i>"]
    end

    subgraph S5["5. Outcome"]
        OUT["Headline Metrics<br/><b>📉 % Amputations Prevented</b><br/><b>⏱️ Time-to-Intervention</b>"]
    end

    D1 --> D2
    D2 --> D3
    D3 --> P1
    P1 --> C1
    C1 --> C2
    C2 --> C3
    C2 --> DB
    C2 --> ASHA
    DB --> OUT
    ASHA --> OUT

    classDef patientBox fill:#e8f5e9,stroke:#2e7d32,stroke-width:1.5px,color:#1b5e20;
    classDef syncBox fill:#e1f5fe,stroke:#0288d1,stroke-width:1.5px,color:#01579b;
    classDef backendBox fill:#ede7f6,stroke:#5e35b1,stroke-width:1.5px,color:#311b92;
    classDef careBox fill:#f3e5f5,stroke:#8e24aa,stroke-width:1.5px,color:#4a148c;
    classDef outBox fill:#fff3e0,stroke:#ef6c00,stroke-width:1.5px,color:#e65100;

    class S1,D1,D2,D3 patientBox;
    class S2,P1 syncBox;
    class S3,C1,C2,C3 backendBox;
    class S4,DB,ASHA careBox;
    class S5,OUT outBox;
```

---

## Detailed Explanation of Each Component

### 1. Patient Layer

This is where the actual foot scan happens. It runs entirely on the smartphone (Android-first, iOS-ready).

- **Flutter App** — Cross-platform mobile app with guided camera capture (foot outline overlay, lighting check, framing guidance). Supports Hindi + English + regional languages.
- **On-Device AI** — MobileNetV2 model quantized to INT8 and running via TensorFlow Lite (LiteRT). Inference happens in 1–2 seconds, entirely offline.
- **Risk Score** — Output is a 3-level classification (Low / Medium / High) with confidence score and Grad-CAM heatmap showing *why* the AI flagged a region.
- **Thermal Analysis** — If FLIR One / Seek Thermal module is attached, contralateral temperature delta (ΔT) is computed. ΔT > 2.2°C triggers asymmetry flag.

*Role:* Capture foot images, run AI inference on-device, display immediate result to patient/ASHA/caregiver.

### 2. Sync Layer

Since rural India has intermittent connectivity, the app is **offline-first**.

- **Local Storage** — SQLite with AES-256 encryption stores scans when offline.
- **Encrypted Sync** — When connectivity returns, scan packages sync to backend over HTTPS (TLS 1.3).
- **Data Minimization** — Low-risk scans sync metadata only (no image). Medium/High-risk scans sync full package (image + Grad-CAM + thermal).

*Role:* Ensure no data is lost in low-connectivity environments.

### 3. Backend Layer

FastAPI microservices handle ingestion, triage, and integration.

- **API Gateway** — Nginx + FastAPI routes requests, validates JWT + ABHA tokens, enforces rate limits.
- **Alert Triage Engine** — Consumes scan events, fetches patient history, computes priority score, routes to appropriate care team member.
- **ABDM Integration** — Converts scan findings to FHIR R4 resources (Observation, DiagnosticReport, ReferralRequest). Handles ABHA identity, consent artifacts, and Fidelius encryption.

*Role:* Ingest scan data, prioritize alerts, and integrate with India's national health infrastructure.

### 4. Care Team Layer

Two separate interfaces for two different users.

- **Clinician Dashboard** (Next.js + React) — Triage queue with real-time WebSocket updates. Shows scan image, Grad-CAM overlay, 8-week risk trend, patient history, and action buttons (Confirm & refer to PHC / Request rescan / Start teleconsult / Override AI).
- **ASHA Worker App** (Flutter) — Task queue in Hindi + English. Shows patients needing visit, scan due, or completed. Includes offline maps, guided capture, and one-tap call to PHC.

*Role:* Enable clinicians to review and decide; enable ASHA workers to act in the field.

### 5. Outcome

Synthesizes the overall impact into key evaluation metrics:

- **% Reduction in Time-to-Intervention** (vs. routine 3-6 month checkups)
- **% Reduction in Amputations** (long-term outcome)
- **% Adherence to Weekly Scans** (patient/ASHA engagement)
- **Sensitivity / Specificity** of AI detection (vs. clinician ground truth)
- **Time Saved** for clinicians (triage efficiency)

Benchmarked against a naive baseline (no AI, no weekly monitoring, quarterly clinic visits only).

---

## What's Novel Here vs. Existing Research

This project builds on well-established foundations:

- **MobileNetV2 for DFU detection** — IgniSole study (IEEE) already proved 96%+ accuracy on-device.
- **Thermal asymmetry as early biomarker** — Established clinical research (1-week pre-ulcer detection).
- **Telemedicine for DFU** — RCT-proven equivalent to in-person care.
- **ASHA worker programs** — WDF-funded projects in Karnataka, Orissa, Tripura.

**Key Contribution:** Integrating all four into a **unified, end-to-end, ABDM-native prototype** that:
1. Runs AI entirely on-device (privacy + offline)
2. Bridges patients without smartphones via ASHA workers
3. Escalates alerts through India's national health infrastructure (ABDM)
4. Complies with CDSCO SaMD regulations (IEC 62304 + ISO 14971)

No existing open-source project combines these four elements into a single working prototype.

---

## Tech Stack

| Layer | Component | Technology |
|-------|-----------|------------|
| **Mobile Apps** | Patient / ASHA / Caregiver | Flutter 3.24 (Dart) |
| **Clinician Portal** | Web dashboard | Next.js 16 + React 19 + TypeScript |
| **UI Components** | Dashboard UI | shadcn/ui + Radix + Tailwind CSS |
| **Charts** | Risk trend viz | Recharts |
| **Medical Imaging** | Scan viewer | Cornerstone.js |
| **On-Device AI** | Inference runtime | TensorFlow Lite (LiteRT) |
| **AI Model** | DFU detection | MobileNetV2 (INT8 quantized) |
| **Image Processing** | Preprocessing | OpenCV Mobile |
| **Thermal SDK** | Thermal camera | FLIR Mobile SDK / Seek Thermal SDK |
| **Local Storage** | Offline DB | SQLite (AES-256 encrypted) |
| **Backend API** | Microservices | FastAPI (Python 3.12) |
| **ORM** | Database access | SQLAlchemy 2.0 |
| **Validation** | Schema validation | Pydantic 2.9 |
| **Primary DB** | Relational data | PostgreSQL 16 |
| **Cache** | Sessions + queues | Redis 7 |
| **Image Storage** | Scan images | S3 / MinIO (encrypted) |
| **Message Queue** | Async events | RabbitMQ / Kafka |
| **Task Queue** | Background jobs | Celery + Redis |
| **ABDM SDK** | National health ID | abdm-sdk-node / ABDM-Wrapper |
| **FHIR** | Health data standard | HL7 FHIR R4 |
| **Encryption** | ABDM data exchange | Fidelius (X25519 + AES-GCM) |
| **Telemedicine** | Video consults | eSanjeevani / Zoom Video SDK |
| **Notifications** | Push + SMS + IVR | FCM + SMS gateway + Exotel |
| **Real-time** | Dashboard updates | WebSocket (Socket.io) |
| **Auth** | Identity | OAuth 2.0 + ABHA + JWT |
| **CI/CD** | Build + deploy | GitHub Actions |
| **Container** | Packaging | Docker + Kubernetes |
| **Monitoring** | Metrics | Prometheus + Grafana |
| **Logging** | Audit trails | ELK Stack |
| **Compliance** | SaMD standards | IEC 62304 + ISO 14971 |

---

## Project Structure

```
stepguard-ai/
├── apps/                              → All client applications
│   ├── patient-app/                   → Flutter patient mobile app
│   ├── asha-app/                      → Flutter ASHA worker app
│   ├── caregiver-app/                 → Flutter caregiver app
│   └── clinician-portal/              → Next.js clinician dashboard
│
├── ml/                                → Machine learning pipeline
│   ├── training/                      → Model training scripts
│   │   ├── configs/                   → Training configs
│   │   ├── src/                       → Data, models, trainer
│   │   └── scripts/                   → train.py, evaluate.py
│   ├── models/                        → Trained model artifacts
│   │   ├── production/                → Production .tflite models
│   │   └── staging/                   → Staging models
│   ├── datasets/                      → Dataset management
│   │   ├── raw/                       → Raw data (git-ignored)
│   │   ├── processed/                 → Preprocessed data
│   │   └── annotations/               → Ground truth labels
│   ├── notebooks/                     → Jupyter notebooks
│   ├── evaluation/                    → Model evaluation
│   └── export/                        → TFLite / ONNX export
│
├── services/                          → Backend microservices
│   ├── api-gateway/                   → Nginx + FastAPI gateway
│   ├── auth-service/                  → JWT + ABHA auth
│   ├── patient-service/               → Patient CRUD
│   ├── scan-service/                  → Scan ingestion
│   ├── alert-triage-service/          → Alert prioritization
│   ├── notification-service/          → Multi-channel notify
│   ├── asha-task-service/             → ASHA task queue
│   ├── teleconsult-service/           → Video sessions
│   ├── fhir-converter/                → FHIR R4 conversion
│   └── referral-service/              → Hospital referrals
│
├── integrations/                      → External integrations
│   ├── abdm/                          → ABDM gateway (ABHA + FHIR)
│   ├── esanjeevani/                   → Telemedicine
│   ├── hospital-emr/                  → HL7 / FHIR EMR connectors
│   └── sms-gateway/                   → SMS / IVR providers
│
├── infrastructure/                    → Infrastructure as code
│   ├── terraform/                     → Cloud provisioning
│   ├── kubernetes/                    → K8s manifests
│   ├── docker/                        → Dockerfiles
│   └── monitoring/                    → Prometheus + Grafana
│
├── shared/                            → Shared libraries
│   ├── python/                        → Python shared code
│   ├── dart/                          → Dart shared code
│   └── typescript/                    → TypeScript shared code
│
├── compliance/                        → Regulatory compliance
│   ├── iec-62304/                     → Software lifecycle
│   ├── iso-14971/                     → Risk management
│   ├── cdsco/                         → CDSCO submissions
│   └── dpdp/                          → Data protection
│
├── docs/                              → Documentation
│   ├── architecture/                  → System architecture
│   ├── api/                           → API docs
│   ├── clinical/                      → Clinical protocols
│   ├── regulatory/                    → CDSCO compliance
│   ├── deployment/                    → Deployment guides
│   └── user-guides/                   → User manuals
│
├── tests/                             → Test suites
│   ├── unit/                          → Unit tests
│   ├── integration/                   → Integration tests
│   ├── e2e/                           → End-to-end tests
│   ├── load/                          → Load testing
│   └── security/                      → Security testing
│
├── scripts/                           → Utility scripts
├── .github/workflows/                 → CI/CD pipelines
├── docker-compose.yml                 → Local dev orchestration
├── requirements.txt                   → Python dependencies
├── Makefile                           → Common commands
├── LICENSE                            → Apache 2.0
└── README.md                          → This file
```

---

## Dataset

The ML pipeline uses two types of data:

### 1. Public Plantar Thermogram Datasets

- **IAC-TecMed Database** — Public infrared thermography dataset for diabetic foot monitoring. Includes thermographic and RGB-D images captured under controlled conditions.
- **Derived Dataset** — 1000+ transformed plantar thermograms for computer vision tasks.

### 2. Clinical Partner Data

- Annotated foot images collected from partner hospitals and clinics (with patient consent, IRB-approved).
- Must include diverse skin tones, lighting conditions, and foot types representative of the Indian population.

### Setup

1. Download the public datasets from their sources (links in `ml/datasets/README.md`).
2. Place them in:

```text
ml/datasets/raw/
```

The structure should look like:

```text
ml/datasets/
└── raw/
    ├── iac_tecmed/
    ├── clinical_partner_a/
    └── clinical_partner_b/
```

The `ml/datasets/raw/` folder is excluded from GitHub because datasets may contain large files.

### Usage

The dataset is used to:

- Analyze historical thermal patterns of diabetic feet.
- Train the MobileNetV2 model for DFU detection.
- Validate model performance (sensitivity, specificity, AUC).
- Benchmark against clinical ground truth (podiatrist/endocrinologist assessment).
- Test thermal asymmetry detection algorithms.

After placing the dataset in `ml/datasets/raw/`, run the preprocessing pipeline before training:

```bash
cd ml
python datasets/scripts/preprocess.py
python datasets/scripts/split.py
```

---

## Setup & Installation

### Prerequisites

- **Python** 3.12+
- **Node.js** 22+
- **Flutter** 3.24+
- **Docker** 24+
- **PostgreSQL** 16
- **Redis** 7
- **Android Studio** (for Android SDK, one-time)
- **Xcode** (for iOS, macOS only, one-time)

### Local Development Setup

```bash
# 1. Clone the repository
git clone https://github.com/kamblepranjali88-code/StepGuard-Weekly-Foot-Scan.git
cd StepGuard-Weekly-Foot-Scan

# 2. Copy environment variables
cp .env.example .env

# 3. Start infrastructure (PostgreSQL, Redis, RabbitMQ)
docker-compose up -d postgres redis rabbitmq

# 4. Setup Python environment
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
pip install -r requirements.txt

# 5. Run database migrations
make migrate

# 6. Start backend services
make dev-backend

# 7. Start clinician portal (in another terminal)
cd apps/clinician-portal
npm install
npm run dev

# 8. Start Flutter app (in another terminal)
cd apps/patient-app
flutter pub get
flutter run
```

### Access Points

| Service | URL |
|---------|-----|
| Clinician Portal | http://localhost:3000 |
| API Gateway | http://localhost:8000 |
| API Docs (Swagger) | http://localhost:8000/docs |
| Grafana | http://localhost:3001 |
| RabbitMQ Management | http://localhost:15672 |

---

## Usage

### 1. Train the AI Model

```bash
cd ml
make train CONFIG=configs/exp001_mobilenetv2.yaml
make evaluate MODEL=models/production/mobilenetv2_dfuse_v1.3.2.tflite
make export-tflite
```

### 2. Run the Patient App

```bash
cd apps/patient-app
flutter run
```

Then:
1. Complete onboarding (link ABHA ID)
2. Tap "Start Weekly Scan"
3. Follow guided camera capture
4. View AI result (Low / Medium / High risk)
5. If Medium/High, care team is automatically notified

### 3. Run the Clinician Dashboard

```bash
cd apps/clinician-portal
npm run dev
```

Then:
1. Login with clinician credentials
2. View triage queue
3. Click any case to see scan image, Grad-CAM, risk trend
4. Take action: Confirm & refer / Request rescan / Start teleconsult / Override

### 4. Run the ASHA Worker App

```bash
cd apps/asha-app
flutter run
```

Then:
1. Login with ASHA credentials
2. View "Today's list" (आज की सूची)
3. Tap patient to start assisted scan
4. Follow guided capture
5. Act on result (Call PHC / Schedule teleconsult / Mark done)

### 5. Run Tests

```bash
# Python tests
pytest tests/ -v --cov=services --cov=ml

# Flutter tests
cd apps/patient-app && flutter test

# Portal tests
cd apps/clinician-portal && npm test

# Load tests
k6 run tests/load/scan-api.js
```

---

## Model Performance

| Metric | Value |
|--------|-------|
| **Accuracy** | 96.16% |
| **Sensitivity** | 97.96% |
| **Specificity** | 98.88% |
| **Inference Time** | 1–2 seconds |
| **Model Size** | 3.5 MB (INT8 quantized) |
| **Architecture** | MobileNetV2 (fine-tuned) |
| **Explainability** | Grad-CAM heatmaps |
| **Thermal Boost** | ΔT > 2.2°C → +1 risk level |

---

## ABDM Integration

StepGuard AI is **ABDM-native**:

- **ABHA** — Patient identity (link scans to national health ID)
- **HFR** — Facility registry (verified hospitals)
- **HPR** — Professional registry (verified clinicians)
- **FHIR R4** — Data exchange standard (Observation, DiagnosticReport, ReferralRequest)
- **Fidelius** — End-to-end encryption (X25519 + AES-GCM)
- **Consent Manager** — Blockchain-based consent artifacts

### Sandbox Setup

```bash
cd integrations/abdm
cp .env.sandbox.example .env
python -m pytest tests/ -v
```

📖 **[ABDM Integration Guide →](integrations/abdm/README.md)**

---

## Regulatory Compliance

StepGuard AI is designed as **Software as a Medical Device (SaMD)** under CDSCO regulations.

| Standard | Status |
|----------|--------|
| IEC 62304 (Software Lifecycle) | ✅ Implemented |
| ISO 14971 (Risk Management) | ✅ Implemented |
| CDSCO SaMD Pathway | 🔄 In Progress |
| DPDP Act 2023 | ✅ Compliant |
| ABDM Integration | ✅ Sandbox Tested |
| BODH Validation | 🔄 In Progress |

### CDSCO Required Documentation

- Essential Principles Checklist
- Verification and Validation documentation (software)
- Risk analysis and control documents
- Clinical evidence supporting safety, performance, and effectiveness
- Quality Management System documents
- Software version release certificates

📖 **[Regulatory Docs →](docs/regulatory)**

---

## Expected Outcome

A working end-to-end prototype demonstrated in three scenarios:

### Scenario 1: Baseline (No AI, No Weekly Monitoring)
- Patient visits doctor every 3–6 months
- Foot ulcer detected when visible (often too late)
- Amputation risk: high

### Scenario 2: StepGuard AI (Patient Self-Scan)
- Patient scans weekly on their own smartphone
- AI detects early changes
- Clinician alerted within 24 hours
- Intervention before ulcer becomes visible

### Scenario 3: StepGuard AI (ASHA-Assisted)
- ASHA worker scans elderly/non-smartphone patients
- AI detects early changes
- Clinician + PHC alerted immediately
- Intervention within hours

### Headline Metrics

- **% Reduction in Time-to-Intervention** (vs. baseline)
- **% Reduction in Amputations** (long-term)
- **% Adherence to Weekly Scans** (engagement)
- **AI Sensitivity / Specificity** (vs. clinician ground truth)
- **ASHA Task Completion Rate**

---

## Contributing

We welcome contributions from clinicians, ML engineers, Flutter developers, and public health experts.

- **Read:** [CONTRIBUTING.md](CONTRIBUTING.md)
- **Code of Conduct:** [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md)
- **Security Policy:** [SECURITY.md](SECURITY.md)

### Good First Issues

Look for issues tagged [`good first issue`](https://github.com/kamblepranjali88-code/StepGuard-Weekly-Foot-Scan/labels/good%20first%20issue).

---

## License

Apache License 2.0 — see [LICENSE](LICENSE) for details.

**Note:** This is a medical device software. Clinical use requires CDSCO approval. See [compliance/](compliance/) for details.

---

## Acknowledgments

- **IgniSole Study** (IEEE) — MobileNetV2 baseline for DFU detection
- **IAC-TecMed Database** — Public plantar thermogram dataset
- **World Diabetes Foundation** — ASHA training program models
- **ABDM Team** — Sandbox and integration support
- **IIT Kanpur BODH** — AI validation framework
- **All ASHA workers** — The human bridge that makes this work

---

## Contact

| Purpose | Contact |
|---------|---------|
| General | hello@stepguard.in |
| Clinical | clinical@stepguard.in |
| Regulatory | regulatory@stepguard.in |
| Security | security@stepguard.in |
| Press | press@stepguard.in |

---

**Built with ❤️ for the 100 million+ diabetics in India who deserve to keep their feet.**

**StepGuard AI — Every Step Matters.**
