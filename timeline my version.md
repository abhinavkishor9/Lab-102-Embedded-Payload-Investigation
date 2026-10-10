# Investigation Timeline

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

