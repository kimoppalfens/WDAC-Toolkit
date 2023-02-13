| [docs](..)  / [using](.) / advanced-hunting.md
|:---|

# Creating a policy from MDE Advanced Hunting events 

This document outlines the steps to create a WDAC (CI) policy from the Microsoft Defender for Endpoint (MDE) Advanced Hunting events exported as a CSV file. 

## 1. Export the MDE AH Events as CSV ##

The WDAC Wizard requires the CSV file in a very specific format. The MDE AH query must follow exactly this format:

```kql
DeviceEvents 
| where ActionType startswith 'AppControlCodeIntegrity' 
// SigningInfo Fields
| extend IssuerName = parsejson(AdditionalFields).IssuerName
| extend IssuerTBSHash = parsejson(AdditionalFields).IssuerTBSHash
| extend PublisherName = parsejson(AdditionalFields).PublisherName
| extend PublisherTBSHash = parsejson(AdditionalFields).PublisherTBSHash
// Audit/Block Fields
| extend AuthenticodeHash = parsejson(AdditionalFields).AuthenticodeHash
| extend PolicyId = parsejson(AdditionalFields).PolicyID
| extend PolicyName = parsejson(AdditionalFields).PolicyName
// Keep only actionable info for the Wizard
| project Timestamp,DeviceId,DeviceName,ActionType,FileName,FolderPath,SHA1,SHA256,IssuerName,IssuerTBSHash,PublisherName,PublisherTBSHash,AuthenticodeHash,PolicyId,PolicyName
```

The Wizard is expecting **exactly the following data fields**:
- Timestamp
- DeviceId
- DeviceName 
- ActionType
- FileName
- FolderPath
- SHA1
- SHA256
- IssuerName
- IssuerTBSHash
- PublisherName
- PublisherTBSHash
- AuthenticodeHash
- PolicyId
- PolicyName

CSV files without the required fields, or with additional fields, may fail to properly parse. 

Select the export button to extract the MDE AH events to a csv file.

The csv created in this way can then be used in the Windows Defender Application control wizard.

To achieve this, launch the wizard and on the Home screen select Policy Editor.
Switch the radio button to Convert Event Log to a WDAC Policy and find the option *Parse MDE Advanced Hunting Events to Policy*.
Click the *Parse Log File(s)* button and browse to your csv file, click Open.
You should receive a message box stating that the WDAC Event logs were parsed.

When you click next you get the option to review the Policy suggestions and can use the *Add Allow Rule* button to add the suggestions into your policy.

