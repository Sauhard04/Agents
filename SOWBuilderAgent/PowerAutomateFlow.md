# Power Automate Flow — SOW Word Document Generator

## Overview

This flow is triggered by the SOW Builder Copilot Studio agent.
It receives 9 inputs, builds a structured SOW Word document using a SharePoint template, and returns a shareable download link.

---

## Step 1 — Create the Word Template in SharePoint

### 1a. Create a SharePoint Document Library

1. Go to your SharePoint site
2. Create a new **Document Library** named: `SOW Templates`
3. Create another library named: `Generated SOWs`

### 1b. Create the Word Template

1. Open **Microsoft Word** (desktop app)
2. Create a new document with the following structure (copy the layout from the Ethos SOW):

```
[COMPANY LOGO / HEADER]

SCOPE OF WORK
Techno-Commercial Proposal for GWS to M365 Migration

Submitted to: [ClientName]
Date: [SOWDate]
Prepared by: Meridian Solutions Pvt. Ltd.
...
```

3. For each placeholder, insert a **Plain Text Content Control**:
   - Go to **Developer tab → Plain Text Content Control**
   - Click **Properties** and set the **Title** to the placeholder name exactly

| Placeholder Title (Content Control) | What it maps to |
|---|---|
| `ClientName` | Client Company Name |
| `SOWDate` | SOW Date |
| `TotalUsers` | Total Number of Users |
| `CurrentEnv` | Current Environment |
| `TotalMailboxSize` | Total Mailbox Size (TB) |
| `TotalDriveSize` | Total Drive Size (TB) |
| `HighestMailbox` | Highest Mailbox (GB) |
| `HighestDrive` | Highest Drive (GB) |
| `MigrationScope` | Migration Scope |
| `MigDays` | Calculated migration days |
| `PilotDays` | Pilot phase days |
| `BatchDays` | Batch migration days |
| `Tier1Count` | 0-2GB mailbox count |
| `Tier2Count` | 2-30GB mailbox count |
| `Tier3Count` | 30-50GB mailbox count |

4. Save the template as `SOW_Template.docx`
5. Upload it to the `SOW Templates` SharePoint library

> **Note:** If you don't have the Developer tab, go to File → Options → Customize Ribbon → check Developer

---

## Step 2 — Create the Power Automate Flow

### Flow Type
**Instant cloud flow** → Trigger: **When Copilot Studio calls a flow**

---

### Flow Steps (in order)

#### Trigger: "When an agent calls a flow" (Copilot Studio)

Add the following **Text inputs** in the trigger:

| Input Name | Type |
|---|---|
| `text_ClientName` | Text |
| `text_SOWDate` | Text |
| `text_TotalUsers` | Text |
| `text_CurrentEnv` | Text |
| `text_TotalMailboxSize` | Text |
| `text_TotalDriveSize` | Text |
| `text_HighestMailbox` | Text |
| `text_HighestDrive` | Text |
| `text_MigrationScope` | Text |

---

#### Step 1 — Calculate Migration Days

**Action: Compose** (name it `CalcMigDays`)

Expression:
```
if(lessOrEquals(int(triggerBody()?['text_TotalUsers']), 100), '8 Days',
  if(lessOrEquals(int(triggerBody()?['text_TotalUsers']), 500), '15 Days',
    if(lessOrEquals(int(triggerBody()?['text_TotalUsers']), 1000), '20 Days',
      '30 Days')))
```

**Action: Compose** (name it `CalcPilotDays`)
```
if(lessOrEquals(int(triggerBody()?['text_TotalUsers']), 100), '2',
  if(lessOrEquals(int(triggerBody()?['text_TotalUsers']), 500), '3', '5'))
```

**Action: Compose** (name it `CalcBatchDays`)
```
if(lessOrEquals(int(triggerBody()?['text_TotalUsers']), 100), '4',
  if(lessOrEquals(int(triggerBody()?['text_TotalUsers']), 500), '8', '12'))
```

---

#### Step 2 — Calculate Tier Counts

**Action: Compose** (name it `Tier1`)
```
string(int(mul(float(triggerBody()?['text_TotalUsers']), 0.44)))
```

**Action: Compose** (name it `Tier2`)
```
string(int(mul(float(triggerBody()?['text_TotalUsers']), 0.42)))
```

**Action: Compose** (name it `Tier3`)
```
string(int(mul(float(triggerBody()?['text_TotalUsers']), 0.06)))
```

---

#### Step 3 — Get Word Template

**Action: Get file content** (SharePoint connector)
- Site Address: `[Your SharePoint Site URL]`
- File Identifier: `/SOW Templates/SOW_Template.docx`

---

#### Step 4 — Populate Word Template

**Action: Populate a Microsoft Word template** (Word Online (Business) connector)
- Location: SharePoint
- Document Library: `SOW Templates`
- File: `SOW_Template.docx`

Then map each content control:

| Content Control Title | Value |
|---|---|
| ClientName | `@{triggerBody()?['text_ClientName']}` |
| SOWDate | `@{triggerBody()?['text_SOWDate']}` |
| TotalUsers | `@{triggerBody()?['text_TotalUsers']}` |
| CurrentEnv | `@{triggerBody()?['text_CurrentEnv']}` |
| TotalMailboxSize | `@{triggerBody()?['text_TotalMailboxSize']} TB` |
| TotalDriveSize | `@{triggerBody()?['text_TotalDriveSize']} TB` |
| HighestMailbox | `@{triggerBody()?['text_HighestMailbox']} GB` |
| HighestDrive | `@{triggerBody()?['text_HighestDrive']} GB` |
| MigrationScope | `@{triggerBody()?['text_MigrationScope']}` |
| MigDays | `@{outputs('CalcMigDays')}` |
| PilotDays | `@{outputs('CalcPilotDays')}` |
| BatchDays | `@{outputs('CalcBatchDays')}` |
| Tier1Count | `@{outputs('Tier1')}` |
| Tier2Count | `@{outputs('Tier2')}` |
| Tier3Count | `@{outputs('Tier3')}` |

---

#### Step 5 — Create the Output File

**Action: Create file** (SharePoint connector)
- Site Address: `[Your SharePoint Site URL]`
- Folder Path: `/Generated SOWs`
- File Name: `SOW_@{triggerBody()?['text_ClientName']}_@{triggerBody()?['text_SOWDate']}.docx`
- File Content: `@{body('Populate_a_Microsoft_Word_template')}`

---

#### Step 6 — Get Shareable Link

**Action: Create sharing link for a file or folder** (SharePoint connector)
- Site Address: `[Your SharePoint Site URL]`
- List or Library: `Generated SOWs`
- Item ID: `@{outputs('Create_file')?['body/Id']}`
- Link Type: **Organization** (or Anonymous if external sharing needed)
- Link Scope: **Organization**

---

#### Step 7 — Return the URL to Copilot Studio

**Action: Return value(s) to Power Virtual Agents**

| Output Name | Type | Value |
|---|---|---|
| `text_DocumentURL` | Text | `@{outputs('Create_sharing_link_for_a_file_or_folder')?['body/sharingLink/url']}` |

---

## Step 3 — Connect the Flow to the Agent

1. **Save and Test** the flow in Power Automate
2. Copy the **Flow GUID** from the URL bar (the long string after `/flows/`)
   - URL looks like: `https://make.powerautomate.com/environments/.../flows/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/...`
3. Open [`topic.yaml`](../SOWBuilderAgent/topic.yaml) and replace:
   ```yaml
   flowId: REPLACE_WITH_YOUR_FLOW_GUID
   ```
   with your actual GUID:
   ```yaml
   flowId: XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```
4. Re-paste the updated YAML into Copilot Studio's code editor
5. Save and Publish

---

## Step 4 — Test End-to-End

1. In Copilot Studio test panel, type `generate sow`
2. Fill the Adaptive Card
3. Click **Generate SOW ➡️**
4. Agent should show: `⏳ Got it! Generating SOW for [Company]...`
5. Then return: `✅ Your SOW document is ready! 📥 Click here to download`
6. Click the link → Word document opens in SharePoint/browser

---

## Troubleshooting

| Issue | Fix |
|---|---|
| "Populate Word template" action missing | Install Word Online (Business) connector; needs M365 license |
| Template placeholders not filling | Verify Content Control Title matches exactly (case-sensitive) |
| Sharing link returns empty | Check SharePoint permissions; ensure link type matches org settings |
| Flow not appearing in Copilot Studio | Flow must be in the same Power Platform environment as the agent |
| `text_DocumentURL` is empty in agent | Check Step 7 output name matches exactly what YAML binding expects |
