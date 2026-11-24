---
name: quark-ui
description: Use Quark's component library and design tokens. Use when building or adapting a web interface with Quark components or --q-* tokens.
license: MIT
---

# Quark UI

Use this skill when a user asks to build an interface with Quark or adapt a Quark component to an existing project.

1. Inspect the project's framework, existing components, styling conventions, and accessibility requirements before making changes.
2. Consult https://design.sik.limited/llms.txt and https://design.sik.limited/registry.json to find the relevant components and current library guidance. Open the component detail page at https://design.sik.limited/en/components/{id}/ for its preview and implementation guidance.
3. Choose the example for the project's framework. Adapt it to the application's actual state, behavior, and conventions; examples are starting points, not a substitute for checking the existing code. For Tabs, follow the specification on its detail page.
4. Use the project's existing design tokens where appropriate. If using Quark's examples, account for the shared stylesheet at https://design.sik.limited/preview.css, its token variables, the q-preview wrapper, fonts, and any icon dependencies. Browser-local studio settings are not available to you unless the user supplies exported tokens or a copied reference.
5. Verify the result in context, including keyboard interaction, accessible names, responsive layout, and relevant project checks. Explain any dependencies or integration decisions.
