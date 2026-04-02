# Intapp .ixt Import – Common Errors & Resolutions

> Sourced from Slack: #open-foo-questions, #open-cloud-escalation, #support-open
> Use as context for Claude Code when parsing, generating, or importing .ixt form/workflow definition files.

---

## Import-Time Errors

| Error Message / Symptom | Trigger | Likely Cause | Resolution |
|---|---|---|---|
| `"workflow definition already exists"` on re-import | Re-importing after a failed or invisible first import | Partial import left orphaned DB records; file GUID already registered | Requires DB cleanup of orphaned workflow definition records before re-import; do not re-import same GUID without cleanup |
| Import completes with no error, but workflow invisible in UI | Sandbox → Production import; cloud tenants | Tenant `WorkflowPackageType` not set to correct option for that workflow type | DBA must set `WorkflowPackageType` (e.g., `Option2`) on tenant; ref: FOO-20416, CNG-32583 |
| `System.Exception: A timeout error occurred` on Finish button | Large workflows (parent + 2+ child workflows); on-prem → cloud migration | Dependency graph save times out under load or resource mismatch between tenants | Retry import; split parent/child workflows into separate imports; verify tenant resource allocation matches other tenants where import succeeds |
| Exception on Finish button click (integration-related) | On-prem → cloud migration | Workflow references an integration that doesn't exist or can't be resolved in the target environment | Audit all integrations in the .ixt before import; ensure every referenced integration exists in the target tenant |
| `"datasets are not deserialized from this export file"` | Importing form definitions with datasets | Export file is corrupt or incomplete; datasets block malformed | Re-export the form from source; verify datasources table: `SELECT * FROM Datasources`; check for missing/null dataset entries in the file |
| Form definitions import fine but workflow definitions do not appear | Any environment | Form and workflow definitions are separate entities; workflow may depend on request type config not present in target | Confirm request types (Client, Matter, etc.) exist in target; import form definitions first, then workflow definitions |
| Cannot import cloud-exported workflow to on-prem | Cloud 2511 → On-prem 2508 | Version schema mismatch — cloud versions are ahead of on-prem | Not supported; import must go from equal or lower version to equal or higher version only |
| Import hangs / "Update test" button resets with no change | All workflows in a tenant affected; browser-independent | Backend DB lock or long-running transaction blocking workflow save | Check for DB locks; attach web.log and escalate as FOO ticket |

---

## Form Definition Structure Errors

| Error Message / Symptom | Trigger | Likely Cause | Resolution |
|---|---|---|---|
| `"The question 'x' has invalid integration: unable to execute the query"` | Request creation after import; 2602+ | Dataset/integration reference in form question is broken or query is malformed | Check dataset linked to question; re-validate query; tracked under NBI-57596 |
| Default value integration returns original value after user edit (API only) | On-prem v2021.1.9; textbox with query integration default | Bug: `GET /api/intake/v1/requests/{requestId}` returns integration value, not edited value | Move dependency questions into the same section as their source question; fixed in later NBI release |
| `System.ArgumentException: An item with the same key has already been added` | `UpdateFormAnswerAction` on specific requests | Duplicate key in form answer collection — likely duplicate question ID/name in form definition | Check form definition for duplicate question identifiers; re-export clean form definition |
| Inner join case sensitivity mismatch | On-prem → Cloud migration | On-prem datasets: case-insensitive joins. Cloud datasets: case-sensitive joins | Normalize casing in dataset join keys before migration; update form definition datasets accordingly |
| Custom field not populated when question value set automatically | Form with label (Q1) + text question (Q2) using Q1 as input parameter | Automatic population of Q2 doesn't trigger custom field assignment; only manual edit triggers it | Known behavior limitation; workaround: add explicit trigger or event on Q2; file FOO if business-critical |

---

## Notes for Claude Code

- `.ixt` files are **binary/compressed** — not directly readable as XML/JSON. Attempt zlib/gzip decompression first.
- If decompressed, look for `WorkflowPackageType`, `DataSources`, and integration reference blocks as common failure points.
- GUID uniqueness is critical — duplicate GUIDs across import attempts cause phantom "already exists" errors.
- Always import **form definitions before workflow definitions** when both are present.
- Cloud ↔ On-prem imports are **not bidirectional** — higher version exports cannot go to lower version targets.
