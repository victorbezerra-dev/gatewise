# 🔐 GateWise
<p align="center">
  <img
    src="https://github.com/user-attachments/assets/5ba14e97-fe53-4762-add2-50edf0fc64db"
    alt="Rectangle"
    width="361"
    height="361"
  />
</p>



**GateWise** is a secure, modular, and auditable access control platform for university laboratories and restricted environments.

The project combines IoT, cryptography, modern backend architectures, and mobile applications to provide secure, traceable, and flexible access management, replacing physical keys and static badges with digitally signed, time-bounded authorizations.

Designed for academic laboratories, research facilities, and controlled environments, GateWise prioritizes security by design, auditability, and scalability, while remaining practical for real-world deployment.

##Hardware Image 
<img width="500" height="700" alt="WhatsApp Image 2026-06-12 at 18 38 12" src="https://github.com/user-attachments/assets/95471b7d-224e-42ec-bbdc-267c214fd004" />

## 🧠 Core Concept

GateWise enforces access decisions through cryptographically signed commands and a human-in-the-loop confirmation flow, ensuring that:

No lock opens without explicit authorization

Every access is traceable and auditable

Devices cannot be tricked by replay or forged commands

Backend rules, not devices, define who can open what and when

## 🔗 Project Structure

The GateWise ecosystem is divided into four complementary repositories:

| Repository | Description |
|------------|-------------|
| [`gatewise-esp32`](https://github.com/victorbezerra-dev/gatewise-esp32) - Public Repo | Firmware for the ESP32 smart lock module (IoT, RSA verification, MQTT/HTTP) |
| [`gatewise-app`](https://github.com/victorbezerra-dev/gatewise-app) - Private Repo | Mobile application for users to request access and confirm openings |
| [`gatewise-backend`](https://github.com/victorbezerra-dev/gatewise-backend) - Private Repo | Backend (.NET + Keycloak + RabbitMQ) handling auth, rules, and signed commands |

> 📌 This repository serves as the central documentation and entry point for the GateWise platform.

## ⚙️ How It Works (High-Level)

### 1️. User Authentication
- Users authenticate via **Keycloak (OIDC)**.
- Roles and permissions are centrally managed.

### 2️. Access Request
- A user requests to open a lab door via the **mobile app**.
- The backend validates business rules:
  - Time window
  - User permissions
  - Lab state

### 3️. Signed Command Issuance
- The backend generates a command (`open`, `lock`, etc.).
- The command is **digitally signed (RSA + SHA-256)**.

### 4️. Device Verification
- The ESP32 receives the command via **MQTT**.
- It verifies the signature using the backend’s **public key**.

### 5️. Human Confirmation
- The ESP32 sends an **HTTP confirmation request**.
- The backend forwards the request to the app via **WebSocket**.
- A user must explicitly confirm the opening.

### 6️. Actuation
- Only after backend approval does the ESP32 actuate the lock.
- All steps are **logged for audit purposes**.
  
## 🔐 Security Architecture

GateWise is built with multiple defensive layers:

### 🔏 Cryptographic Command Channel (Backend → Device)

All lock commands are:

- **Digitally signed using asymmetric cryptography (RSA + SHA-256)**
- **Published over MQTT**

Each ESP32:

- Stores **only the backend public key**
- Verifies command authenticity **before any action**

Commands include:

- **Nonce**
- **Timestamp**
- **Intent** (`open`, `lock`, etc.)

This prevents:

- **Forged commands**
- **Replay attacks**
- **Unauthorized actuation**

---

### 🔁 Mutual Trust Verification (Device → Backend)

After validating a command, the ESP32:

- Sends an **HTTP confirmation request**
- Signs the request using its **own private key**

The backend:

- Verifies the device signature using the registered **device public key**
- Confirms that the request originated from a **trusted device**

This establishes a **bidirectional cryptographic trust channel**.

---

### 🔐 Zero-Trust Device Model

Devices are considered **untrusted by default**.

The ESP32:

- Has **no embedded credentials or shared secrets**
- Cannot autonomously decide to open a lock

Final authority always resides in the **backend**, enforced through:

- **Business rules**
- **Human confirmation**
- **Cryptographic validation**

> Physical access is treated as a **security-critical operation**, not a convenience feature.

## Authentication and Access Control Flow
<p align="center">
  <img  width="70%"  alt="gatewise-flow" src="https://github.com/user-attachments/assets/e22b1d1d-3911-44f4-9445-a3e12f3fe69f" />
</p>


## 🧩 Technologies Used

### 🔌 Embedded / IoT
- **ESP32** (Arduino Core)
- **mbedTLS** (RSA, SHA-256, Base64)
- **MQTT**
- **HTTPClient**
- **Wi-Fi**

---

### 🖥️ Backend
- **.NET**
- **Keycloak** (OIDC / IAM)
- **RabbitMQ**
- **REST + WebSocket APIs**
- **Outbox pattern** for reliability

---

### 📱 Mobile (Flutter)
- **Flutter**
- **Secure storage** for sensitive data (tokens, keys)
- **WebSocket / SignalR** confirmation flow
- **Lottie animations** for rich UI feedback

## 🎯 Use Cases

- **University laboratories**
- **Research facilities**
- **Shared technical rooms**
- **Teaching labs with controlled access**
- **Temporary or conditional access scenarios**

## 🚀 Future Roadmap

- 🔄 **OTA firmware updates**
- 🧬 **Biometric confirmation** (FaceMatch + Liveness)
- 📊 **Advanced telemetry & health monitoring**
- 🧠 **Smart access recommendations**
- 🔐 **Hardware secure elements** (optional)
- 🏫 **Multi-campus support**
- 📜 **Immutable audit trails** (blockchain-ready)

## 🧠 Why GateWise?

- **No physical keys**
- **No static access cards**
- **No blind trust in devices**
- **Full traceability**
- **Cryptographic guarantees**
- **Designed by engineers, for engineers**

> GateWise treats **physical access as a security-critical system**, not a convenience feature.

---

## 🤝 Contributing

Contributions are welcome:

- Improve **security flows**
- Add **device support**
- Enhance **audit capabilities**
- Improve **UX** for admins or users
- Review **cryptographic implementations**

If you care about **security**, **distributed systems**, or **real-world IoT**, this project is for you.

---

Crafted with 🔐 **security-first thinking**, ⚙️ **engineering rigor**, and ❤️ for systems that must not fail.


