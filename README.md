# Lab 102 — Embedded Payload Investigation

## Overview

This lab investigates a controlled text file containing deliberately appended content. PowerShell is used to collect metadata, compare SHA-256 hashes, and inspect binary content. Sysmon and Wazuh are then examined to determine whether endpoint telemetry corroborates the file activity.

The experiment demonstrates evidence-based file analysis without introducing or executing malicious code. The presence of unexpected data is treated as an observation, not automatic proof of malware.

## Objectives

- Create a reference file and a modified sample.
- Collect file paths, sizes, and timestamps.
- Calculate and compare SHA-256 hashes.
- Inspect binary content and identify an appended marker.
- Understand the limitations of file signatures and magic numbers.
- Review Sysmon Event ID 1 and Event ID 11.
- Investigate Wazuh agent connectivity and event visibility.
- Document findings, troubleshooting steps, and telemetry limitations.

## Lab Environment

- Operating System: Windows 11 Pro
- Hostname: DESKTOP-9MMM37V
- PowerShell: 7.6.6
- Endpoint monitoring: Sysmon
- SIEM: Wazuh
- Working directory: C:\EmbeddedPayloadLab
- Evidence directory: C:\EmbeddedPayloadLab\Evidence

## Scenario

An analyst needs to determine whether a file contains additional content beyond its apparent text structure. A known reference file is created, copied, and modified by appending a recognizable marker. The analyst compares both files and examines available endpoint telemetry.

The investigation focuses on verifying file contents and determining what can be established from the evidence. It does not assume that appended data represents executable code or malicious activity.

## Investigation Workflow

1. Create the investigation workspace.
2. Generate the reference file and record its metadata.
3. Append a harmless marker to a copy of the reference file.
4. Compare file sizes and SHA-256 hashes.
5. Inspect the sample's hexadecimal representation.
6. Examine file signatures and structural limitations.
7. Search Sysmon for relevant process and file-creation events.
8. Check Wazuh for corresponding endpoint telemetry.
9. Preserve evidence and document unresolved questions.

## Key Findings

- The reference file was 38 bytes.
- The modified sample was 66 bytes.
- The reference SHA-256 hash was FE858628F3027A916262C68D42F78B877EDBEFC64A6D800E89A6792502946D71.
- The sample SHA-256 hash was A423E3D6B043F1B93326BF0E8E6039047DCA950E96D458B26806B247FAB4C5B.
- Binary inspection confirmed the marker EMBEDDED-CONTENT-TEST-102.
- Sysmon was running and generating Event ID 1 and Event ID 11 records.
- The displayed Sysmon records did not conclusively identify the controlled sample.
- The Wazuh agent later reported a connected state and an empty message buffer.
- End-to-end Wazuh ingestion of the sample's specific event was not confirmed.

## Evidence Artifacts

- reference.txt: Baseline file.
- embedded-sample.txt: Modified file containing the appended marker.
- Hash-Comparison.txt: Saved hash comparison.
- Investigation-Evidence.txt: File metadata and hash record.
- Sysmon events: Potential process and file-creation evidence.
- Wazuh records: Potential corroborating endpoint telemetry.

## Findings and Limitations

The sample contains additional text, and its hash differs from that of the reference file. These observations confirm that the files have different contents.

The experiment does not establish the presence of executable malware, payload execution, or a security incident. Sysmon activity and Wazuh agent connectivity were observed, but the available evidence did not conclusively demonstrate that the specific sample's file-creation event was collected and indexed by Wazuh.

## Conclusion

The investigation confirmed appended content through file-size comparison, SHA-256 hashing, and binary inspection. It also demonstrated the importance of separating local event generation from successful SIEM ingestion. The file modification is confirmed, while maliciousness and sample-specific telemetry correlation remain unestablished.

## Skills Demonstrated

- File integrity verification
- SHA-256 hashing
- PowerShell forensic analysis
- Binary and hexadecimal inspection
- Sysmon event analysis
- Wazuh troubleshooting
- Evidence preservation
- Evidence-based investigation
