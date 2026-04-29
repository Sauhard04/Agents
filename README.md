<div align="center">

# 🤖 Microsoft Copilot Studio Agents

[![Built with](https://img.shields.io/badge/Built%20with-Microsoft%20Copilot%20Studio-0078D4?style=for-the-badge&logo=microsoft&logoColor=white)](https://copilotstudio.microsoft.com)
[![Platform](https://img.shields.io/badge/Platform-Microsoft%20365-D83B01?style=for-the-badge&logo=microsoft-office&logoColor=white)](https://www.microsoft.com/microsoft-365)
[![Teams](https://img.shields.io/badge/Deployed%20on-Microsoft%20Teams-6264A7?style=for-the-badge&logo=microsoft-teams&logoColor=white)](https://teams.microsoft.com)
[![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)

**A curated collection of intelligent AI agents built with Microsoft Copilot Studio, designed to automate enterprise workflows and enhance employee productivity across the Microsoft 365 ecosystem.**

---

</div>

## 📋 Table of Contents

- [Overview](#-overview)
- [Agents](#-agents)
  - [Claim Reimbursement Agent](#-claim-reimbursement-agent)
  - [M365 License Recommendation Agent](#-m365-license-recommendation-agent)
- [Architecture](#-architecture)
- [Getting Started](#-getting-started)
- [Project Structure](#-project-structure)
- [Tech Stack](#-tech-stack)
- [Contributing](#-contributing)

---

## 🌟 Overview

This repository contains production-ready **Microsoft Copilot Studio agents** that integrate seamlessly with Microsoft Teams, SharePoint, and Power Automate. Each agent is designed to solve a specific enterprise challenge through conversational AI, reducing manual effort and accelerating business processes.

> **Why these agents?**  
> Manual workflows like expense reimbursements and license management consume hours of employee time. These agents bring automation directly into the tools your team already uses — Microsoft Teams and Microsoft 365.

---

## 🚀 Agents

### 💰 Claim Reimbursement Agent

<table>
<tr>
<td width="120" align="center">

**v1.0.3**

</td>
<td>

An intelligent expense reimbursement assistant that guides employees through the entire claim submission process via a conversational interface.

</td>
</tr>
</table>

**✨ Key Features:**
- 📝 Guided data collection — collects expense type, amount, date, and proof of purchase
- 📎 File attachment support — employees can upload receipts and invoices directly
- ⚡ Auto-approval logic — claims under ₹2,000 are auto-approved for faster processing
- 📧 Manager routing — claims ≥ ₹2,000 are escalated to the manager via email with attachments
- 📊 SharePoint integration — all claim data is logged to a centralized SharePoint list
- 🔄 Power Automate flow — end-to-end automation from submission to approval

---

### 📋 M365 License Recommendation Agent

<table>
<tr>
<td width="120" align="center">

**v1.0.0**

</td>
<td>

A structured, flow-based advisor that walks users through a decision tree to recommend the most suitable Microsoft 365 license for their organization.

</td>
</tr>
</table>

**✨ Key Features:**
- 🏢 Organization profiling — gathers user count, org type, and industry
- 🔒 Security needs assessment — evaluates compliance and security requirements
- 🎯 Precise recommendations — maps requirements to the optimal M365 license tier
- 🚫 No hallucination — strictly follows a predefined questionnaire (no generative guessing)
- 💬 Rigid conversation flow — ensures consistent, reliable output every time

---

## 🏗 Architecture

```
                    ┌─────────────────────┐
                    │   Microsoft Teams   │
                    │   (User Interface)  │
                    └─────────┬───────────┘
                              │
                              ▼
                    ┌─────────────────────┐
                    │  Microsoft Copilot  │
                    │      Studio         │
                    │  (Agent Runtime)    │
                    └─────────┬───────────┘
                              │
                 ┌────────────┼────────────┐
                 ▼                         ▼
       ┌──────────────────┐     ┌──────────────────┐
       │  Power Automate  │     │   Knowledge Base  │
       │     (Flows)      │     │   (Dataverse)     │
       └────────┬─────────┘     └───────────────────┘
                │
                ▼
       ┌──────────────────┐
       │   SharePoint     │
       │   (Data Store)   │
       └──────────────────┘
```

---

## 🚀 Getting Started

### Prerequisites

- Microsoft 365 Business/Enterprise license
- Access to [Microsoft Copilot Studio](https://copilotstudio.microsoft.com)
- Microsoft Teams (desktop or web)
- Power Automate access (for Claim Reimbursement flows)

### Deployment

1. **Clone the repository**
   ```bash
   git clone https://github.com/Sauhard04/Agents.git
   cd Agents
   ```

2. **Import into Copilot Studio**
   - Navigate to [Microsoft Copilot Studio](https://copilotstudio.microsoft.com)
   - Use the agent manifest files to configure each agent in your environment

3. **Configure Power Automate flows** *(Claim Agent only)*
   - Set up the SharePoint list for claim data storage
   - Configure the approval email flow with manager routing

4. **Deploy to Teams**
   - Publish the agent from Copilot Studio
   - Add the agent to your Teams environment via the admin center

---

## 📁 Project Structure

```
Agents/
├── 📂 ClaimReimbursementAgent/
│   ├── 📄 manifest.json          # Teams app manifest (v1.0.3)
│   ├── 🖼️ color.png              # Full-color agent icon
│   └── 🖼️ outline.png            # Outline agent icon
│
├── 📂 M365LicenseRecommendationAgent/
│   ├── 📄 manifest.json          # Teams app manifest (v1.0.0)
│   ├── 🖼️ color.png              # Full-color agent icon
│   └── 🖼️ outline.png            # Outline agent icon
│
└── 📄 README.md
```

---

## 🛠 Tech Stack

| Technology | Purpose |
|---|---|
| **Microsoft Copilot Studio** | Agent builder & conversational AI runtime |
| **Microsoft Teams** | Deployment platform & user interface |
| **Power Automate** | Workflow automation & business logic |
| **SharePoint** | Data storage & list management |
| **Bot Framework** | Underlying bot communication protocol |
| **Dataverse** | Agent configuration & knowledge storage |

---

## 🤝 Contributing

Contributions are welcome! If you'd like to add a new agent or improve an existing one:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/new-agent`)
3. Commit your changes (`git commit -m 'Add new agent'`)
4. Push to the branch (`git push origin feature/new-agent`)
5. Open a Pull Request

---

<div align="center">


Made by [Sauhard Kaushik](https://github.com/Sauhard04) · Meridian Solutions

</div>
