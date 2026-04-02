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

## Handcrafted / Generated Form Definition Errors

These errors occur when building .ixt files from scratch (e.g., with Claude Code) rather than exporting from the UI.

| Error Message / Symptom | Trigger | Likely Cause | Resolution |
|---|---|---|---|
| `"Value cannot be null. Parameter name: source"` | Importing a minimal hand-built .ixt | TemplateDefinition missing required top-level elements | Include all top-level elements even if empty: `SchemaVersion`, `ApprovalRuleCategories`, `ApprovalRuleDefinitions`, `AttachmentTypes`, `Attachments`, `ComponentGroupTypes`, `CustomFields`, `Datasources`, `ExternalSourceTypes`, `FormDefinitions`, `Groups`, `ImportanceLevels`, `IntelligentEntries`, `NotificationTemplates`, `PartyTypes`, `QueryIntegrations`, `RequirementGroups`, `ScoreDefinitions`, `ScoreLevelSets`, `TeamRoles`, `Users`, `VirtualTables`, `WorkflowDefinitions` |
| `"Sequence contains no matching element"` (.NET LINQ `.Single()`) | Importing a form with integration references pointing to non-existent entities | `DatasourceIntegrationDefinition` or `IntegrationId` references a datasource/integration ID that doesn't exist in the target tenant | Set all `DatasourceIntegrationDefinition` elements to `i:nil="true"`; set `QueryIntegrations` to empty at both TemplateDefinition and FormDefinition level |
| `"This form definition is invalid and hence is in a draft state"` | Form imports but is marked invalid/draft | One or more of: (1) non-hex question IDs, (2) missing FormDefinition required fields, (3) Section missing `Title`/`TitleExpression`/`RepeatableGroupId`, (4) Page missing `Title`, (5) `DisplayedScoreLevelLabel` empty instead of `i:nil="true"` | See ID format rules and required fields below |

### Required Field Checklist for Hand-Built FormDefinitions

**Question / Page / Section IDs — must be hex format:**
- Pattern: `{11-hex-chars}-{2-3-hex-chars}-{10-hex-chars}` e.g. `196016b4c80-3-c3d4e5f678`
- Page `<d2p1:Id>` and Section `<d2p1:Id>` also require hex format (NOT integers)
- FormDefinition `<Id>` is a plain integer and is fine as-is

**FormDefinition required elements** (all must be present):
```
CancelSectionId (i:nil="true")
DisplayDefaultCommentFieldOnCancel
DisplayedScoreLevelLabel (i:nil="true")  ← must be nil, not empty string
FormRequestSummary > RequestSummaries
Header > DisplayLinkedRequest, HeaderItems, ItemsPerColumn, Visibility
RequestNameQuestion (i:nil="true")
RequestNameTemplate (i:nil="true")
ScoreDefinitionIds
ScoreDefinitions
ScoreDisplayFormat
ScoreMappings
SharePointFolderTemplate (i:nil="true")
```

**Section required elements** (in addition to common fields):
```
<d2p1:Name i:nil="true" />            ← Name should be nil
<d2p1:RepeatableGroupId i:nil="true" />
<d2p1:Title>Section Display Name</d2p1:Title>   ← Title is the visible label
<d2p1:TitleExpression i:nil="true" />
```

**Page required elements:**
```
<d2p1:Title>Page Display Name</d2p1:Title>
```

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
- When building **workflow definitions** from scratch: `ActivityState` and `ConflictSearchState` use `xsi:type="d5p1:..."` where `d5p1 = http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Workflows.States`; `SingleApprovalState` uses `d8p1 = http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Workflows.States.Approvals`. The `states` list element must re-declare `d5p1` namespace since it is a sibling (not descendant) of `InitialState`.
- **Workflow → Form linkage**: `FormDefinitionId` in the WorkflowDefinition must match the `Id` of an existing FormDefinition. Import the form (.ixt) first, then the workflow (.ixt), or include both in the same TemplateDefinition export.
- **ConflictSearchState** requires `ClaimRequiredForActions`, `CourtesyOnly`, `Reviewers` (with at least one `StateReviewer`), `ConflictSearchIntegrationId`, and `RelatedPartiesQuestionId` after `Transitions`.
- **ActivityState for conflicts** uses `CreateConflictsSearchActivity` inside `<d5p1:Activities>` to configure the search term mappings from form questions to conflict search parties.
