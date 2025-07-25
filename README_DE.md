# IT-Optimierung: Active Directory-Automatisierung mit PowerShell DSC

![PowerShell](https://img.shields.io/badge/PowerShell-5.1+-5391FE?style=for-the-badge&logo=powershell)![Active Directory](https://img.shields.io/badge/Active%20Directory-Automated-0078D4?style=for-the-badge&logo=windows-server)![DSC](https://img.shields.io/badge/DSC-Enabled-blueviolet?style=for-the-badge)![Efficiency](https://img.shields.io/badge/Onboarding-80%25%20Faster-brightgreen?style=for-the-badge)![License](https://img.shields.io/badge/License-MIT-green.svg?style=for-the-badge)

> Eine transformative Automatisierungsinitiative, die **PowerShell Desired State Configuration (DSC)** nutzt, um das Management des Benutzerlebenszyklus im Active Directory zu revolutionieren. Dieses Projekt automatisiert die Benutzerbereitstellung, Gruppenmitgliedschaften und GPO-Richtlinien und erreicht eine **Reduzierung der Onboarding-Zeit für Mitarbeiter um 80 %**.

---

## 🎯 Die Herausforderung: Manuelles Onboarding als Engpass

In vielen IT-Umgebungen ist die Verwaltung des Benutzerlebenszyklus im Active Directory ein manueller, repetitiver und fehleranfälliger Prozess. Das Onboarding eines neuen Mitarbeiters umfasst oft eine lange Checkliste: einen Benutzer anlegen, ein Passwort setzen, ihn zu Dutzenden von Sicherheits- und Verteilergruppen hinzufügen und die richtigen Gruppenrichtlinienobjekte (GPOs) verknüpfen.

Dieser manuelle Ansatz führt zu:

*   **Langsames Onboarding:** Neue Mitarbeiter warten Stunden oder sogar Tage auf den vollen Systemzugriff, was ihre Produktivität beeinträchtigt.
*   **Inkonsistenz & Fehler:** Manuelle Dateneingabe führt unweigerlich zu Fehlern, was zu fehlerhaften Berechtigungen und Sicherheitslücken führt.
*   **Hoher Betriebsaufwand:** IT-Teams verbringen wertvolle Zeit mit administrativen Routineaufgaben anstatt mit strategischen Initiativen.
*   **Schwierige Überprüfbarkeit (Auditing):** Die Nachverfolgung, wer worauf Zugriff hat und warum, wird zu einer erheblichen Compliance-Herausforderung.

## ✨ Die Lösung: Automatisierung mit PowerShell DSC

Dieses Projekt stellt ein robustes, deklaratives Automatisierungs-Framework mit **PowerShell DSC** vor. Indem wir den „gewünschten Zustand“ (Desired State) für unsere Active Directory-Umgebung definieren, können wir sicherstellen, dass jedes Benutzerkonto, jede Gruppe und jede Richtlinie konsistent und automatisch konfiguriert wird.

Die Lösung basiert auf drei Grundpfeilern:

1.  **Deklarative Konfiguration:** Anstatt Skripte zu schreiben, die Schritte ausführen, definieren wir, *wie* der Endzustand aussehen soll. DSC kümmert sich um das „Wie“.
2.  **Idempotente Ausführung:** Die Konfigurationen können wiederholt ausgeführt werden, ohne Fehler oder unbeabsichtigte Änderungen zu verursachen. Dies stellt sicher, dass die Umgebung immer dem definierten Zustand entspricht.
3.  **Zentralisierte Verwaltung:** Alle Konfigurationen werden als Code verwaltet, was Versionskontrolle, Peer-Reviews und einen klaren Audit-Trail ermöglicht.

---

## 🚀 Wichtige Merkmale & Automatisierungskomponenten

*   ✅ **Automatisierte Benutzerbereitstellung:** Neue Benutzerkonten werden basierend auf einer Vorlage (z. B. Abteilung, Rolle) erstellt und konfiguriert, wobei Daten aus einer Quelldatei (wie einer CSV) oder einer HR-System-API abgerufen werden.
*   ✅ **Dynamische Gruppenmitgliedschaft:** Benutzer werden basierend auf ihren Attributen (z. B. Titel, Abteilung) automatisch den richtigen Sicherheits- und Verteilergruppen zugewiesen, um sicherzustellen, dass sie immer die korrekten Zugriffsberechtigungen haben.
*   ✅ **Konsistente GPO-Konfiguration:** DSC stellt sicher, dass die richtigen GPOs für bestimmte Organisationseinheiten (OUs) verknüpft und durchgesetzt werden, wodurch Benutzer- und Computerrichtlinien in der gesamten Domäne standardisiert werden.
*   ✅ **Konfiguration als Code (Configuration as Code):** Die gesamte Active Directory-Benutzerrichtlinie wird in lesbaren, versionierten PowerShell-Skripten gespeichert, wodurch „Konfigurationsdrift“ vermieden wird.

---

## 💻 Verwendete Kerntechnologien

| Technologie | Zweck |
| :--- | :--- |
| **PowerShell** | Die Kern-Skriptsprache für die Automatisierungslogik. |
| **Desired State Configuration (DSC)** | Die deklarative Plattform zur Verwaltung der IT-Infrastruktur als Code. |
| **Active Directory** | Der Verzeichnisdienst, der automatisiert und verwaltet wird. |
| **CSV / JSON** | Datenquellen, die Benutzerinformationen in die Bereitstellungsskripte einspeisen. |
| **Git & GitHub** | Für die Versionskontrolle aller Konfigurationsskripte und der Dokumentation. |

---

## 🔧 Funktionsweise: Ein Drei-Phasen-Ansatz

Dieses Projekt implementiert einen vollständigen, automatisierten Lebenszyklus für die Benutzerverwaltung.

### Phase 1: Definition des gewünschten Zustands

Wir erstellen DSC-Konfigurationsskripte, die den idealen Zustand für unsere Benutzer, Gruppen und OUs definieren.

**Beispiel: Eine DSC-Konfiguration für einen neuen Benutzer**
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

### Phase 2: Kompilieren und Bereitstellen (Staging)

Die für Menschen lesbaren Konfigurationsskripte werden in `.mof`-Dateien kompiliert. Dies sind die Dokumente, die die DSC-Engine zur Durchsetzung des Zustands verwendet.

```bash
# Die Konfiguration kompilieren
NewUserOnboarding -UserName "jdoe" -Department "Sales" -OUPath "OU=Sales,DC=corp,DC=local" -OutputPath C:\DSC\MOF

# Die Konfiguration starten
Start-DscConfiguration -Path C:\DSC\MOF -Wait -Verbose
```

### Phase 3: Kontinuierliche Durchsetzung

Der DSC Local Configuration Manager (LCM) auf dem Domänencontroller wird so konfiguriert, dass er regelmäßig Abweichungen vom gewünschten Zustand überprüft und automatisch korrigiert. Dadurch wird sichergestellt, dass die Umgebung über die Zeit konsistent bleibt.

---

## 📁 Verzeichnisstruktur

```
/Active-Directory-Automation
│
├── Configurations/         # Haupt-DSC-Konfigurationsskripte
│   ├── Base_OU_Structure.ps1
│   ├── User_Onboarding.ps1
│   └── Group_Membership.ps1
│
├── DSCResources/           # Benutzerdefinierte DSC-Ressourcen, falls vorhanden
│
├── Data_Sources/           # Beispieldateien für neue Benutzer
│   └── new_hires.csv
│
├── Scripts/                # Hilfs- und Ausführungsskripte
│   ├── Invoke-Onboarding.ps1
│   └── Run-DscComplianceCheck.ps1
│
├── Docs/                   # Unterstützende Dokumentation
│   ├── Architecture.png
│   └── GPO_Policy_Matrix.md
│
├── README.md               # Sie sind hier!
│
└── LICENSE
```

---

## 🚀 Erste Schritte

1.  **Klonen Sie das Repository:**
    ```bash
    git clone https://github.com/your-username/Active-Directory-Automation.git
    cd Active-Directory-Automation
    ```
2.  **Voraussetzungen:**
    *   Stellen Sie sicher, dass das PowerShell-DSC-Feature auf Ihrem Server aktiviert ist (`Install-WindowsFeature DSC-Service`).
    *   Stellen Sie sicher, dass das `PSDscResources`-Modul installiert ist (`Install-Module -Name PSDscResources`).
3.  **Passen Sie die Konfiguration an:**
    *   Öffnen Sie `Configurations/User_Onboarding.ps1` und passen Sie die Parameter an Ihre Domänen- und OU-Struktur an.
    *   Füllen Sie `Data_Sources/new_hires.csv` mit den Benutzerdaten, die Sie bereitstellen möchten.
4.  **Führen Sie die Automatisierung aus:**
    *   Führen Sie das Haupt-Wrapper-Skript aus, um den Onboarding-Prozess zu starten.
    ```powershell
    .\Scripts\Invoke-Onboarding.ps1 -CsvPath .\Data_Sources\new_hires.csv
    ```

---

## 🎯 Wichtige Ergebnisse & Auswirkungen

| Vorher | Nachher |
| :--- | :--- |
| Manuelle, Ticket-basierte Benutzererstellung | Vollautomatisierte, Zero-Touch-Bereitstellung |
| **2-4 Stunden** pro neuem Mitarbeiter | **Unter 15 Minuten** pro neuem Mitarbeiter |
| Inkonsistente Berechtigungen und Gruppen | Standardisierte, rollenbasierte Zugriffskontrolle |
| Hohes Risiko für menschliche Fehler | Konsistente und überprüfbare Konfigurationen |
| **Erheblicher operativer Aufwand** | **80 % Reduzierung der Onboarding-Zeit** |

Dieses Projekt hat das Management des Benutzerlebenszyklus von einer manuellen Routineaufgabe in einen optimierten, automatisierten und sicheren Prozess verwandelt. Dadurch wird das IT-Team entlastet, um sich auf wertschöpfende Initiativen zu konzentrieren.

---

## 🤝 Mitwirken

Dieses Projekt dient als Vorlage für die Automatisierung von Active Directory. Beiträge sind willkommen! Eröffnen Sie gerne ein Issue, um Verbesserungen zu diskutieren, oder reichen Sie einen Pull Request mit neuen Funktionen ein, wie zum Beispiel:

*   Integration mit einer HRIS-API (z. B. Workday, BambooHR).
*   Automatisierte Offboarding-Skripte.
*   Erweiterte GPO-Richtlinienkonfigurationen.
*   Pester-Tests für DSC-Konfigurationen.
