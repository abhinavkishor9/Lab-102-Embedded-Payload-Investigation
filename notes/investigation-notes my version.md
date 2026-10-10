# Investigation Notes

## 1. Workspace Preparation

The workspace was created at C:\EmbeddedPayloadLab, with an evidence directory at C:\EmbeddedPayloadLab\Evidence.

The main artifacts were:

- reference.txt
- embedded-sample.txt
- Hash-Comparison.txt
- Investigation-Evidence.txt

The dedicated workspace separated the experiment from unrelated lab activities.

## 2. Reference File Analysis

The reference file contained the text:

This is the original reference file.

Observed metadata:

- Path: C:\EmbeddedPayloadLab\Evidence\reference.txt
- Size: 38 bytes
- Creation time in UTC: 2026-10-10 01:53:09
- Last-write time in UTC: 2026-10-10 01:53:09
- SHA-256: FE858628F3027A916262C68D42F78B877EDBEFC64A6D800E89A6792502946D71

These values establish the baseline for comparison.

## 3. Modified Sample Analysis

The reference file was copied to embedded-sample.txt. The following marker was appended:

EMBEDDED-CONTENT-TEST-102

Observed metadata showed that the sample was 66 bytes, compared with 38 bytes for the reference. The 28-byte difference is consistent with the appended marker and newline characters.

This confirms a controlled file modification. It does not establish that the sample contains executable code or malware.

## 4. SHA-256 Comparison

Reference file:

- Filename: reference.txt
- Size: 38 bytes
- SHA-256: FE858628F3027A916262C68D42F78B877EDBEFC64A6D800E89A6792502946D71

Modified sample:

- Filename: embedded-sample.txt
- Size: 66 bytes
- SHA-256: A423E3D6B043F1B93326BF0E8E6039047DCA950E96D458B26806B247FAB4C5B

The hashes differ, confirming that the files have different contents. A hash comparison alone does not explain the difference or establish maliciousness.

The results were saved in Hash-Comparison.txt.

## 5. Binary Inspection

The sample was read as bytes using PowerShell's ReadAllBytes method and displayed in hexadecimal form.

The first bytes were:

54-68-69-73-20-69-73-20-74-68-65-20-6F-72-69-67

These bytes correspond to the beginning of the ASCII text This is the orig.

The sample was also converted to ASCII and searched for the marker. The Contains check returned True for EMBEDDED-CONTENT-TEST-102.

The marker's presence in the file content is confirmed. The inspection did not establish the presence of an executable payload.

## 6. File-Signature Assessment

The sample was created by appending text to a text file. It was not deliberately constructed as an executable or archive.

The observed leading bytes were consistent with ASCII text rather than the ZIP local-file signature 50 4B 03 04.

File signatures can help identify candidate formats, but matching bytes alone do not prove that a complete embedded file exists. Additional structural and format-specific validation would be required to confirm a genuine embedded archive or executable.

No embedded executable or archive was established in this lab.

## 7. Sysmon Telemetry Review

The investigation queried Microsoft-Windows-Sysmon/Operational for Event ID 1 and Event ID 11.

Event ID 1 can provide process creation details. Event ID 11 can record file-creation activity when the applicable configuration captures it.

The query returned both event types. A subsequent review of recent Event ID 11 records showed repeated PowerShell temporary scripts under C:\Windows\SystemTemp.

The displayed events did not establish that Sysmon recorded the specific file C:\EmbeddedPayloadLab\Evidence\embedded-sample.txt.

The existence of other Sysmon events cannot be treated as proof that the sample's creation was captured.

## 8. Wazuh Telemetry Review

The Windows Wazuh agent was restarted during troubleshooting. A later state check returned:

- Status: connected
- Last acknowledgement: 2026-10-10 08:46:22
- Message buffer: 0

The agent log also showed that it was analyzing Microsoft-Windows-Sysmon/Operational.

Earlier log entries contained connection errors involving manager address 192.168.203.129 and TCP port 1514, along with offline warnings.

These observations show that connectivity problems occurred earlier and that the agent later reported a connected state. They do not prove that the specific sample event reached the manager or became searchable in Wazuh.

