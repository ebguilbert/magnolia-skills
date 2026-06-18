---
name: magnolia-content-apps
description: Use when creating a new Magnolia 6.4 light-module content app. 
  Triggers on requests to "add a content app", "create a content app", "new content type", "add content type"
  or any task involving creating files in light-modules/apps/ or light-modules/contentTypes/.
allowed-tools: Read, Write, Edit, Grep, Glob
---

# Creating a Magnolia 6.4 content app

When using !content-type: shorthand, the system auto-generates an app definition from the content type model.
A content app requires a content type definition for the model, app definition for the app and decoration for the app location in admin central UI.
If in doubt, always check latest docs for Magnolia 6.4 at https://docs.magnolia-cms.com/product-docs/

Always create files in this order:
1. Content type YAML at `light-modules/<module>/contentTypes/<name in singular>.yaml`
2. App YAML at `light-modules/<module>/apps/<name in plural>.yaml`
3. i18n YAML at `light-modules/<module>/i18n/<name in singular>_app_<language>.properties`
3. AdminCentral decoration YAML at `light-modules/<module>/decorations/admincentral/config.yaml`

## Content type conventions
- Content type is composed by datasource and model
- Datasource is JCR by default, it only requires a workspace name, which is auto created by default
- Always register a nodeType for the model, using the name of contentType
- All model properties become form properties in the detail subapp in the content app
- Model property has always a name and optionally a type
- Property types can be: 
  - Any of the supported JCR property types: String, Date, etc. String is default, omit it.
  - richText creates a Rich text field 
  - asset creates a Link field to choose an asset 
  - Submodel name 
  - Any other content type name
- Property has an optional required validation: true, if set
- Property has an i18n setting: true, if set
- Property can be multiple: true, if set


### Content type example:

```yaml
# contentTypes/event.yaml
datasource:
  workspace: events
  autoCreate: true

model:
  nodeType: event          # REQUIRED: always register an explicit node type
  properties:
    - name: title
      required: true       # Validation belongs on content type
      i18n: true           # i18n belongs on content type
    - name: description
    - name: location
    - name: date
      type: Date           # Only declare type when NOT String (String is default)
    - name: eventType
      options:             # Options for comboBox-like fields belong on content type
        conference:
          label: Conference
          value: conference
        workshop:
          label: Workshop
          value: workshop
    - name: active
      type: Boolean
```
## Content app conventions
- !content-type is not a map property, use it like: `!content-type:<name in singular>` (no space used)
- Always use browser and detail as subApps
- Create a form property called `name` to hold the content type name
- Use layout if there are more than 5 form properties, ask for desired distribution of properties
- Use i18 file for labels
- Two default filters for browser/workbench: `jcrPublishingStatus` and `mgnl:createdBy`

### Content app example:

```yaml
# apps/events.yaml
!content-type:event
name: events
label: Events
icon: icon-items

subApps:
  detail:
    form:
      properties:
        description:
          rows: 3                # Display customization
        date:
          time: true             # dateField enhancement
          dateFormat: yyyy-MM-dd
          timeFormat: HH:mm:ss

      layout:                    # Optional: organize into tabs
        $type: tabbedLayout
        tabs:
          - name: main
            label: Event
            fields:
              - name: name
              - name: title
              - name: description
              - name: eventType
          - name: details
            label: Details
            fields:
              - name: location
              - name: date
              - name: active

  browser:
    actions:
      activate:
        $type: openDialogAction
        dialogId: workflow-pages:publish
        catalog: workflow
        command: activate
      editItem:
        label: Edit
        icon: icon-edit
        $type: openDetailSubappAction
        viewType: edit
        appName: events-app
        subAppName: detail
        availability:
          writePermissionRequired: true
          nodeTypes:
            - event

    actionbar:
      defaultAction: editItem
      sections:
        - name: item
          availability:
            nodeTypes:
              - event
          groups:
            - name: editGroup
              items:
                - name: editItem
            - name: activationActions
              items:
                - name: activate

    workbench:
      contentViews:
        tree:
          columns:
            - name: name           # Column name = JCR property 
              expandRatio: 0.5
              nodeTypeToComponents:  # Icons 
                event:
                  icon: icon-datepicker
                  showPath: true
                mgnl:folder:
                  icon: icon-folder
                  showPath: true

            - name: location
              expandRatio: 0.5

            - name: date
              $type: dateColumn     # Only needed for non-String types
              expandRatio: 0.5

            - name: eventType
              width: 120
```

### Content app column Types

| `$type` | Use Case |
|---------|----------|
| `dateColumn` | Formatted date/time |
| `iconAndValueColumn` | Value with icon |
| `iconColumn` | Icon only |
| `statusColumn` | Publication status |
| `componentColumn` | Generic property display |
| `pathColumn` | JCR path |


### Content app Action Types

| Action Type | Use Case |
|------------|----------|
| `openDetailSubappAction` | Open edit form for content |
| `deleteNodesConfirmationAction` | Delete with confirmation |
| `openDialogAction` | Open a modal dialog |
| `jcrCommandAction` | Execute JCR command |
| `exportAction` | Export content |
| `importAction` | Import content |
| `copyContentAction` | Copy to clipboard |
| `pasteContentAction` | Paste from clipboard |
| `cutContentAction` | Cut to clipboard |
| `duplicateNodeAction` | Duplicate content |
| `addNodeAction` | Add child node |
| `markAsDeletedAction` | Soft delete |
| `restoreJcrVersionAction` | Restore version |
| `chainedAction` | Multiple actions in sequence |
| `confirmationAction` | Action with confirmation |

### Content app browser actionbar availability examples

- By Node Type
```yaml
availability:
  nodeTypes:
    - event
    - mgnl:folder
```

- For Root Only
```yaml
availability:
  root: true
```

- Write Permission Required
```yaml
availability:
  writePermissionRequired: true
```

### Content app browser search configuration example 

```yaml
    workbench:
      sortFilterOptions: true       # Sort filters alphabetically (default: true)
      searchEnabled: true           # Enable/disable search (default: true)
      searchPlaceholder: 'Type here to search...'
```

### Content app browser custom filters example

```yaml
    workbench:
      filters:
        - name: eventType
          label: Event Type
          icon: icon-datepicker
          filterOperator: STARTS_WITH
          $type: dropdownMultiselect
          propertyName: eventType
          datasource:
            $type: optionListDatasource
            options:
              workEvent:
                value: workEvent
                label: Work Event
              holiday:
                value: holiday
                label: Holiday
```

### Content app browser column filtering example

```yaml
  columns:
    - name: location
      expandRatio: 1.0
      filterComponent:
        $type: textField
```

### Content app browser column inline editing example

```yaml
  columns:
    - name: location
      expandRatio: 1.0
      editable: true
      editor:
        availability:
          nodeTypes:
            - event
        field:
          $type: textField
          rows: 3
```

## i18n properties example

```properties
#For App's search bar
events.browser.workbench.searchPlaceholder: Search in events

#For App's tabs
events.tabs.main.label: Event
events.tabs.detail.label: Details

#For Browser SubApp
events.browser.views.name.label: Event Name
events.browser.views.date.label: Event Date
events.browser.views.location.label: Event Location
events.browser.views.eventType.label: Event Type
events.browser.views.activationStatus.label: Status

#For Detail SubApp - The Form
events.name.label: Event Name
events.location.label: Event Location
events.description.label: Event Description
events.eventType.label: Event Type
events.date.label: Event Date
```

## Admin central decoration example

```yaml
# decorations/admincentral/config.yaml
layout:
  groups:
    - name: content
      apps:
        - name: events
```
