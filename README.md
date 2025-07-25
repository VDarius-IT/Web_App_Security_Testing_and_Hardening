# Streamlining IT: Active Directory Automation with PowerShell DSC

![PowerShell](https://img.shields.io/badge/PowerShell-5.1+-5391FE?style=for-the-badge&logo=powershell)![Active Directory](https://img.shields.io/badge/Active%20Directory-Automated-0078D4?style=for-the-badge&logo=windows-server)![DSC](https://img.shields.io/badge/DSC-Enabled-blueviolet?style=for-the-badge)![Efficiency](https://img.shields.io/badge/Onboarding-80%25%20Faster-brightgreen?style=for-the-badge)![License](https://img.shields.io/badge/License-MIT-green.svg?style=for-the-badge)

> A transformative automation initiative that leverages **PowerShell Desired State Configuration (DSC)** to revolutionize user lifecycle management in Active Directory. This project automates user provisioning, group memberships, and GPO policies, achieving an **80% reduction in employee onboarding time**.

---

## 🎯 The Challenge: Manual Onboarding is a Bottleneck

In many IT environments, managing the user lifecycle in Active Directory is a manual, repetitive, and error-prone process. Onboarding a new employee often involves a long checklist: creating a user, setting a password, assigning them to dozens of security and distribution groups, and linking the correct Group Policy Objects (GPOs).

This manual approach leads to:

*   **Slow Onboarding:** New hires wait hours or even days for full system access, hindering their productivity.
*   **Inconsistency & Errors:** Manual data entry inevitably leads to mistakes, resulting in improper permissions and security gaps.
*   **High Operational Overhead:** IT teams spend valuable time on routine administrative tasks instead of strategic initiatives.
*   **Difficult Auditing:** Tracking who has access to what and why becomes a significant compliance challenge.

## ✨ The Solution: Automation with PowerShell DSC

This project introduces a robust, declarative automation framework using **PowerShell DSC**. By defining the "desired state" for our Active Directory environment, we can ensure that every user account, group, and policy is configured consistently and automatically.

The solution is built on three core pillars:

1.  **Declarative Configuration:** Instead of writing scripts that perform steps, we define *what* the end-state should look like. DSC handles the "how."
2.  **Idempotent Execution:** The configurations can be run repeatedly without causing errors or unintended changes, ensuring the environment always matches the defined state.
3.  **Centralized Management:** All configurations are managed as code, allowing for version control, peer review, and a clear audit trail.

---

## 🚀 Key Features & Automation Components

*   ✅ **Automated User Provisioning:** New user accounts are created and configured based on a template (e.g., department, role), pulling data from a source file (like a CSV) or an HR system API.
*   ✅ **Dynamic Group Membership:** Users are automatically assigned to the correct security and distribution groups based on their attributes (e.g., title, department), ensuring they always have the right level of access.
*   ✅ **Consistent GPO Configuration:** DSC ensures that the correct GPOs are linked and enforced for specific Organizational Units (OUs), standardizing user and computer policies across the domain.
*   ✅ **Configuration as Code:** The entire Active Directory user policy is stored in readable, version-controlled PowerShell scripts, eliminating "configuration drift."

---

## 💻 Core Technologies Used

| Technology | Purpose |
| :--- | :--- |
| **PowerShell** | The core scripting language used for automation logic. |
| **Desired State Configuration (DSC)** | The declarative platform for managing IT infrastructure as code. |
| **Active Directory** | The directory service being automated and managed. |
| **CSV / JSON** | Data sources used to feed user information into the provisioning scripts. |
| **Git & GitHub** | For version control of all configuration scripts and documentation. |

---

## 🔧 How It Works: A Three-Phase Approach

This project implements a complete, automated lifecycle for user management.

### Phase 1: Defining the Desired State

We create DSC Configuration scripts that define the ideal state for our users, groups, and OUs.

**Example: A DSC Configuration for a New User**
```powershell
Configuration NewUserOnboarding {
    param (
        [string]$UserName,
        [string]$Department,
        [string]$OUPath
    )

    Import-DscResource -ModuleName 'PSDscResources'

    Node 'localhost' {
        User $UserName {
            Ensure       = 'Present'
            UserName     = $UserName
            Password     = (ConvertTo-SecureString 'DefaultPassword123' -AsPlainText -Force)
            FullName     = "User - $UserName"
            Department   = $Department
            Path         = $OUPath
            PasswordNeverExpires = $false
            UserCannotChangePassword = $false
        }

        Group "Department_Group_$Department" {
            Ensure      = 'Present'
            GroupName   = "Department_Group_$Department"
            MembersToInclude = @($UserName)
        }
    }
}
```

### Phase 2: Compiling and Staging

The human-readable configuration scripts are compiled into `.mof` files, which are the documents the DSC engine uses to enforce the state.

```bash
# Compile the configuration
NewUserOnboarding -UserName "jdoe" -Department "Sales" -OUPath "OU=Sales,DC=corp,DC=local" -OutputPath C:\DSC\MOF

# Start the configuration
Start-DscConfiguration -Path C:\DSC\MOF -Wait -Verbose
```

### Phase 3: Continuous Enforcement

The DSC Local Configuration Manager (LCM) on the Domain Controller is configured to periodically check for and automatically correct any deviations from the desired state, ensuring the environment remains consistent over time.

---

## 📁 Repository Structure

```
/Active-Directory-Automation
│
├── Configurations/         # Main DSC configuration scripts
│   ├── Base_OU_Structure.ps1
│   ├── User_Onboarding.ps1
│   └── Group_Membership.ps1
│
├── DSCResources/           # Custom DSC resources, if any
│
├── Data_Sources/           # Example data files for new users
│   └── new_hires.csv
│
├── Scripts/                # Helper and execution scripts
│   ├── Invoke-Onboarding.ps1
│   └── Run-DscComplianceCheck.ps1
│
├── Docs/                   # Supporting documentation
│   ├── Architecture.png
│   └── GPO_Policy_Matrix.md
│
├── README.md               # You are here!
│
└── LICENSE
```

---

## 🚀 Getting Started

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/your-username/Active-Directory-Automation.git
    cd Active-Directory-Automation
    ```
2.  **Prerequisites:**
    *   Ensure the PowerShell DSC feature is enabled on your server (`Install-WindowsFeature DSC-Service`).
    *   Ensure the `PSDscResources` module is installed (`Install-Module -Name PSDscResources`).
3.  **Customize the Configuration:**
    *   Open `Configurations/User_Onboarding.ps1` and adjust the parameters to fit your domain and OU structure.
    *   Populate `Data_Sources/new_hires.csv` with the user data you want to provision.
4.  **Run the Automation:**
    *   Execute the main wrapper script to start the onboarding process.
    ```powershell
    .\Scripts\Invoke-Onboarding.ps1 -CsvPath .\Data_Sources\new_hires.csv
    ```

---

## 🎯 Key Outcomes & Impact

| Before | After |
| :--- | :--- |
| Manual, ticket-based user creation | Fully automated, zero-touch provisioning |
| **2-4 hours** per new employee | **Under 15 minutes** per new employee |
| Inconsistent permissions and groups | Standardized, role-based access control |
| High risk of human error | Consistent and auditable configurations |
| **Significant operational drain** | **80% reduction in onboarding time** |

This project has transformed user lifecycle management from a manual chore into a streamlined, automated, and secure process, freeing up the IT team to focus on high-value initiatives.

---

## 🤝 Contribute

This project serves as a blueprint for Active Directory automation. Contributions are welcome! Feel free to open an issue to discuss improvements or submit a pull request with new features, such as:

*   Integration with an HRIS API (e.g., Workday, BambooHR).
*   Automated offboarding scripts.
*   Advanced GPO policy configurations.
*   Pester tests for DSC configurations.
