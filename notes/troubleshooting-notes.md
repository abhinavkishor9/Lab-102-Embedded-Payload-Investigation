# Lab 102 — Troubleshooting Notes

## 1. Purpose

This document records the issues encountered while investigating file-creation telemetry and Wazuh event visibility. The troubleshooting process prioritizes evidence collection and preserves the existing lab configuration.

## 2. Sysmon Service Status

### Observation

The Sysmon service query returned:

- Service name: Sysmon64
- State: Running
- Executable: C:\WINDOWS\Sysmon64.exe

### Interpretation

Sysmon was running during the check. A running service does not guarantee that every file operation is captured because event collection depends on the active configuration and filtering rules.

### Recommended Check

Inspect the current configuration using:

& "C:\Windows\Sysmon64.exe" -c

If the command fails, verify the executable path through the Sysmon service configuration. Review the existing rules before making any changes.

## 3. Test File Not Found in Sysmon Event ID 11

### Observation

A controlled file was created at:

C:\Users\Public\WazuhLab\Event11_Forwarding_Test.txt

The search did not return a matching Event ID 11. Subsequent queries showed frequent Event ID 11 records for PowerShell temporary scripts under C:\Windows\SystemTemp.

### Interpretation

Sysmon was generating file-creation events, but the displayed records did not reference the controlled test file. The cause was not established.

Possible explanations include Sysmon filtering rules, the precise operation performed, or an event-search assumption.

### Recommended Check

Search recent Event ID 11 records for the exact filename and time window. If no event appears, inspect the active Sysmon configuration and its file-creation rules before changing anything.

Do not infer that Sysmon is completely broken merely because one test file was not found.

## 4. Historical Wazuh Connection Errors

### Observation

The agent log contained historical errors involving:

- Error 1216: Unable to connect to the manager at 192.168.203.129:1514/tcp.
- Error 1208: Unable to connect to the enrollment service at port 1515.
- Error 1137: Lost connection with manager.
- Warnings indicating that the agent was offline.
- A warning that the message buffer had reached 90 percent.

The Wazuh service was subsequently restarted. Connectivity tests to ports 1514 and 1515 later succeeded.

### Interpretation

The logs demonstrate earlier connectivity instability. They do not prove that the agent remained disconnected after the later successful checks.

A buffer warning at 90 percent does not by itself establish that events were lost.

### Verification

Check the current agent state:

Get-Content "C:\Program Files (x86)\ossec-agent\wazuh-agent.state" |
    Select-String "^status|last_ack|msg_buffer"

A later check returned:

- Status: connected
- Last acknowledgement: 2026-10-10 08:46:22
- Message buffer: 0

This confirms the state reported at that checkpoint. It does not prove that a particular Sysmon event reached the manager.

## 5. Wazuh Sysmon Channel Analysis

### Observation

The agent log contained the following message:

Analyzing event log: 'Microsoft-Windows-Sysmon/Operational'.

### Interpretation

The log shows that the agent was analyzing the Sysmon event channel. It does not prove that every event was collected, forwarded, processed, indexed, or displayed.

### Configuration Check

The intended event-channel configuration is:

<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>

Inspect the existing agent configuration to verify the entry. Do not add a duplicate entry if it already exists.

## 6. Wazuh Alert Visibility

### Observation

The investigation attempted to find Sysmon Event ID 11 records for the controlled sample in Wazuh.

### Interpretation

Not every collected event necessarily triggers an alert. Therefore, the absence of a record in the alerts view does not independently prove that event collection failed.

Agent connectivity, local event collection, manager receipt, indexing, and alert generation are separate stages.

### Recommended Checks

1. Verify the Windows agent's current connection state.
2. Identify the exact Sysmon event and sample filename locally.
3. Search the Wazuh dashboard for the relevant agent and time range.
4. Inspect event details for the filename, path, event ID, and timestamp.
5. Determine whether the dashboard view contains alerts only or other event data.
6. Check the manager logs for relevant connection or processing errors.

Archives were not enabled during this investigation. Keep them disabled unless there is a separate, justified reason to enable them.

## 7. Event Correlation Requirements

Before attributing an event to the experiment, verify that:

- The event identifies the investigated file or relevant process.
- The timestamp aligns with the controlled operation.
- The process details are consistent with the activity performed.
- The event belongs to the correct endpoint.
- The available evidence establishes which monitoring stage was successful.

A general PowerShell event near the test time is not sufficient to prove that the sample was captured.

## 8. Configuration Preservation

During troubleshooting:

- Do not replace the Sysmon configuration without reviewing and preserving the current rules.
- Do not enable Wazuh archives merely to make the test work.
- Do not reinstall the agent based only on historical connection errors.
- Do not treat an empty message buffer as proof of complete ingestion.
- Collect targeted evidence before changing the configuration.

## 9. Current Assessment

Sysmon was running and generating Event ID 1 and Event ID 11 records. The Wazuh agent later reported a connected state with an empty buffer, although historical connection errors were present.

The available results did not establish that Sysmon recorded the controlled sample's file creation or that Wazuh indexed a corresponding event. The remaining issue is sample-specific telemetry correlation, not a confirmed failure of the entire monitoring stack.
