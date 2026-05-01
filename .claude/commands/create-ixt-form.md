# Intapp Request Forms — IXT Builder Skill

Use this skill to create new Intapp Request Forms (.ixt) workflows and forms.
**No codebase discovery needed** — all templates and patterns are embedded below.

---

## OVERVIEW

- `.ixt` files = ZIP archives containing a single `definitions.xml`
- Schema version: **4300** (Infor Cloud 2602+)
- Serialization: WCF DataContract XML
- Always define FormDefinitions **before** WorkflowDefinitions
- Cloud rule: `QueryIntegrations` must always be **empty**

---

## STEP-BY-STEP PROCESS

1. Choose new **FormId** and **WorkflowId** (integers; existing: 9001=AML form, 9002=AML workflow)
2. Generate unique hex IDs for every Page, Section, and Question (format below)
3. Write `definitions.xml` using templates below
4. Package: `cd <dir> && zip <name>.ixt definitions.xml`
5. Move `.ixt` to `samples/`
6. Commit and push

---

## HEX ID FORMAT

Pages, Sections, and Questions need IDs in this exact pattern:
```
{11-hex-chars}-{2-hex-chars}-{10-hex-chars}
```
Examples:
```
196016c0100-01-c1d2e3f4a5   ← page
196016c0110-01-c1d2e3f4b6   ← section
196016c0111-11-c1d2e3f4c1   ← question
```
- All characters must be valid hex (0-9, a-f)
- Must be unique across the entire form
- FormDefinition.Id and WorkflowDefinition.Id are plain integers

Suggested block allocation per form to avoid collisions:
- Form 9003 → hex prefix `196016c0...`
- Form 9005 → hex prefix `196016d0...`
- Form 9007 → hex prefix `196016e0...`

---

## NAMESPACE REFERENCE

| Prefix | URI | Used for |
|--------|-----|----------|
| `i` | `http://www.w3.org/2001/XMLSchema-instance` | `i:nil`, `i:type` |
| `z` | `http://schemas.microsoft.com/2003/10/Serialization/` | `z:Id`, `z:Ref` |
| `d2p1` (Forms) | `http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms` | Pages, Sections |
| `d2p1` (Workflows) | `http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Workflows` | WorkflowDefinition |
| `d5p1` | `http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Workflows.States` | State nodes |
| `d6p1` | `http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Workflows.Transitions` | Transitions |
| `d7p1` / `d9p1` / `d11p1` | `http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Conditions` | Conditions (alias varies by nesting level) |
| `d8p1` (Questions) | `http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Questions` | Question elements |
| `d8p1` (Activities) | `http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Workflows.Activities` | Activity elements |
| `d10p1` (arrays) | `http://schemas.microsoft.com/2003/10/Serialization/Arrays` | Dropdown key-value pairs |

> Note: `d2p1` is re-declared with a different URI inside `WorkflowDefinitions`. This is intentional.

---

## FILE SKELETON

```xml
<?xml version="1.0" encoding="utf-8"?>
<TemplateDefinition xmlns:i="http://www.w3.org/2001/XMLSchema-instance"
                    xmlns="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Templates">
  <SchemaVersion xmlns="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model">4300</SchemaVersion>
  <ApprovalRuleCategories xmlns:d2p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Workflows.Approval" />
  <ApprovalRuleDefinitions xmlns:d2p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Workflows.Approval" />
  <AttachmentTypes xmlns:d2p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Common" />
  <Attachments xmlns:d2p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Common" />
  <ComponentGroupTypes xmlns:d2p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Common.Groups" />
  <CustomFields xmlns:d2p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Common" />
  <Datasources xmlns:d2p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Datasources" />
  <ExternalSourceTypes xmlns:d2p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Common" />

  <FormDefinitions xmlns:d2p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms">
    <!-- FORM DEFINITION GOES HERE -->
  </FormDefinitions>

  <Groups xmlns:d2p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Entities" />
  <ImportanceLevels xmlns:d2p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Common" />
  <IntelligentEntries xmlns:d2p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Workflows" />
  <NotificationTemplates xmlns:d2p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Notifications" />
  <PartyTypes xmlns:d2p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Entities" />
  <QueryIntegrations xmlns:d2p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Integrations" />
  <RequirementGroups xmlns:d2p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Requests" />
  <ScoreDefinitions xmlns:d4p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Scores" />
  <ScoreLevelSets xmlns:d4p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Scores" />
  <TeamRoles xmlns:d2p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Entities" />
  <Users xmlns:d2p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Entities" />
  <VirtualTables xmlns:d2p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.DataSets.VirtualTables" />

  <WorkflowDefinitions xmlns:d2p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Workflows">
    <!-- WORKFLOW DEFINITION GOES HERE -->
  </WorkflowDefinitions>
</TemplateDefinition>
```

---

## FORM DEFINITION TEMPLATE

Replace `{FORM_ID}`, `{FORM_NAME}`, `{PAGE_Z_ID}`, `{PAGE_HEX_ID}`, `{PAGE_TITLE}` and insert sections inside `<d2p1:Items>`.

```xml
<d2p1:FormDefinition>
  <Id xmlns="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model">{FORM_ID}</Id>
  <SchemaVersion xmlns="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model">4300</SchemaVersion>
  <d2p1:CancelSectionId i:nil="true" />
  <d2p1:DisplayDefaultCommentFieldOnCancel>false</d2p1:DisplayDefaultCommentFieldOnCancel>
  <d2p1:DisplayedScoreLevelLabel i:nil="true" />
  <d2p1:Draft>false</d2p1:Draft>
  <d2p1:FormRequestSummary><d2p1:RequestSummaries /></d2p1:FormRequestSummary>
  <d2p1:Header>
    <d2p1:DisplayLinkedRequest>false</d2p1:DisplayLinkedRequest>
    <d2p1:HeaderItems />
    <d2p1:ItemsPerColumn>1</d2p1:ItemsPerColumn>
    <d2p1:Visibility>Expanded</d2p1:Visibility>
  </d2p1:Header>
  <d2p1:Name>{FORM_NAME}</d2p1:Name>
  <d2p1:Pages>
    <d2p1:Page z:Id="{PAGE_Z_ID}" xmlns:z="http://schemas.microsoft.com/2003/10/Serialization/">
      <d2p1:ContainerSortOrder>0</d2p1:ContainerSortOrder>
      <d2p1:HideInPrintMode>false</d2p1:HideInPrintMode>
      <d2p1:DisplayCriteria>
        <d2p1:AlwaysHidden>false</d2p1:AlwaysHidden>
        <d2p1:DisplayConditions>
          <d2p1:DisplayModeConditions>
            <d2p1:Conditions xmlns:d9p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Conditions" i:nil="true" />
            <d2p1:DisplayMode>Visible</d2p1:DisplayMode>
          </d2p1:DisplayModeConditions>
        </d2p1:DisplayConditions>
        <d2p1:HideConditions xmlns:d7p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Conditions" i:nil="true" />
        <d2p1:ReadOnlyConditions xmlns:d7p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Conditions" i:nil="true" />
      </d2p1:DisplayCriteria>
      <d2p1:ExpansionCriteria xmlns:d6p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.ExpansionConfiguration">
        <d6p1:ExpansionModeConditions>
          <d6p1:ExpansionModeConditions>
            <d6p1:Conditions xmlns:d9p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Conditions" i:nil="true" />
            <d6p1:ExpansionMode>Collapsed</d6p1:ExpansionMode>
          </d6p1:ExpansionModeConditions>
        </d6p1:ExpansionModeConditions>
      </d2p1:ExpansionCriteria>
      <d2p1:Id>{PAGE_HEX_ID}</d2p1:Id>
      <d2p1:Items>
        <!-- INSERT SECTION COMPONENTS HERE (ContainerSortOrder 0,1,2...) -->
      </d2p1:Items>
      <d2p1:Title>{PAGE_TITLE}</d2p1:Title>
    </d2p1:Page>
  </d2p1:Pages>
  <d2p1:QueryIntegrations xmlns:d4p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Integrations" />
  <d2p1:RequestNameQuestion xmlns:d4p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Questions" i:nil="true" />
  <d2p1:RequestNameTemplate i:nil="true" />
  <d2p1:ScoreDefinitionIds xmlns:d4p1="http://schemas.microsoft.com/2003/10/Serialization/Arrays" />
  <d2p1:ScoreDefinitions xmlns:d4p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Scores" />
  <d2p1:ScoreDisplayFormat>Numeric</d2p1:ScoreDisplayFormat>
  <d2p1:ScoreLevelBadge>
    <d2p1:DisplayCriteria>
      <d2p1:AlwaysHidden>false</d2p1:AlwaysHidden>
      <d2p1:DisplayConditions>
        <d2p1:DisplayModeConditions>
          <d2p1:Conditions xmlns:d8p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Conditions" i:nil="true" />
          <d2p1:DisplayMode>Visible</d2p1:DisplayMode>
        </d2p1:DisplayModeConditions>
      </d2p1:DisplayConditions>
      <d2p1:HideConditions xmlns:d6p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Conditions" i:nil="true" />
      <d2p1:ReadOnlyConditions xmlns:d6p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Conditions" i:nil="true" />
    </d2p1:DisplayCriteria>
  </d2p1:ScoreLevelBadge>
  <d2p1:ScoreMappings xmlns:d4p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Scoring" />
  <d2p1:SharePointFolderTemplate i:nil="true" />
  <d2p1:Subtitle>Subtitle</d2p1:Subtitle>
  <d2p1:Title>{FORM_NAME}</d2p1:Title>
  <d2p1:Variables i:nil="true" />
</d2p1:FormDefinition>
```

---

## SECTION TEMPLATE

Replace `{SECTION_Z_ID}`, `{SORT_ORDER}` (0-based), `{SECTION_HEX_ID}`, `{SECTION_TITLE}`.
Insert questions inside `<d2p1:Questions>`.

```xml
<d2p1:FormComponentItem z:Id="{SECTION_Z_ID}" i:type="d2p1:Section"
                        xmlns:z="http://schemas.microsoft.com/2003/10/Serialization/">
  <d2p1:ContainerSortOrder>{SORT_ORDER}</d2p1:ContainerSortOrder>
  <d2p1:HideInPrintMode>false</d2p1:HideInPrintMode>
  <d2p1:DisplayCriteria>
    <d2p1:AlwaysHidden>false</d2p1:AlwaysHidden>
    <d2p1:DisplayConditions>
      <d2p1:DisplayModeConditions>
        <d2p1:Conditions xmlns:d11p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Conditions" i:nil="true" />
        <d2p1:DisplayMode>Visible</d2p1:DisplayMode>
      </d2p1:DisplayModeConditions>
    </d2p1:DisplayConditions>
    <d2p1:HideConditions xmlns:d9p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Conditions" i:nil="true" />
    <d2p1:ReadOnlyConditions xmlns:d9p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Conditions" i:nil="true" />
  </d2p1:DisplayCriteria>
  <d2p1:DisplayHorizontally>false</d2p1:DisplayHorizontally>
  <d2p1:DoNotShowInPrintView>false</d2p1:DoNotShowInPrintView>
  <d2p1:HideGroupTitle>false</d2p1:HideGroupTitle>
  <d2p1:HideSectionOutline>false</d2p1:HideSectionOutline>
  <d2p1:Id>{SECTION_HEX_ID}</d2p1:Id>
  <d2p1:KeepHorizontalGaps>false</d2p1:KeepHorizontalGaps>
  <d2p1:Name i:nil="true" />
  <d2p1:Questions xmlns:d8p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Questions">
    <!-- INSERT QUESTIONS HERE (ContainerSortOrder 0,1,2...) -->
  </d2p1:Questions>
  <d2p1:RepeatableGroupId i:nil="true" />
  <d2p1:Title>{SECTION_TITLE}</d2p1:Title>
  <d2p1:TitleExpression i:nil="true" />
</d2p1:FormComponentItem>
```

---

## QUESTION TEMPLATES

All questions share this common header block. Replace `{Q_Z_ID}`, `{TYPE}`, `{SORT_ORDER}`, `{Q_HEX_ID}`, `{Q_NAME}`, `{Q_TITLE}` then append the type-specific tail.

```xml
<d8p1:Question z:Id="{Q_Z_ID}" i:type="d8p1:{TYPE}"
               xmlns:z="http://schemas.microsoft.com/2003/10/Serialization/">
  <d2p1:ContainerSortOrder>{SORT_ORDER}</d2p1:ContainerSortOrder>
  <d2p1:HideInPrintMode>false</d2p1:HideInPrintMode>
  <d8p1:AllowLock>false</d8p1:AllowLock>
  <d8p1:DisplayCriteria>
    <d2p1:AlwaysHidden>false</d2p1:AlwaysHidden>
    <d2p1:DisplayConditions>
      <d2p1:DisplayModeConditions>
        <d2p1:Conditions xmlns:d11p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Conditions" i:nil="true" />
        <d2p1:DisplayMode>Visible</d2p1:DisplayMode>
      </d2p1:DisplayModeConditions>
    </d2p1:DisplayConditions>
    <d2p1:HideConditions xmlns:d11p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Conditions" i:nil="true" />
    <d2p1:ReadOnlyConditions xmlns:d11p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Conditions" i:nil="true" />
  </d8p1:DisplayCriteria>
  <d8p1:DisplayWidth>Full</d8p1:DisplayWidth>
  <d8p1:HelpText i:nil="true" />
  <d8p1:Id>{Q_HEX_ID}</d8p1:Id>
  <d8p1:Name>{Q_NAME}</d8p1:Name>
  <d8p1:ParentQuestionId i:nil="true" />
  <d8p1:QuestionDefaultValueSource xmlns:d11p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.DynamicExpressions" i:nil="true" />
  <d8p1:QuestionDefaultValueSourceId i:nil="true" />
  <d8p1:ReadOnly>false</d8p1:ReadOnly>
  <d8p1:RequestCustomFieldKey i:nil="true" />
  <d8p1:RequestFieldType>None</d8p1:RequestFieldType>
  <d8p1:RequirementConditions xmlns:d11p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.RequirementConfiguration" i:nil="true" />
  <d8p1:RequirementCriteria i:nil="true" />
  <d8p1:ShowRequiredIndicator>false</d8p1:ShowRequiredIndicator>
  <d8p1:ShowSpinner>false</d8p1:ShowSpinner>
  <d8p1:Title>{Q_TITLE}</d8p1:Title>
  <d8p1:ValueIntegrationDefinition xmlns:d11p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Questions.Integrations" i:nil="true" />
  <!-- APPEND TYPE-SPECIFIC TAIL BELOW -->
```

### TextInputQuestion tail — SingleLine
`{TYPE}` = `TextInputQuestion`
```xml
  <d8p1:DefaultValue i:nil="true" />
  <d8p1:AlignmentInGridView>Left</d8p1:AlignmentInGridView>
  <d8p1:AllowAlphaCharacters>true</d8p1:AllowAlphaCharacters>
  <d8p1:AllowCustomCharacters>false</d8p1:AllowCustomCharacters>
  <d8p1:AllowNumericCharacters>true</d8p1:AllowNumericCharacters>
  <d8p1:AllowRichTextFormatting>false</d8p1:AllowRichTextFormatting>
  <d8p1:AllowSpecialCharacters>true</d8p1:AllowSpecialCharacters>
  <d8p1:CharacterLimit>0</d8p1:CharacterLimit>
  <d8p1:CustomExpression />
  <d8p1:DefaultFormat />
  <d8p1:TextInputMode>SingleLine</d8p1:TextInputMode>
</d8p1:Question>
```

### TextInputQuestion tail — MultiLine
Same as above but:
```xml
  <d8p1:TextInputMode>MultiLine</d8p1:TextInputMode>
</d8p1:Question>
```

### BooleanRadioButtonInputQuestion tail
`{TYPE}` = `BooleanRadioButtonInputQuestion`
```xml
  <d8p1:DefaultValue i:nil="true" />
  <d8p1:FalseLabel />
  <d8p1:RepeatDirection>Horizontal</d8p1:RepeatDirection>
  <d8p1:TrueLabel />
</d8p1:Question>
```

### DateTimeInputQuestion tail
`{TYPE}` = `DateTimeInputQuestion`
```xml
  <d8p1:DefaultValue i:nil="true" />
  <d8p1:Mode>DateOnly</d8p1:Mode>
  <d8p1:SupportsTimeZone>false</d8p1:SupportsTimeZone>
  <d8p1:ValidationConditions xmlns:d11p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Conditions" i:nil="true" />
  <d8p1:ValidationErrorMessage i:nil="true" />
</d8p1:Question>
```

### DropdownListInputQuestion tail
`{TYPE}` = `DropdownListInputQuestion`
Add one `KeyValueOfstringstring` block per option. Key and Value are always identical.
```xml
  <d8p1:DefaultValue i:nil="true" />
  <d8p1:DatasourceIntegrationDefinition xmlns:d10p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Questions.Integrations" i:nil="true" />
  <d8p1:DefaultDatasource xmlns:d10p1="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <d10p1:KeyValueOfstringstring>
      <d10p1:Key>Option A</d10p1:Key>
      <d10p1:Value>Option A</d10p1:Value>
    </d10p1:KeyValueOfstringstring>
    <d10p1:KeyValueOfstringstring>
      <d10p1:Key>Option B</d10p1:Key>
      <d10p1:Value>Option B</d10p1:Value>
    </d10p1:KeyValueOfstringstring>
  </d8p1:DefaultDatasource>
  <d8p1:ExtraValues xmlns:d10p1="http://schemas.datacontract.org/2004/07/System.Collections.Generic" i:nil="true" />
  <d8p1:ExtraValuesDirection>End</d8p1:ExtraValuesDirection>
  <d8p1:HintText />
</d8p1:Question>
```

---

## WORKFLOW DEFINITION TEMPLATE

Links to the form via `{FORM_ID}`. The state machine is **nested inline** — each state's
transitions contain their destination states as children.

```xml
<d2p1:WorkflowDefinition>
  <Id xmlns="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model">{WORKFLOW_ID}</Id>
  <SchemaVersion xmlns="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model">4300</SchemaVersion>
  <d2p1:CanPutOnHold>false</d2p1:CanPutOnHold>
  <d2p1:DefinitionId>{WORKFLOW_ID}</d2p1:DefinitionId>
  <d2p1:Description i:nil="true" />
  <d2p1:Draft>false</d2p1:Draft>
  <d2p1:FormDefinitionId>{FORM_ID}</d2p1:FormDefinitionId>
  <d2p1:GlobalSubRequestsConfiguration i:nil="true" />
  <d2p1:GroupName i:nil="true" />
  <d2p1:HideFromRequestListFilters>false</d2p1:HideFromRequestListFilters>
  <d2p1:IntelligentEntryIds i:nil="true" />
  <d2p1:MaximumTransitionButtonsCount>5</d2p1:MaximumTransitionButtonsCount>
  <d2p1:Name>{WORKFLOW_NAME}</d2p1:Name>
  <d2p1:NotificationEvents />
  <d2p1:NotificationRedirectionConfiguration>
    <d2p1:DevelopmentRecipients />
    <d2p1:ProductionRecipients />
    <d2p1:RedirectInDevelopment>false</d2p1:RedirectInDevelopment>
    <d2p1:RedirectInProduction>false</d2p1:RedirectInProduction>
    <d2p1:RedirectInTest>false</d2p1:RedirectInTest>
    <d2p1:TestRecipients />
  </d2p1:NotificationRedirectionConfiguration>
  <d2p1:OnHoldById i:nil="true" />
  <d2p1:PutOnHoldAfterInactiveFor i:nil="true" />
  <d2p1:ReminderNotificationEvents />
  <d2p1:RequestCreationConfiguration>
    <d2p1:ClientRequired>false</d2p1:ClientRequired>
    <d2p1:MatterRequired>false</d2p1:MatterRequired>
    <d2p1:ShowClientLookup>true</d2p1:ShowClientLookup>
    <d2p1:ShowMatterLookup>false</d2p1:ShowMatterLookup>
  </d2p1:RequestCreationConfiguration>
  <d2p1:SortOrderNumber>2</d2p1:SortOrderNumber>
  <d2p1:TaskNotificationEvents xmlns:d4p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Workflows.States.Tasks" />
  <d2p1:Version>0</d2p1:Version>
  <d2p1:VisibilityConfiguration>
    <d2p1:HideActivities>false</d2p1:HideActivities>
    <d2p1:HideApprovalRules>true</d2p1:HideApprovalRules>
    <d2p1:HideComments>false</d2p1:HideComments>
    <d2p1:HideFiles>false</d2p1:HideFiles>
    <d2p1:HideFilesOnRequestDetails>false</d2p1:HideFilesOnRequestDetails>
    <d2p1:HideNotes>false</d2p1:HideNotes>
    <d2p1:HidePeople>false</d2p1:HidePeople>
    <d2p1:HideSubRequests>false</d2p1:HideSubRequests>
  </d2p1:VisibilityConfiguration>
  <d2p1:WorkflowDefinitionType>RequestConflictsSearch</d2p1:WorkflowDefinitionType>
  <d2p1:WorkflowThread>
    <d2p1:InitialDecision i:nil="true" />
    <!-- STATE MACHINE GOES HERE — see patterns below -->
    <d2p1:Name i:nil="true" />
    <d2p1:states xmlns:d5p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Workflows.States">
      <!-- One z:Ref entry per state z:Id defined in the machine -->
      <d5p1:WorkflowState z:Ref="{STATE_Z_ID}" xmlns:z="http://schemas.microsoft.com/2003/10/Serialization/" />
    </d2p1:states>
  </d2p1:WorkflowThread>
  <d2p1:notificationTemplates i:nil="true" />
  <d2p1:queryIntegrations i:nil="true" />
</d2p1:WorkflowDefinition>
```

---

## WORKFLOW STATE PATTERNS

### Pattern A — CreateRequest (always first)

```xml
<d2p1:InitialState xmlns:d5p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Workflows.States"
                   z:Id="{STATE_Z_ID}" xmlns:z="http://schemas.microsoft.com/2003/10/Serialization/">
  <d2p1:Name>Create Request</d2p1:Name>
  <d5p1:GroupName i:nil="true" />
  <d5p1:NotificationEvents />
  <d5p1:ReminderNotificationEvents />
  <d5p1:RequirementGroupId i:nil="true" />
  <d5p1:StateType>CreateRequest</d5p1:StateType>
  <d5p1:Transitions xmlns:d6p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Workflows.Transitions">
    <!-- INSERT ONE AUTO TRANSITION HERE pointing to next state -->
  </d5p1:Transitions>
</d2p1:InitialState>
```

### Pattern B — ActivityState (auto gate, no human action)

Used as a passthrough between states. Set `Activities` to empty if no automated task.

```xml
<d6p1:Destination z:Id="{STATE_Z_ID}" i:type="d5p1:ActivityState">
  <d2p1:Name>{STATE_NAME}</d2p1:Name>
  <d5p1:GroupName />
  <d5p1:NotificationEvents />
  <d5p1:ReminderNotificationEvents />
  <d5p1:RequirementGroupId i:nil="true" />
  <d5p1:StateType>Standard</d5p1:StateType>
  <d5p1:Transitions>
    <!-- AUTO transition to next state -->
  </d5p1:Transitions>
  <d5p1:Activities xmlns:d8p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Workflows.Activities" />
</d6p1:Destination>
```

### Pattern C — ConflictSearchState (manual approval gate)

This is the **human review / sign-off state**. No actual conflict search needed — set integration IDs to nil.
Each reviewer can Approve or Cancel.

```xml
<d6p1:Destination z:Id="{STATE_Z_ID}" i:type="d5p1:ConflictSearchState">
  <d2p1:Name>{STATE_NAME}</d2p1:Name>
  <d5p1:GroupName />
  <d5p1:NotificationEvents />
  <d5p1:ReminderNotificationEvents />
  <d5p1:RequirementGroupId i:nil="true" />
  <d5p1:StateType>ConflictSearch</d5p1:StateType>
  <d5p1:Transitions>
    <!-- Approve transition (Green) -->
    <!-- Cancel transition (Red, Destination i:nil="true") -->
  </d5p1:Transitions>
  <d5p1:ClaimRequiredForActions>false</d5p1:ClaimRequiredForActions>
  <d5p1:CourtesyOnly>false</d5p1:CourtesyOnly>
  <d5p1:Reviewers>
    <d5p1:StateReviewer>
      <d5p1:GrantCondition xmlns:d9p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Conditions">
        <AdvancedExpression xmlns="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Expressions" />
        <CollectionType xmlns="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Expressions">All</CollectionType>
        <Conditions xmlns="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Expressions" />
      </d5p1:GrantCondition>
      <d5p1:PrincipalIds xmlns:d9p1="http://schemas.microsoft.com/2003/10/Serialization/Arrays" />
      <d5p1:Reviewer>
        <d2p1:Id>0</d2p1:Id>
        <d2p1:QuestionId i:nil="true" />
        <d2p1:TeamRoleKey i:nil="true" />
        <d2p1:Type>CreatedBy</d2p1:Type>
      </d5p1:Reviewer>
      <d5p1:StateActions>
        <d5p1:StateAction>
          <d5p1:Action>Approve</d5p1:Action>
          <d5p1:Transition>Approve</d5p1:Transition>
        </d5p1:StateAction>
        <d5p1:StateAction>
          <d5p1:Action>Cancel</d5p1:Action>
          <d5p1:Transition>Cancel</d5p1:Transition>
        </d5p1:StateAction>
      </d5p1:StateActions>
    </d5p1:StateReviewer>
  </d5p1:Reviewers>
  <d5p1:ConflictSearchIntegrationId i:nil="true" />
  <d5p1:RelatedPartiesQuestionId i:nil="true" />
</d6p1:Destination>
```

### Pattern D — FinalState (terminal)

```xml
<d6p1:Destination z:Id="{STATE_Z_ID}" i:type="d5p1:FinalState">
  <d2p1:Name>{STATE_NAME}</d2p1:Name>
  <d5p1:GroupName i:nil="true" />
  <d5p1:NotificationEvents />
  <d5p1:ReminderNotificationEvents />
  <d5p1:RequirementGroupId i:nil="true" />
  <d5p1:StateType>Final</d5p1:StateType>
  <d5p1:Transitions />
  <d5p1:Vertices xmlns:d9p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Workflows.Visualization" />
</d6p1:Destination>
```

### Pattern E — Transitions

**Auto transition** (system-driven, no human button):
```xml
<d6p1:WorkflowTransition z:Id="{TRANS_Z_ID}">
  <d2p1:Name>Auto</d2p1:Name>
  <d6p1:CommentRequired>false</d6p1:CommentRequired>
  <d6p1:CommentRequiredHelpText i:nil="true" />
  <d6p1:DelayInterval i:nil="true" />
  <d6p1:Destination><!-- next state here --></d6p1:Destination>
  <d6p1:HideConfirmation>false</d6p1:HideConfirmation>
  <d6p1:Label i:nil="true" />
  <d6p1:ShowAsButton>false</d6p1:ShowAsButton>
  <d6p1:TransitionColor>Green</d6p1:TransitionColor>
  <d6p1:TransitionOrder i:nil="true" />
  <d6p1:TransitionType>Auto</d6p1:TransitionType>
</d6p1:WorkflowTransition>
```

**Approve transition** (green button):
```xml
<d6p1:WorkflowTransition z:Id="{TRANS_Z_ID}">
  <d2p1:Name>Approve</d2p1:Name>
  <d6p1:CommentRequired>false</d6p1:CommentRequired>
  <d6p1:CommentRequiredHelpText i:nil="true" />
  <d6p1:DelayInterval i:nil="true" />
  <d6p1:Destination><!-- next state here --></d6p1:Destination>
  <d6p1:HideConfirmation>false</d6p1:HideConfirmation>
  <d6p1:Label>Complete</d6p1:Label>
  <d6p1:ShowAsButton>false</d6p1:ShowAsButton>
  <d6p1:TransitionColor>Green</d6p1:TransitionColor>
  <d6p1:TransitionOrder i:nil="true" />
  <d6p1:TransitionType>Approve</d6p1:TransitionType>
</d6p1:WorkflowTransition>
```

**Cancel transition** (red button, always nil destination):
```xml
<d6p1:WorkflowTransition z:Id="{TRANS_Z_ID}">
  <d2p1:Name>Cancel</d2p1:Name>
  <d6p1:CommentRequired>false</d6p1:CommentRequired>
  <d6p1:CommentRequiredHelpText i:nil="true" />
  <d6p1:DelayInterval i:nil="true" />
  <d6p1:Destination i:nil="true" />
  <d6p1:HideConfirmation>false</d6p1:HideConfirmation>
  <d6p1:Label i:nil="true" />
  <d6p1:ShowAsButton>false</d6p1:ShowAsButton>
  <d6p1:TransitionColor>Red</d6p1:TransitionColor>
  <d6p1:TransitionOrder i:nil="true" />
  <d6p1:TransitionType>Cancel</d6p1:TransitionType>
</d6p1:WorkflowTransition>
```

---

## DUAL SIGN-OFF WORKFLOW PATTERN (e.g. MNPI dual-CO)

State machine nesting for two sequential manual approvals:

```
CreateRequest (Auto) →
  ActivityState "Pre-Processing" (Auto) →
    ConflictSearchState "Reviewer 1 Sign-Off" (Approve) →
      ActivityState "R2 Gate" (Auto) →
        ConflictSearchState "Reviewer 2 Sign-Off" (Approve) →
          FinalState
        (Cancel) → nil
      (Cancel) → nil
```

z:Id assignment convention (use your own block):
- `i40001` InitialState
- `i40002` Transition: CreateRequest → Gate1
- `i40003` ActivityState Gate1
- `i40004` Transition: Gate1 → Review1
- `i40005` ConflictSearchState Review1
- `i40006` Transition: Review1 Approve → Gate2
- `i40007` Transition: Review1 Cancel → nil
- `i40008` ActivityState Gate2
- `i40009` Transition: Gate2 → Review2
- `i40010` ConflictSearchState Review2
- `i40011` Transition: Review2 Approve → Final
- `i40012` Transition: Review2 Cancel → nil
- `i40013` FinalState

`<d2p1:states>` must reference all 6 state z:Ids: i40001, i40003, i40005, i40008, i40010, i40013.

---

## PACKAGING COMMAND

```bash
cd /path/to/output/dir
zip MyForm-Combined.ixt definitions.xml
mv MyForm-Combined.ixt /path/to/repo/samples/
```

---

## PRE-IMPORT CHECKLIST

- [ ] All Page/Section/Question IDs are hex format (`{11}-{2}-{10}`)
- [ ] FormDefinition.Id and WorkflowDefinition.Id are plain integers
- [ ] Every Section has a non-nil `<d2p1:Title>`
- [ ] `<d2p1:QueryIntegrations>` inside FormDefinition is empty (cloud requirement)
- [ ] All 23 top-level `TemplateDefinition` children present (even if empty)
- [ ] `WorkflowDefinition.FormDefinitionId` matches the form's integer Id
- [ ] `<d2p1:states>` lists every state z:Id defined in the machine
- [ ] No duplicate z:Id values in the document
- [ ] `<d2p1:DisplayedScoreLevelLabel i:nil="true" />` (not empty string)
- [ ] Form imported **before** workflow in same session (or use combined file)

---

## COMMON ERRORS

| Error | Cause | Fix |
|-------|-------|-----|
| "Value cannot be null. Parameter name: source" | Missing top-level TemplateDefinition element | Add all 23 empty collections |
| "This form definition is invalid and in draft state" | Non-hex question/section ID | Use `{11hex}-{2hex}-{10hex}` format |
| "Sequence contains no matching element" | QueryIntegrations references something | Set `<QueryIntegrations />` empty |
| "workflow definition already exists" | Re-importing same workflow ID | Use new integer IDs |
| Import hangs on Finish | Nested workflow dependency timeout | Split form and workflow imports; retry |

---

## QUICK-START: NEW COMPLIANCE FORM

1. Pick IDs: `FormId = 9005`, `WorkflowId = 9006`, hex prefix `196016d0...`
2. Scaffold `definitions.xml` from **FILE SKELETON**
3. Add FormDefinition using **FORM DEFINITION TEMPLATE**
4. Add sections using **SECTION TEMPLATE** (one per compliance gate)
5. Add questions using **QUESTION TEMPLATES** (Boolean for attestations, Dropdown for classifications, DateTime for dates, Text for names/descriptions)
6. Add WorkflowDefinition using **WORKFLOW DEFINITION TEMPLATE**
7. Wire states using **DUAL SIGN-OFF WORKFLOW PATTERN** (or simpler single-review pattern)
8. Run checklist, then package and import
