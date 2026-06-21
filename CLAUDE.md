# Project Overview
Magnolia CMS 6.4 project. Light modules architecture with Freemarker templates
and JS frontend logic for page rendering.

## Special features
- Travel demo module for sample templates, content type, content app, personalization and site definition.
- Focal image module for sample content with custom imaging generators.
- CDN helper module for managing cache headers and create surrogate keys for purging cache in the CDN

# Conventions
- Parent pom.xml file for Maven project
- Light Modules live in light-modules folder.
- Each module is a subfolder of light-modules folder: light-modules/<module>/
- Modules can include apps, templates, rest endpoints and webhooks.
- Template scripts as Freemarker.
- Definitions as YAML.
- Light Modules are hot reloaded by Magnolia.
- Webapp live in <project>-webapp folder.
- Additional Maven modules can be folders with pom.xml as descriptor file.
- Maven modules need a redeploy.
- CMS content data is stored in repositories folder.
- Micro profile is stored in micro-profiles folder. Where publishing receivers are configured.
- Magnolia properties file is located in webapp folder, WEB-INF/config/default/magnolia.properties file.

# Commands
- rm -rF repositories   # Clean database

# Workflow Rules
- Before editing a template, content type, content app, webhook, rest endpoint or any other definition, check latest Magnolia documentation online.
- Run independent searches in parallel. Multiple concurrent reads are faster than sequential and use context more efficiently.
- Ask before adding dependencies to the parent pom.
- When testing, open a browser to access Magnolia for creating content, reviewing and validating. Use default credentials and http://localhost:8080/<project>-webapp as URL.