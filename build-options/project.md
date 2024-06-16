# --project

This option is for Monorepos.&#x20;

You can run every subcommand of the `keycloakify` CLI tool from the root of your Keycloakify project using the `--project` (or `-p`) option. Example with the `build` command:

```bash
npx keycloakify build -p <path>
```

`<path>` would be typically something like `packages/keycloak-theme`

{% content-ref url="../keycloakify-in-my-app/as-a-subproject-of-your-monorepo.md" %}
[as-a-subproject-of-your-monorepo.md](../keycloakify-in-my-app/as-a-subproject-of-your-monorepo.md)
{% endcontent-ref %}