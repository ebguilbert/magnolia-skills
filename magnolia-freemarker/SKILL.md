---
name: magnolia-freemarker
description: Use when creating Magnolia 6.4 freemarker scripts for templates in light modules. 
  Triggers on requests to "add a page template", "create a page template, "new page template", "add component template", "create component template",
  or any task involving creating FTL files in light-modules/templates/.
allowed-tools: Read, Write, Edit, Grep, Glob
---

# Creating a template script
When creating page or component templates, a template script is usually referenced for rendering HTML using data from the page or component node.

## Freemarker conventions
- Every page template script must include `[@cms.page /]` at the top of the file, before any HTML. This is needed for the page to be editable
- Use ${content.property} for retrieving page or component node properties
- For rendering areas use `[@cms.area name="area-name" /]` where suitable
- Only use `[@cms.component content="node" /]` for areas with template script
- FreeMarker templates use the `[#` syntax as opposed to `<#`. For example use `[#if]` instead of `<#if>`
- Do not use maps or lambdas, use explicit `#list` loops.
- For richTextField content, always use `cmsfn.decode(content)`
