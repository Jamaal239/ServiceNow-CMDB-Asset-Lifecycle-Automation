# ServiceNow CMDB Asset Lifecycle Automation

## 📌 Project Overview
An enterprise-grade configuration management database (CMDB) pipeline engineered within ServiceNow Workflow Studio (Flow Designer). This project automates the transition of physical or virtual hardware allocation by monitoring fulfillment infrastructure tasks and dynamically instantiating corresponding Configuration Items (CIs) into the `cmdb_ci_computer` architecture upon deployment confirmation.

The system bridges the gap between active transactional technical tasks (`sc_task`) and hardware data state accuracy, mitigating the risk of tracking blindspots, configuration drift, and manual administrative data entry delays.

---

## 🛠️ System Architecture & Workflow Logic

### 1. Event Trigger Engine
* **Source Table:** Catalog Task `[sc_task]`
* **Evaluation Matrix:** Tries execution criteria explicitly upon record update conditions.
* **Filter Criteria:** Fires exclusively when an operational task state changes to **Closed Complete** and matches the string short description identifier: `Configure VDI Environment for User`.
* **Execution Frequency:** Restructured to evaluate exactly **Once** per specific task update stream to preserve system computational resources and prevent duplicate asset provisioning logs.

### 2. Automated Record Instantiation
* **Target Table:** Computer `[cmdb_ci_computer]`
* **Dynamic Data Pill Transformations:**
  * **Name Mapping:** Prefixes asset configurations with a standardized system string (`VDI-`) appended directly to the variable data extraction of the target end user's full network runtime login name.
  * **Asset Ownership:** Automatically populates corporate accountability tables by extracting user account details from the root requested items and copying them to the CI's `Assigned to` property.
  * **Operational Baseline State:** Instantly updates target hardware state values to an operational status of `Installed`.

---

## 📸 Step-by-Step Configuration & Verification

### Phase 1: Flow Metadata Initialization
The flow was configured within the Global Scope and set to run as "System User." This ensures the automation has the proper backend permissions to write data directly to the CMDB tables without being blocked by security constraints.
<img width="1920" height="945" alt="01_Flow_Properties" src="https://github.com/user-attachments/assets/e9c50465-ba07-4a1a-b005-ee4bc892fcc8" />


### Phase 2: Flow Canvas Staging
A clean baseline workspace was established in Workflow Studio to organize our trigger logic and subsequent record actions.
<img width="1920" height="947" alt="02_Blank_Flow_Canvas" src="https://github.com/user-attachments/assets/c2dad9d9-59ca-4c90-b928-1283c04cf30d" />

### Phase 3: Trigger Condition Mapping
Set the automation to monitor the Catalog Task `[sc_task]` table. It triggers exclusively when a task state changes to "Closed Complete" and the Short Description matches "Configure VDI Environment for User."
<img width="1920" height="944" alt="03_Flow_Trigger" src="https://github.com/user-attachments/assets/2f28d5d1-1853-459e-aa9d-1880d163b6e0" />

### Phase 4: Create Record Action Injection
Added a core platform action to target the ServiceNow CMDB Computer table `[cmdb_ci_computer]`, which tells the system exactly where to create the new asset record.
<img width="1920" height="942" alt="04_Create_Record_Action" src="https://github.com/user-attachments/assets/74e3f73d-72a3-402d-8fe2-c0de7898a461" />


### Phase 5: Data Pill & Dot-Walking Mapping
Mapped the field values for the new asset record. Used dot-walking to pull the user's name directly from the parent requested item variables, dynamically creating a unique asset name (`VDI-{{User Name}}`) and assigning ownership.
<img width="1920" height="944" alt="05_Mapped_Asset_Fields" src="https://github.com/user-attachments/assets/33356a5a-5b66-4505-beb4-c5efdce3c2f9" />


### Phase 6: Flow Execution Testing & Verification
Ran a sandbox test using a sample catalog task record. Checked the Flow Execution Details dashboard to verify that all operations completed successfully with zero data truncation or lookup faults.
<img width="1920" height="940" alt="06_Flow_Test_Execution" src="https://github.com/user-attachments/assets/79874f71-c969-4d7c-aaca-71c68c1b7a1e" />


---

## 🛠️ Real-World Engineering Insights

### 🔍 Problems I Solved
* **Fixed Tracking Blindspots:** Stopped the common issue where IT technicians manually build a virtual machine but forget to update the inventory database, which usually leaves a team guessing who owns what.
* **Eliminated Manual Typos:** Saved the service desk from data-entry mistakes by locking down the asset names to a clean format (`VDI-`) that fills itself in automatically using real system data.
* **Protected Database Performance:** Kept the system running fast by telling the automation to trigger exactly *once* when a task completes, rather than re-running over and over every time someone adds a comment.

### 💡 Core Things I Learned
* **Table Relations & Dot-Walking:** Mastered how to grab data across different tables (like pulling a user's original account name from a Request variable all the way down into an active Catalog Task).
* **Defensive Automation:** Learned how to set strict trigger rules (like matching an exact short description) so my automation only runs when it is supposed to, instead of accidentally triggering on unrelated IT tickets.
* **Debugging Workflows:** Gained experience using the Flow Execution dashboard to trace data variables, read error messages, and verify that the database successfully created the records.

### 🏢 How This Applies to Real Enterprise IT
* **Faster Employee Onboarding:** The second a technician finishes setting up a new hire's virtual desktop, the computer is logged into global inventory automatically. No double-data entry needed.
* **Audit & Asset Accuracy:** Keeps the company's hardware records perfectly accurate, which is essential for tracking IT budgets, software licenses, and prepping for official company audits.
* **Quick Security Tracking:** If a machine behaves suspiciously or gets compromised on the network, security teams can search the CMDB instantly to see exactly who that asset belongs to, speeding up response times.
