# SPL Queries

These are the main detection queries I wrote after ingesting Sysmon logs from my Atomic Red Team tests.

---

## Find ransom note files created
Atomic test 5 and 10 dropped ransom notes like `README_FOR_DECRYPT.txt`.

```spl
index=ransomware_detection
| rex "<EventID>(?<EventID>\\d+)</EventID>"
| search EventID=11
| rex "<Data Name='TargetFilename'>(?<TargetFilename>[^<]+)</Data>"
| search TargetFilename="*README*" OR TargetFilename="*decrypt*" OR TargetFilename="*locked*"
| table _time, TargetFilename


##Find files created with .akira extension

index=ransomware_detection
| rex "<EventID>(?<EventID>\\d+)</EventID>"
| search EventID=11
| rex "<Data Name='TargetFilename'>(?<TargetFilename>[^<]+)</Data>"
| search TargetFilename="*.akira"
| table _time, TargetFilename

##Find Encryption rules running (GPG4Win & DiskCryptor

index=ransomware_detection
| rex "<EventID>(?<EventID>\\d+)</EventID>"
| search EventID=1
| rex "<Data Name='Image'>(?<Image>[^<]+)</Data>"
| search Image="*gpg*" OR Image="*diskcryptor*" OR Image="*dccon*"
| table _time, Image

##Find PowerShell commands used to drop ransom notes

index=ransomware_detection
| rex "<EventID>(?<EventID>\\d+)</EventID>"
| search EventID=1
| rex "<Data Name='CommandLine'>(?<CommandLine>[^<]+)</Data>"
| search CommandLine="*Out-File*" OR CommandLine="*echo*" OR CommandLine="*README*"
| table _time, CommandLine

##Simple timeline of events over time

index=ransomware_detection
| rex "<EventID>(?<EventID>\\d+)</EventID>"
| timechart count by EventID