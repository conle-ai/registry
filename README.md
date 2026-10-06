# Conle registry

Plugins and extensions from [Conle](https://conle.ai) for Claude and other agent harnesses.

## Install in the Claude app

1. Open **Customize → Plugins**.
2. Click **Add → Add marketplace → Add from a repository**.
3. Enter `conle-ai/registry` and click **Add**.
4. Open **Discover**, find the plugin you want, and click **Add**.

Updates arrive automatically. There's nothing else to install.

## Install in Claude Code

```
/plugin marketplace add conle-ai/registry
/plugin install <plugin-name>@conle
```

## What's here

| Path | What it is |
|---|---|
| `.claude-plugin/marketplace.json` | The catalog Claude reads. The marketplace is named `conle`. |
| `plugins/<name>/` | One folder per plugin |

Other kinds of extensions, such as connectors and tools, will get their own top-level folders as they're added.

## How releases work

Plugins are published here automatically from private Conle repos whenever a new version is released. Each release is tagged `<plugin>-v<version>`.

Don't edit anything under `plugins/` by hand. The next release overwrites it.

## License

Copyright © 2026 Conle. All rights reserved. See [LICENSE](LICENSE).
