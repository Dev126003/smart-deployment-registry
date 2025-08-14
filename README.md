# Smart Deployment Registry (SDR) – Blockchain-Powered Deployment Version Control

##  Overview

**Smart Deployment Registry (SDR)** is a Spring Boot + Blockchain based backend solution designed to securely track deployment versions, prevent tampering, and provide full auditability for create, update, and delete operations.

The system integrates:
- **Blockchain (Ethereum + Solidity)** for immutability
- **AES encryption** for data security
- **Role-based access control** via OpenZeppelin for authorization
- **Spring Boot layered architecture** for clean and modular backend design

---

##  Problem Statement

In traditional DevOps workflows:

  Issue                Description                                           

  Centralized logs     Deployment logs are centralized and can be altered or deleted 
  Version tracking     Depends on CI/CD logs or manual updates              
  Auditing             Slow and not tamper-proof                             

---

##  SDR Solution

 Solution Step       Description 

 AES Encryption       Encrypt deployment files before processing 
 SHA-256 Hashing      Generate unique version identifier for files 
 Blockchain Storage   Store hashes and metadata on Ethereum using Solidity smart contracts 
 Role-Based Access    Admin/Writer roles via OpenZeppelin access control 
 REST APIs            CRUD operations tested in Postman 

---

##  Tech Stack & Roles

| Technology      | Role in SDR |
|-----------------|-------------|
| Spring Boot     | Core backend framework handling API endpoints, service logic, and blockchain integration |
| Java            | Primary programming language for backend and blockchain interaction logic |
| Solidity        | Smart contract language defining storage, update, and retrieval rules for deployment metadata |
| Web3j           | Java library to interact with Ethereum blockchain from Spring Boot service layer |
| Ganache         | Local Ethereum blockchain for testing smart contracts without gas costs |
| AES Encryption  | Secures file content before hashing and blockchain storage |
| SHA-256 Hashing | Generates unique fingerprint for every deployment file to track versions |
| OpenZeppelin    | Provides pre-built smart contract libraries for role-based access control |
| Postman         | API testing tool to verify backend endpoints and workflows |

---

##  System Architecture

### Spring Boot Layered Flow

| Layer                  | Responsibility |
|------------------------|----------------|
| Controller Layer       | Handles API requests from Postman or frontend (e.g., `/files/create`, `/files/update`) |
| Service Layer          | Encrypts file with AES, generates SHA-256 hash, calls blockchain service to store data |
| Blockchain Service     | Uses Web3j to connect to Ganache or live Ethereum network |
| Repository Layer       | (Optional) Stores temporary metadata in local DB for quick retrieval (off-chain) |
| Smart Contract Layer   | Enforces role-based access (Admin, Writer) and CRUD rules for deployment metadata |
| Blockchain Network     | Stores hashes and metadata permanently |

### High-Level Flow 


    A[User / Frontend / Postman] --> B[Spring Boot Controller]
    B --> C[Service Layer]
    C --> D[Encryption & Hashing]
    D --> E[Blockchain Service via Web3j]
    E --> F[Smart Contract on Ganache]
    C --> G[Repository Layer (Optional)]
    F --> H[Immutable Deployment Records]
 How SDR Works (Step-by-Step)
Step	Action
1	File Upload: API receives file via POST request (curl -X POST "http://localhost:8080/api/files/create" -F "file=@deployment-config.json")
2	AES Encryption: File content encrypted using secret key stored securely
3	Hash Generation: SHA-256 hash of encrypted file creates unique fingerprint
4	Blockchain Storage: Call smart contract function storeDeployment(fileHash, metadata); metadata includes timestamp, filename, version number, deployer address; only Admin/Writer can store/update
5	Retrieval & Verification: API fetches data via getDeployment(versionId); compare hash of downloaded file for integrity
6	Address Verification: Web3j checks sender's private key and Ethereum address; smart contract enforces role-based authorization

 Role-Based Access (OpenZeppelin)
Role	Permissions
Admin	Grant/revoke roles, delete records, manage contract
Writer	Create and update deployment metadata
Default	Read-only access for verification

Example Solidity snippet:

solidity
Copy
Edit
bytes32 public constant WRITER_ROLE = keccak256("WRITER_ROLE");

constructor() {
    _grantRole(DEFAULT_ADMIN_ROLE, msg.sender);
    _grantRole(WRITER_ROLE, msg.sender);
}

function storeDeployment(bytes32 fileHash, string memory metadata) public onlyRole(WRITER_ROLE) {
    deployments[fileHash] = metadata;
}
 Example API Endpoints
Endpoint	Method	Description
/api/files/create	POST	Create new deployment file entry
/api/files/{fileId}	POST	Fetch deployment metadata
/api/files/update/{fileId}	POST	Update deployment metadata
/api/files/{fileId}	DELETE	POST a deployment record

 Security Layers
AES Encryption – Protects file before hashing

SHA-256 Hashing – Ensures tamper detection

Blockchain Storage – Guarantees immutability

Role-Based Access Control – Restricts modification rights

Ganache Blcoks-:
<img width="1192" height="402" alt="image" src="https://github.com/user-attachments/assets/7763feab-b020-43b7-8c5a-f0b88e6935cd" />





