# Lab-102-Embedded-Payload-Investigation
## Overview
An embedded payload investigation examines a file to determine whether it contains additional data or another file concealed within its apparent structure. A file may look like a normal document, image, script, or archive while containing appended data, embedded files, executable content, or other unexpected byte sequences.
Attackers may use techniques such as appending data to legitimate files, embedding archives inside other file types, or disguising executable content with misleading extensions. However, finding an unusual file signature or unexpected data does not automatically prove that a file is malicious. Some legitimate formats support embedded objects, metadata, or trailing data.

This lab investigates a controlled text file containing deliberately appended content. PowerShell is used to collect metadata, compare SHA-256 hashes, and inspect binary content. Sysmon and Wazuh are then examined to determine whether endpoint telemetry corroborates the file activity.

The experiment demonstrates evidence-based file analysis without introducing or executing malicious code. The presence of unexpected data is treated as an observation, not automatic proof of malware.

## Lab Objectives

- Understand how to investigate a file that may contain appended or embedded content without executing it.
- Establish a baseline by examining an original reference file and comparing it with a modified sample.
- Use PowerShell to inspect file size, timestamps, file content, and SHA-256 hashes.
- Perform basic hexadecimal and ASCII inspection to identify readable content and appended markers.
- Compare file hashes and metadata to document differences between the original and modified samples.
- Validate whether Sysmon records relevant process creation and file creation activity during controlled testing.
- Investigate Wazuh agent health, Sysmon event-channel monitoring, and available telemetry to assess event visibility.
- Distinguish confirmed findings from assumptions and document any telemetry gaps or collection limitations.
- Preserve evidence integrity and maintain a clear investigation trail.
- Avoid executing unknown content or classifying a file as malicious without sufficient supporting evidence.

## Lab Environment

- Operating System: Windows 11 Pro
- Hostname: DESKTOP-9MMM37V
- PowerShell: 7.6.6
- Endpoint monitoring: Sysmon
- SIEM: Wazuh
- Working directory: C:\EmbeddedPayloadLab
- Evidence directory: C:\EmbeddedPayloadLab\Evidence

## Lab Scenario

A SOC analyst is asked to examine a text file that may contain additional content appended to its original data. The objective is to determine whether the file differs from a known reference, identify any extra content, and assess what the available endpoint telemetry can confirm about the investigation.

The investigation takes place on a Windows 11 endpoint using PowerShell for file analysis, Sysmon for endpoint event monitoring, and Wazuh for log collection and security monitoring. A known reference file is compared with a modified sample containing a deliberately appended test marker. The sample is used to practice evidence examination without executing unknown content or introducing a real malicious payload.

The investigation focuses on the following activities:

- **File examination:** Record file size, timestamps, SHA-256 hashes, and readable content for both files.
- **Content comparison:** Inspect the modified sample using text and hexadecimal views to identify the appended marker and document the differences.
- **Endpoint telemetry validation:** Examine available Sysmon process-creation and file-creation events to determine whether relevant activity was recorded.
- **Wazuh verification:** Check agent health, event-channel monitoring, and available telemetry to investigate potential collection or visibility gaps.
- **Evidence-based assessment:** Separate directly observed findings from unverified assumptions and document any limitations that prevent a definitive conclusion.

The investigation is considered successful when the file differences are documented, the test marker is verified, and the available telemetry is assessed accurately. Missing events are recorded as a visibility limitation rather than proof that an action did not occur. Since the sample contains deliberately appended test text, its structure alone does not establish malicious intent or the presence of executable malware.

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

