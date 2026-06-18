---
name: magnolia-dialogs
description: Use when creating Magnolia 6.4 dialogs for templates in light modules. 
  Triggers on requests to "add a page dialog", "create a page dialog, "new page dialog", "add component dialog", "create component dialog",
  or any task involving creating files in light-modules/dialogs/.
allowed-tools: Read, Write, Edit, Grep, Glob
---

# Creating a dialog

When creating page or component templates, a dialog is usually referenced for entering data into the page or component node.
A dialog requires a form with properties, where each property will be rendered as a form field in the dialog UI.
Dialog YAML properties are the same as Content app detail YAML properties (Check magnolia-content-apps skill)
If in doubt, always check latest docs for Magnolia 6.4 at https://docs.magnolia-cms.com/product-docs/

Always create files in this order:
1. Dialog YAML at `light-modules/<module>/dialogs/<name>.yaml`
2. i18n YAML at `light-modules/<module>/i18n/<name>_dialog_<language>.properties`

## Dialog conventions
- Dialog is composed by properties and layout (optional)
- Property has $type, each type has its own settings, generic settings are
  - `required` validation: true, if set
  - `i18n` setting: true, if set
  - `description`, String if set
- Use layout if there are more than 5 form properties, ask for desired distribution of properties
- Use i18 file for labels and descriptions (if set)

## Dialog example

```yaml
form:
  properties:
    title:
      $type: textField
      required: true
    abstract:
      $type: textField
      rows: 5
    keywords:
      $type: textField
      rows: 3
      i18n: true
    description:
      $type: textField
      rows: 5
      i18n: true
  layout:
    $type: tabbedLayout
    tabs:
      tabMain:
        label: Page Content
        fields:
          - name: title
          - name: abstract
      tabMeta:
        label: Page Meta Data
        fields:
          - name: keywords
          - name: description
```

## Property type examples

### textField
```yaml
title:
  $type: textField
  label: Title
  required: true
  defaultValue: Default text
  placeholder: Enter text...
  description: Help text shown below field
  i18n: true          # Enable translation
  rows: 3             # Multi-line (textarea)
```

### checkBoxField
```yaml
enabled:
  $type: checkBoxField
  label: Enable Feature
  defaultValue: true
  description: Check to enable
```

### comboBoxField (Dropdown)
```yaml
category:
  $type: comboBoxField
  label: Category
  datasource:
    $type: optionListDatasource
    options:
      optionOne:                           # Key (used internally)
        label: Option One Display Label    # Shown to user
        value: option_one                  # Stored value
      optionTwo:
        label: Option Two Display Label
        value: option_two
```

### richTextField
```yaml
content:
  $type: richTextField
  label: Content
  height: 300
```
### dateField
```yaml
eventStart:
  $type: dateField
  label: Event Start
  time: true
  timeFormat: HH:mm:ss
```

### linkField

#### Link to Pages
```yaml
page:
  $type: pageLinkField
  label: Select Page
```

#### Link to DAM Assets
```yaml
image:
  $type: damLinkField
  label: Select Image
```

#### Link to Custom Workspace
```yaml
event:
  $type: linkField
  label: Event
  datasource:
    $type: jcrDatasource
    workspace: events    # Your custom workspace name
```

## i18n properties example
- Replace <module> with light module's name
- Replace <dialog-path> with folder name and file name under dialogs folder. For example, `<dialog-path>=pages.events` for `light-modules/<module>/dialogs/pages/events.yaml`

```properties
#For Dialog's title
<module>.<dialog-path>.label
#For Dialog's tabs
<module>.<dialog-path>.tabs.tabMain.label=Page Content
<module>.<dialog-path>.tabs.tabMeta.label=Page Meta Data

#For Dialog's fields
<module>.<dialog-path>.title.label=Event Title
<module>.<dialog-path>.abstract=Event Abstract
```
