# Payville Inc — Linux Infrastructure Setup

## Overview
This project documents the setup of Linux user accounts, groups,
and directory permissions for Payville Inc, a small business.
The goal is to implement the principle of least privilege — each
employee only has access to directories relevant to their role.

**Server OS:** Ubuntu 24.04 LTS
**Working Directory:** `/payville`

---

## Employees and Roles

| Employee   | Role                   | Username   |
|------------|------------------------|------------|
| Andrew     | System Administrator   | andrew     |
| Julius     | Legal                  | julius     |
| Chizi      | Human Resource Manager | chizi      |
| Jeniffer   | Sales Manager          | jeniffer   |
| Adeola     | Business Strategist    | adeola     |
| Bach       | CEO                    | bach       |
| Gozie      | IT Intern              | gozie      |
| Ogochukwu  | Finance Manager        | ogochukwu  |

---

## Step 1 — Created Groups

I created department groups to represent each role in the company.
Combined groups were also created to handle directories that need
to be shared between two roles.

**Department groups:**
```bash
groupadd sysadmin
groupadd legal
groupadd hr
groupadd sales
groupadd strategy
groupadd executive
groupadd it_interns
groupadd finance
```

**Combined and shared access groups:**
```bash
groupadd company_all    # All employees
groupadd finance_exec   # Finance Manager + CEO
groupadd strategy_exec  # Business Strategist + CEO
groupadd legal_exec     # Legal + CEO
```

---

## Step 2 — Created Users

Each user was created with a home directory, bash shell,
job title description, and assigned to their department group.
```bash
useradd -m -s /bin/bash -c "System Administrator" -G sysadmin andrew
useradd -m -s /bin/bash -c "Legal" -G legal julius
useradd -m -s /bin/bash -c "Human Resource Manager" -G hr chizi
useradd -m -s /bin/bash -c "Sales Manager" -G sales jeniffer
useradd -m -s /bin/bash -c "Business Strategist" -G strategy adeola
useradd -m -s /bin/bash -c "CEO" -G executive,sysadmin,finance,legal,hr,sales,strategy bach
useradd -m -s /bin/bash -c "IT Intern" -G it_interns gozie
useradd -m -s /bin/bash -c "Finance Manager" -G finance ogochukwu
```

**Added all users to company_all group:**
```bash
for user in andrew julius chizi jeniffer adeola bach gozie ogochukwu; do
  usermod -aG company_all $user
done
```

**Added users to combined groups:**
```bash
usermod -aG finance_exec ogochukwu
usermod -aG finance_exec bach
usermod -aG strategy_exec adeola
usermod -aG strategy_exec bach
usermod -aG legal_exec julius
usermod -aG legal_exec bach
```

---

## Step 3 — Created Company Directories

All directories were created under /payville.
```bash
mkdir -p /payville/finance_budgets
mkdir -p /payville/contract_documents
mkdir -p /payville/business_projections
mkdir -p /payville/business_models
mkdir -p /payville/employee_data
mkdir -p /payville/company_vision_and_mission
mkdir -p /payville/server_config_scripts
```

---

## Step 4 — Set Ownership and Permissions

Used chown to assign group ownership and chmod to control access.
chmod 2770 means only the owner and group have full access.
chmod 2775 means the group has full access and everyone else can read.
```bash
chown root:finance_exec /payville/finance_budgets
chmod 2770 /payville/finance_budgets

chown root:legal_exec /payville/contract_documents
chmod 2770 /payville/contract_documents

chown root:strategy_exec /payville/business_projections
chmod 2770 /payville/business_projections

chown root:strategy_exec /payville/business_models
chmod 2770 /payville/business_models

chown root:hr /payville/employee_data
chmod 2770 /payville/employee_data

chown root:company_all /payville/company_vision_and_mission
chmod 2775 /payville/company_vision_and_mission

chown root:sysadmin /payville/server_config_scripts
chmod 2770 /payville/server_config_scripts
```

---

## Access Control Summary

| Directory                    | Group Owner    | Who Has Access                      |
|------------------------------|----------------|-------------------------------------|
| finance_budgets              | finance_exec   | Ogochukwu, Bach (CEO)               |
| contract_documents           | legal_exec     | Julius, Bach (CEO)                  |
| business_projections         | strategy_exec  | Adeola, Bach (CEO)                  |
| business_models              | strategy_exec  | Adeola, Bach (CEO)                  |
| employee_data                | hr             | Chizi only                          |
| company_vision_and_mission   | company_all    | All employees (read only)           |
| server_config_scripts        | sysadmin       | Andrew only                         |

---

## How the Script Was Run

All the above commands were written into a single bash script
using nano and executed with one command:
```bash
nano payville_setup.sh
sudo bash payville_setup.sh
```

---

## Screenshots

### Script Written in Nano
![Script](payville-script.png)

### Script Part 2
![Script2](payville-script2.png)

### Script Part 3
![Script3](paville-script3.png)

### Output — Users and Directories
![Output](payville-output.png)

---

## What I Learned

Working on this project helped me understand how Linux manages
users, groups, and file permissions. I learned how to use useradd,
groupadd, chown, and chmod to control access to directories based
on job roles. I also learned how to write a bash script to automate
multiple commands at once instead of typing them one by one.
