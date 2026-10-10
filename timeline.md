# Lab 102 — Investigation Timeline

## Timeline

| Sequence | Activity | Evidence | Assessment |
|---|---|---|---|
| 1 | Created the investigation workspace | C:\EmbeddedPayloadLab\Evidence was created. | Confirmed |
| 2 | Created the reference file | reference.txt measured 38 bytes. | Confirmed |
| 3 | Created the modified sample | embedded-sample.txt measured 66 bytes. | Confirmed |
| 4 | Calculated SHA-256 hashes | The reference and sample hashes differed. | Confirmed |
| 5 | Inspected binary content | The marker EMBEDDED-CONTENT-TEST-102 was found. | Confirmed |
| 6 | Reviewed file signatures | The leading bytes were consistent with ordinary ASCII text. | Confirmed |
| 7 | Queried Sysmon Event ID 1 and Event ID 11 | Both event types appeared in the queried time window. | Confirmed |
| 8 | Searched for the controlled test filename | No matching Event ID 11 was returned for the tested file. | Not observed |
| 9 | Reviewed Wazuh agent logs | Historical connection errors and offline warnings were present. | Confirmed |
| 10 | Restarted the Wazuh agent | The Wazuh service returned to Running state. | Confirmed |
| 11 | Checked the agent state | The agent later reported connected with an empty message buffer. | Confirmed at checkpoint |
| 12 | Assessed SIEM correlation | Ingestion of the sample-specific event was not demonstrated. | Unconfirmed |

## File Evidence

### Reference File

- Path: C:\EmbeddedPayloadLab\Evidence\reference.txt
- Size: 38 bytes
- Creation time in UTC: 2026-10-10 01:53:09
- Last-write time in UTC: 2026-10-10 01:53:09
- SHA-256: FE858628F3027A916262C68D42F78B877EDBEFC64A6D800E89A6792502946D71

### Modified Sample

- Path: C:\EmbeddedPayloadLab\Evidence\embedded-sample.txt
- Size: 66 bytes
- Last-write time in UTC: 2026-10-10 01:54:19
- SHA-256: A423E3D6B043F1B93326BF0E8E6039047DCA950E96D458B26806B247FAB4C5B
- Appended marker: EMBEDDED-CONTENT-TEST-102

## Telemetry Timeline Notes

Sysmon was running and generating file-creation and process-creation records. The displayed Event ID 11 records included PowerShell temporary scripts under C:\Windows\SystemTemp, but they did not establish that the investigated sample was recorded.

The Wazuh agent log contained earlier connection errors involving manager address 192.168.203.129 and TCP port 1514. After the service restart, a later state check reported connected, last_ack='2026-10-10 08:46:22', and msg_buffer='0'.

The acknowledgement timestamp represents agent communication state, not the timestamp of the sample's file-creation event.

## Final Assessment

The investigation confirmed that the modified file contained appended text and had a different SHA-256 hash from the reference file. Sysmon activity and Wazuh connectivity were observed, but the available evidence did not conclusively link a Sysmon event or Wazuh record to the specific sample.

No executable payload, malicious execution, or security compromise was established. The investigation remains conclusive for the controlled file modification and inconclusive for sample-specific end-to-end telemetry correlation.
