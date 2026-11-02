<p align="center">
  <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/dot-net/dot-net-original.svg" alt="ASP.NET Core Web API" width="70" height="70" />
</p>

<h1 align="center">Employee Performance Evaluation System - RESTful Web API (360° & AHP)</h1>

<p align="center">
  <b>High-performance ASP.NET Core 8 Web API backend for employee performance evaluation using 360-Degree Feedback and Analytical Hierarchy Process (AHP) algorithm, featuring pairwise comparison matrices, consistency ratio validation, JWT authentication, and SQL Server EF Core.</b>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/.NET-8.0-512BD4?style=flat&logo=dotnet&logoColor=white" alt=".NET 8" />
  <img src="https://img.shields.io/badge/C%23-12-239120?style=flat&logo=csharp&logoColor=white" alt="C#" />
  <img src="https://img.shields.io/badge/Entity%20Framework%20Core-8.0-blue?style=flat" alt="EF Core 8" />
  <img src="https://img.shields.io/badge/Microsoft%20SQL%20Server-CC292B?style=flat&logo=microsoftsqlserver&logoColor=white" alt="SQL Server" />
  <img src="https://img.shields.io/badge/JWT-Bearer%20Auth-black?style=flat&logo=jsonwebtokens" alt="JWT" />
  <img src="https://img.shields.io/badge/Swagger-OpenAPI-85EA2D?style=flat&logo=swagger&logoColor=black" alt="Swagger" />
</p>

---

## 📌 Overview

**Employee Performance Evaluation Web API** serves as the core computation engine and data service for the enterprise Decision Support System (Sistem Pendukung Keputusan / SPK). It powers employee performance appraisals by coupling **360-Degree multi-rater evaluation** with Thomas L. Saaty's **Analytical Hierarchy Process (AHP)** mathematical algorithm.

The API coordinates:
1. **Mathematical Weight Computation**: Automated pairwise comparison matrix calculations, eigenvector derivations, and mathematical consistency checks ($CR < 0.1$).
2. **Multi-Rater Assessment Aggregation**: Real-time scoring collation across Self-Assessment, Peer Reviews, and Manager Assessments.
3. **Data Security & Integration**: Role-Based Access Control (RBAC), secure JWT Bearer issuance, BCrypt password hashing, and MailKit SMTP OTP transmission.

---

## 🧮 Mathematical Engine & Algorithmic Formulations

### 1. Analytical Hierarchy Process (AHP)
The system calculates objective criteria weights based on Saaty's pairwise comparison methodology:

- **Pairwise Comparison Matrix ($A$):**
  Given $n$ criteria, matrix $A_{n \times n}$ satisfies reciprocity:
  $$a_{ij} > 0, \quad a_{ji} = \frac{1}{a_{ij}}, \quad a_{ii} = 1$$

- **Priority Vector / Eigenvector ($\mathbf{w}$):**
  Columns are normalized and averaged across rows to determine the criteria priority weights:
  $$w_i = \frac{1}{n} \sum_{j=1}^n \frac{a_{ij}}{\sum_{k=1}^n a_{kj}}, \quad \sum_{i=1}^n w_i = 1$$

- **Maximum Eigenvalue ($\lambda_{\max}$):**
  Computed via the dot product of the pairwise matrix and priority weight vector:
  $$\lambda_{\max} = \frac{1}{n} \sum_{i=1}^n \frac{(A \mathbf{w})_i}{w_i}$$

- **Consistency Validation ($CR < 0.1$):**
  To prevent contradictory comparisons, the Consistency Index ($CI$) and Consistency Ratio ($CR$) are strictly evaluated against Saaty's Random Index ($RI$):
  $$CI = \frac{\lambda_{\max} - n}{n - 1}, \qquad CR = \frac{CI}{RI}$$
  *(For $n = 7$ criteria, the system utilizes standard empirical $RI = 1.32$)*. Calculations are only accepted and persisted if $CR \le 0.10$.

### 2. 360-Degree Multi-Rater Synthesis
Individual final scores synthesize evaluations from multiple organizational perspectives:
- **Self Evaluation ($R_{\text{self}}$)**
- **Peer Evaluation ($R_{\text{peer}}$)**
- **Manager / Supervisor Evaluation ($R_{\text{manager}}$)**

The aggregated score for criterion $k$ is calculated with designated perspective weights:
$$R_k = \alpha R_{\text{self}, k} + \beta R_{\text{peer}, k} + \gamma R_{\text{manager}, k}$$

The overall composite performance score for employee $i$ is calculated by taking the weighted sum against the AHP criteria priority vector:
$$S_i = \sum_{k=1}^n w_k \cdot R_k$$

The resulting score $S_i$ is mapped automatically into performance grade categories (e.g., *Sangat Baik*, *Baik*, *Cukup*, *Kurang*).

---

## 🚀 Key API Endpoints & Modules

| Module / Controller | Route Prefix | Key Functionalities |
|---|---|---|
| **Authentication** | `/api/Auth` | User login, JWT token issuance, password reset, OTP verification |
| **Users & Staff** | `/api/Users` | Employee CRUD, role assignments, department info, avatar upload |
| **Master Kriteria** | `/api/MasterKriteria` | Performance criteria and sub-criteria parameter management |
| **Master Bobot AHP** | `/api/MasterBobotAHP` | Pairwise matrix submission, AHP calculation, $CR$ validation, weight storage |
| **Master Kategori** | `/api/MasterKategori` | Score grade thresholds, badges, and recommendation definitions |
| **Periode Penilaian** | `/api/Periode` | Assessment cycles, scheduling, and activation flags |
| **Penilaian 360** | `/api/Penilaian360` | Multi-rater questionnaire retrieval, response submission, score collation |
| **Hasil Penilaian** | `/api/HasilPenilaianSistem` | Composite final score generation, ranking tables, and performance recaps |
| **Menu Roles (RBAC)** | `/api/MenuRoles` | Dynamic user navigation menus mapped to specific role claims |
| **Roles** | `/api/Roles` | Role definitions (Administrator/HRD, Karyawan) |

---

## 🛠️ Technology Stack

| Layer | Technologies |
|---|---|
| **Framework** | ASP.NET Core 8.0 Web API |
| **Language** | C# 12 (.NET 8.0 SDK) |
| **Database & ORM** | Microsoft SQL Server, Entity Framework Core 8.0 |
| **Security & Cryptography** | JWT Bearer Authentication, BCrypt.Net-Next (Salted Hashing) |
| **Communication / SMTP** | MailKit 4.7.1, MimeKit (Automated OTP delivery) |
| **API Documentation** | Swashbuckle OpenAPI / Swagger UI |

---

## 📂 Project Architecture

```plaintext
RestAPISistemPenilaian/
├── Controllers/                  # REST API Controllers exposing HTTP endpoints
│   ├── AuthController.cs         # JWT Authentication, Password Reset, OTP verification
│   ├── HasilPenilaianSistemController.cs # Performance calculation & rankings
│   ├── MasterBobotAHPController.cs       # AHP pairwise comparison & consistency engine
│   ├── MasterKategoriController.cs      # Performance category classification
│   ├── MasterKriteriaController.cs      # Evaluation criteria definition
│   ├── MenuRolesController.cs    # Dynamic RBAC navigation permissions
│   ├── Penilaian360Controller.cs # 360-degree questionnaire & feedback submission
│   ├── PeriodeController.cs      # Assessment period management
│   ├── RolesController.cs        # Role definition management
│   └── UsersController.cs        # Employee master data & profiles
├── Data/                         # Entity Framework DbContext & Database configuration
├── Interfaces/                   # Abstractions & service contracts
├── Models/                       # Domain Entities and Data Transfer Objects (DTOs)
│   ├── DTOs/                     # Request and Response payloads
│   └── Entities/                 # SQL database table representations
├── Properties/                   # launchSettings.json (ports & profiles)
├── Repositories/                 # Data access layer repositories
├── Services/                     # Business logic and AHP mathematical algorithms
│   └── SistemPenilaianService.cs # Comprehensive AHP & 360-degree calculation engine
├── appsettings.json              # Connection strings, JWT secrets, SMTP configuration
├── Program.cs                    # ASP.NET Core 8 Web Host bootstrap & DI registrations
├── RestAPISistemPenilaian.csproj # Dependencies and NuGet packages
└── .gitignore                    # Git tracking ignore rules
```

---

## ⚙️ Quick Start & Setup Guide

### Prerequisites
- [.NET 8.0 SDK](https://dotnet.microsoft.com/download/dotnet/8.0)
- Microsoft SQL Server / LocalDB
- SMTP Mail Account (optional for OTP password recovery)

### 1. Database Configuration
Open `appsettings.json` and adjust your SQL Server connection string:
```json
"ConnectionStrings": {
  "DefaultConnection": "Server=(localdb)\\MSSQLLocalDB;Database=SistemPenilaian;Trusted_Connection=True;TrustServerCertificate=True;"
}
```

### 2. Run the Application
Navigate to the project root directory and start the service:
```bash
dotnet restore
dotnet run
```

By default, the service will run at:
- **API Base URL**: `http://localhost:21005`
- **Swagger Documentation**: `http://localhost:21005/swagger`

---

## 👤 Default Demo Credentials

| Role | Email | Password | Access Rights |
|---|---|---|---|
| **HRD / Admin** | `hrd@sistempenilaian.com` | `Admin123!` | Full System Administration & AHP Calibration |
| **Karyawan 1** | `budi@sistempenilaian.com` | `Karyawan123!` | Self & Peer 360 Assessments |
| **Karyawan 2** | `siti@sistempenilaian.com` | `Karyawan123!` | Self & Peer 360 Assessments |
| **Karyawan 3** | `ahmad@sistempenilaian.com` | `Karyawan123!` | Self & Peer 360 Assessments |

---

## 🔗 Related Repositories

- **Frontend Web Portal**: [employee-performance-360-ahp-web](https://github.com/tijartaufikrahman/employee-performance-360-ahp-web.git)
