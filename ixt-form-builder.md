# Intapp Request Forms — IXT Builder Reference

**How to use this file:** Attach it to a Claude chat and say something like:
> "Using this reference, create an IXT form and workflow for [your use case]. Output the complete `definitions.xml` content."

Claude should produce a complete `definitions.xml` that you zip into a `.ixt` file:
```bash
zip MyForm.ixt definitions.xml
```
Then import in Intapp under Settings → Templates.

---

## PLATFORM FACTS

- `.ixt` = ZIP archive containing one file: `definitions.xml`
- Schema version: **4300** (Infor Cloud 2602+)
- Serialization format: WCF DataContract XML with namespace prefixes
- **Cloud rule:** `QueryIntegrations` inside FormDefinition must always be empty
- Always list `FormDefinitions` before `WorkflowDefinitions` in the file

---

## ID RULES

| Element | Format | Example |
|---------|--------|---------|
| FormDefinition.Id | Plain integer | `9003` |
| WorkflowDefinition.Id | Plain integer | `9004` |
| Page / Section / Question Id | `{11 hex}-{2 hex}-{10 hex}` | `196016c0100-01-c1d2e3f4a5` |
| z:Id (serialization ref) | `i` + integer | `i30001` |

All hex IDs must be unique within the file. Use a different hex prefix per form to avoid collisions:
- Form 9003 → prefix `196016c0...`
- Form 9005 → prefix `196016d0...`
- Form 9007 → prefix `196016e0...`

---

## NAMESPACE REFERENCE

Declare these as shown — the prefix letter matters because it must match across nested elements.

```
xmlns:i   = http://www.w3.org/2001/XMLSchema-instance
xmlns:z   = http://schemas.microsoft.com/2003/10/Serialization/
xmlns     = http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Templates  (root only)

Inside <FormDefinitions>:
  xmlns:d2p1 = http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms

Inside <WorkflowDefinitions>:
  xmlns:d2p1 = http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Workflows  (re-declared)
  xmlns:d5p1 = http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Workflows.States
  xmlns:d6p1 = http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Workflows.Transitions

Conditions namespace (used at multiple levels, prefix alias varies):
  http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Conditions
  → use d7p1 at page level, d9p1 at section level, d11p1 at question level

Questions namespace (declared on <d2p1:Questions> element):
  xmlns:d8p1 = http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Questions

Dropdown options arrays:
  xmlns:d10p1 = http://schemas.microsoft.com/2003/10/Serialization/Arrays
```

---

## COMPLETE FILE SKELETON

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
    <!-- FORM DEFINITION -->
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
    <!-- WORKFLOW DEFINITION -->
  </WorkflowDefinitions>
</TemplateDefinition>
```

---

## FORM DEFINITION TEMPLATE

```xml
<d2p1:FormDefinition>
  <Id xmlns="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model">FORM_ID</Id>
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
  <d2p1:Name>FORM_NAME</d2p1:Name>
  <d2p1:Pages>
    <d2p1:Page z:Id="PAGE_ZID" xmlns:z="http://schemas.microsoft.com/2003/10/Serialization/">
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
      <d2p1:Id>PAGE_HEXID</d2p1:Id>
      <d2p1:Items>
        <!-- SECTIONS GO HERE -->
      </d2p1:Items>
      <d2p1:Title>PAGE_TITLE</d2p1:Title>
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
  <d2p1:Title>FORM_NAME</d2p1:Title>
  <d2p1:Variables i:nil="true" />
</d2p1:FormDefinition>
```

---

## SECTION TEMPLATE

One `FormComponentItem` per section. `SORT_ORDER` starts at 0.

```xml
<d2p1:FormComponentItem z:Id="SECTION_ZID" i:type="d2p1:Section"
                        xmlns:z="http://schemas.microsoft.com/2003/10/Serialization/">
  <d2p1:ContainerSortOrder>SORT_ORDER</d2p1:ContainerSortOrder>
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
  <d2p1:Id>SECTION_HEXID</d2p1:Id>
  <d2p1:KeepHorizontalGaps>false</d2p1:KeepHorizontalGaps>
  <d2p1:Name i:nil="true" />
  <d2p1:Questions xmlns:d8p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Questions">
    <!-- QUESTIONS GO HERE -->
  </d2p1:Questions>
  <d2p1:RepeatableGroupId i:nil="true" />
  <d2p1:Title>SECTION_TITLE</d2p1:Title>
  <d2p1:TitleExpression i:nil="true" />
</d2p1:FormComponentItem>
```

---

## QUESTION TEMPLATES

All questions share this common header. Append the type-specific tail immediately after.

### Common header (all types)

```xml
<d8p1:Question z:Id="Q_ZID" i:type="d8p1:TYPE"
               xmlns:z="http://schemas.microsoft.com/2003/10/Serialization/">
  <d2p1:ContainerSortOrder>SORT_ORDER</d2p1:ContainerSortOrder>
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
  <d8p1:Id>Q_HEXID</d8p1:Id>
  <d8p1:Name>Q_NAME</d8p1:Name>
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
  <d8p1:Title>Q_TITLE</d8p1:Title>
  <d8p1:ValueIntegrationDefinition xmlns:d11p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Questions.Integrations" i:nil="true" />
  <!-- TYPE TAIL BELOW -->
```

### Tail: TextInputQuestion (single line)
`i:type="d8p1:TextInputQuestion"`
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

### Tail: TextInputQuestion (multi-line)
Same as above but `<d8p1:TextInputMode>MultiLine</d8p1:TextInputMode>`

### Tail: BooleanRadioButtonInputQuestion
`i:type="d8p1:BooleanRadioButtonInputQuestion"`
```xml
  <d8p1:DefaultValue i:nil="true" />
  <d8p1:FalseLabel />
  <d8p1:RepeatDirection>Horizontal</d8p1:RepeatDirection>
  <d8p1:TrueLabel />
</d8p1:Question>
```

### Tail: DateTimeInputQuestion
`i:type="d8p1:DateTimeInputQuestion"`
```xml
  <d8p1:DefaultValue i:nil="true" />
  <d8p1:Mode>DateOnly</d8p1:Mode>
  <d8p1:SupportsTimeZone>false</d8p1:SupportsTimeZone>
  <d8p1:ValidationConditions xmlns:d11p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Forms.Conditions" i:nil="true" />
  <d8p1:ValidationErrorMessage i:nil="true" />
</d8p1:Question>
```

### Tail: DropdownListInputQuestion
`i:type="d8p1:DropdownListInputQuestion"` — Key and Value are always identical strings.
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

```xml
<d2p1:WorkflowDefinition>
  <Id xmlns="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model">WORKFLOW_ID</Id>
  <SchemaVersion xmlns="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model">4300</SchemaVersion>
  <d2p1:CanPutOnHold>false</d2p1:CanPutOnHold>
  <d2p1:DefinitionId>WORKFLOW_ID</d2p1:DefinitionId>
  <d2p1:Description i:nil="true" />
  <d2p1:Draft>false</d2p1:Draft>
  <d2p1:FormDefinitionId>FORM_ID</d2p1:FormDefinitionId>
  <d2p1:GlobalSubRequestsConfiguration i:nil="true" />
  <d2p1:GroupName i:nil="true" />
  <d2p1:HideFromRequestListFilters>false</d2p1:HideFromRequestListFilters>
  <d2p1:IntelligentEntryIds i:nil="true" />
  <d2p1:MaximumTransitionButtonsCount>5</d2p1:MaximumTransitionButtonsCount>
  <d2p1:Name>WORKFLOW_NAME</d2p1:Name>
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
    <!-- STATE MACHINE INLINE HERE -->
    <d2p1:Name i:nil="true" />
    <d2p1:states xmlns:d5p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Workflows.States">
      <!-- List every state z:Id here as z:Ref -->
      <d5p1:WorkflowState z:Ref="i40001" xmlns:z="http://schemas.microsoft.com/2003/10/Serialization/" />
      <d5p1:WorkflowState z:Ref="i40003" xmlns:z="http://schemas.microsoft.com/2003/10/Serialization/" />
    </d2p1:states>
  </d2p1:WorkflowThread>
  <d2p1:notificationTemplates i:nil="true" />
  <d2p1:queryIntegrations i:nil="true" />
</d2p1:WorkflowDefinition>
```

---

## WORKFLOW STATE BUILDING BLOCKS

States are **nested inline** — each transition's `<Destination>` contains the next state as a child element.

### CreateRequest state (always the InitialState)
```xml
<d2p1:InitialState xmlns:d5p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Workflows.States"
                   z:Id="i40001" xmlns:z="http://schemas.microsoft.com/2003/10/Serialization/">
  <d2p1:Name>Create Request</d2p1:Name>
  <d5p1:GroupName i:nil="true" />
  <d5p1:NotificationEvents />
  <d5p1:ReminderNotificationEvents />
  <d5p1:RequirementGroupId i:nil="true" />
  <d5p1:StateType>CreateRequest</d5p1:StateType>
  <d5p1:Transitions xmlns:d6p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Workflows.Transitions">
    <!-- one AUTO transition here -->
  </d5p1:Transitions>
</d2p1:InitialState>
```

### ActivityState (automated passthrough gate)
```xml
<d6p1:Destination z:Id="STATE_ZID" i:type="d5p1:ActivityState">
  <d2p1:Name>STATE_NAME</d2p1:Name>
  <d5p1:GroupName />
  <d5p1:NotificationEvents />
  <d5p1:ReminderNotificationEvents />
  <d5p1:RequirementGroupId i:nil="true" />
  <d5p1:StateType>Standard</d5p1:StateType>
  <d5p1:Transitions>
    <!-- one AUTO transition to next state -->
  </d5p1:Transitions>
  <d5p1:Activities xmlns:d8p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Workflows.Activities" />
</d6p1:Destination>
```

### ConflictSearchState (manual human sign-off)
Use for any state that requires a human to Approve or Cancel.
`ConflictSearchIntegrationId` and `RelatedPartiesQuestionId` are nil — no actual search needed.
```xml
<d6p1:Destination z:Id="STATE_ZID" i:type="d5p1:ConflictSearchState">
  <d2p1:Name>STATE_NAME</d2p1:Name>
  <d5p1:GroupName />
  <d5p1:NotificationEvents />
  <d5p1:ReminderNotificationEvents />
  <d5p1:RequirementGroupId i:nil="true" />
  <d5p1:StateType>ConflictSearch</d5p1:StateType>
  <d5p1:Transitions>
    <!-- Approve + Cancel transitions here -->
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

### FinalState
```xml
<d6p1:Destination z:Id="STATE_ZID" i:type="d5p1:FinalState">
  <d2p1:Name>STATE_NAME</d2p1:Name>
  <d5p1:GroupName i:nil="true" />
  <d5p1:NotificationEvents />
  <d5p1:ReminderNotificationEvents />
  <d5p1:RequirementGroupId i:nil="true" />
  <d5p1:StateType>Final</d5p1:StateType>
  <d5p1:Transitions />
  <d5p1:Vertices xmlns:d9p1="http://schemas.datacontract.org/2004/07/IntApp.Wilco.Model.Workflows.Visualization" />
</d6p1:Destination>
```

### Auto transition
```xml
<d6p1:WorkflowTransition z:Id="TRANS_ZID">
  <d2p1:Name>Auto</d2p1:Name>
  <d6p1:CommentRequired>false</d6p1:CommentRequired>
  <d6p1:CommentRequiredHelpText i:nil="true" />
  <d6p1:DelayInterval i:nil="true" />
  <d6p1:Destination><!-- next state --></d6p1:Destination>
  <d6p1:HideConfirmation>false</d6p1:HideConfirmation>
  <d6p1:Label i:nil="true" />
  <d6p1:ShowAsButton>false</d6p1:ShowAsButton>
  <d6p1:TransitionColor>Green</d6p1:TransitionColor>
  <d6p1:TransitionOrder i:nil="true" />
  <d6p1:TransitionType>Auto</d6p1:TransitionType>
</d6p1:WorkflowTransition>
```

### Approve transition (green button)
```xml
<d6p1:WorkflowTransition z:Id="TRANS_ZID">
  <d2p1:Name>Approve</d2p1:Name>
  <d6p1:CommentRequired>false</d6p1:CommentRequired>
  <d6p1:CommentRequiredHelpText i:nil="true" />
  <d6p1:DelayInterval i:nil="true" />
  <d6p1:Destination><!-- next state --></d6p1:Destination>
  <d6p1:HideConfirmation>false</d6p1:HideConfirmation>
  <d6p1:Label>Complete</d6p1:Label>
  <d6p1:ShowAsButton>false</d6p1:ShowAsButton>
  <d6p1:TransitionColor>Green</d6p1:TransitionColor>
  <d6p1:TransitionOrder i:nil="true" />
  <d6p1:TransitionType>Approve</d6p1:TransitionType>
</d6p1:WorkflowTransition>
```

### Cancel transition (red button, always nil destination)
```xml
<d6p1:WorkflowTransition z:Id="TRANS_ZID">
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

## DUAL SIGN-OFF PATTERN (two sequential approvers)

Use this when two separate people must both approve before completion (e.g. dual compliance officers).
The state machine nests like Russian dolls — each state lives inside the transition of its predecessor.

```
CreateRequest (i40001) --Auto-->
  ActivityState "Gate 1" (i40003) --Auto-->
    ConflictSearchState "Reviewer 1" (i40005)
      --Approve--> ActivityState "Gate 2" (i40008) --Auto-->
                    ConflictSearchState "Reviewer 2" (i40010)
                      --Approve--> FinalState (i40013)
                      --Cancel-->  nil
      --Cancel-->  nil
```

`<d2p1:states>` must include refs to: i40001, i40003, i40005, i40008, i40010, i40013

---

## PACKAGING

```bash
cd /path/containing/definitions.xml
zip MyForm-Combined.ixt definitions.xml
# move to samples/ folder in repo
```

---

## PRE-IMPORT CHECKLIST

- [ ] All Page/Section/Question Ids are hex format: `{11hex}-{2hex}-{10hex}`
- [ ] FormDefinition.Id and WorkflowDefinition.Id are plain integers
- [ ] Every Section has a non-empty `<d2p1:Title>` (not nil)
- [ ] `<d2p1:QueryIntegrations />` inside FormDefinition is empty
- [ ] All 23 top-level TemplateDefinition children are present (even if empty)
- [ ] `WorkflowDefinition.FormDefinitionId` matches the form integer Id
- [ ] `<d2p1:states>` lists every state z:Id defined in the machine
- [ ] No duplicate z:Id values anywhere in the document
- [ ] `<d2p1:DisplayedScoreLevelLabel i:nil="true" />` — must be nil not empty string

---

## COMMON ERRORS

| Error | Cause | Fix |
|-------|-------|-----|
| "Value cannot be null. Parameter name: source" | Missing top-level TemplateDefinition element | Add all 23 empty collections to skeleton |
| "This form definition is invalid and in draft state" | Non-hex ID on section or question | Use `{11hex}-{2hex}-{10hex}` format |
| "Sequence contains no matching element" | QueryIntegrations references a datasource | Set `<QueryIntegrations />` to empty |
| "workflow definition already exists" | Re-importing same workflow integer Id | Increment the Id integers |
| Import hangs on Finish | Nested state dependency timeout | Split into form-only and workflow-only imports |
