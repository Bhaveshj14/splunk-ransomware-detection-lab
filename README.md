# 🛡️ Ransomware Detection Lab in Splunk

This project is a simple blue team detection lab to see how simulated ransomware activity shows up in logs, and to practice writing SPL queries in Splunk.

---

## 🔧 What I built
- Deployed a Windows VM with Sysmon installed for detailed process, file, and registry logging.
- Used **Atomic Red Team** to safely simulate ransomware attacks:
  - 🧪 Test 5: PureLocker ransom note (drops ransom note file)
  - 🧪 Test 8: Encrypt files with GPG4Win
  - 🧪 Test 9: Encrypt files with DiskCryptor
  - 🧪 Test 10: Drop `.akira` extension files and ransom note
- Collected over **3,000 Sysmon logs**, exported as XML.
- Ingested data into Splunk (`ransomware_detection` index).

---

## 🔍 What I detected
✅ Ransom note files created:
- `README_FOR_DECRYPT.txt`, `YOUR_FILES_ARE_LOCKED.txt`

✅ Files created with suspicious extension:
- `.akira` files dropped by Akira test

✅ Encryption tools executed:
- `gpg.exe` (GPG4Win)
- DiskCryptor tools

✅ PowerShell commands to create ransom notes:
- Using `echo` or `Out-File`

---

## 📊 What I built in Splunk
- Timechart of EventIDs over time
- Top suspicious commands and processes
- List of created ransom note files and `.akira` files

---

## 🧪 How I ran the Atomic tests
```powershell
# Clone Atomic Red Team
git clone https://github.com/redcanaryco/invoke-atomicredteam.git
cd atomic-red-team

# Import test runner
Set-ExecutionPolicy Bypass -Scope Process -Force
Import-Module .\atomics\Invoke-AtomicRedTeam\Invoke-AtomicTest.psm1

# Show available tests under T1486
Invoke-AtomicTest T1486 -ShowDetailsBrief

# Run tests
Invoke-AtomicTest T1486 -TestNumbers 5
Invoke-AtomicTest T1486 -TestNumbers 8
Invoke-AtomicTest T1486 -TestNumbers 9
Invoke-AtomicTest T1486 -TestNumbers 10
