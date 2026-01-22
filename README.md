<div align="center">

# Skript Syntax Validator Action

![GitHub Actions](https://img.shields.io/badge/GitHub-Actions-blue?logo=github)
[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](LICENSE)
[![Minecraft](https://img.shields.io/badge/Minecraft-1.21+-green)](https://papermc.io)
[![Skript](https://img.shields.io/badge/Skript-2.9+-orange)](https://github.com/SkriptLang/Skript)
[![Stand With Ukraine](https://raw.githubusercontent.com/vshymanskyy/StandWithUkraine/main/badges/StandWithUkraine.svg)](https://stand-with-ukraine.pp.ua)

A lightweight **Composite GitHub Action** for automated syntax validation of [Skript](https://github.com/SkriptLang/Skript) scripts.<br>
This action runs a minimalistic, "headless" PaperMC server, loads your scripts, and analyzes the logs for syntax errors. Written entirely in **Bash**, making it transparent, fast, and easy to maintain without any Node.js dependencies.

</div>

## ✨ Features

- 🚀 **Fast:** Uses an optimized PaperMC server configuration without GUI.
- 📦 **Addon Support:** Automatically loads `.jar` files (addons) to ensure correct syntax validation.
- 🔧 **Flexible:** Configurable Minecraft version, Skript version, and RAM allocation.
- 🔍 **Detailed Logs:** Outputs precise error lines directly into GitHub Actions annotations.

## 🚀 Quick Start

Add this step to your `.github/workflows/main.yml`:

```yaml
steps:
  - uses: actions/checkout@v4

  - name: Validate Skript Syntax
    uses: Andromedov/skript-tests-action@main
    with:
      minecraft-version: '1.21.10'
      skript-version: '2.14.0'
      path-to-skripts: './scripts'
      path-to-addons: './addons'
```

## ⚙️ Inputs

| Input               | Description                                                                     | Required | Default     |
|---------------------|---------------------------------------------------------------------------------|----------|-------------|
| `minecraft-version` | Minecraft version (PaperMC).                                                    | **Yes**  | `1.21.10`   |
| `skript-version`    | Skript version (GitHub Release Tag).                                            | **Yes**  | `2.14.0`    |
| `path-to-skripts`   | Path to the directory containing your `.sk` files.                              | **Yes**  | `./scripts` |
| `path-to-addons`    | Path to the directory containing addon `.jar` files (e.g., SkBee, skript-yaml). | No       | `./addons`  |
| `server-ram`        | RAM allocated to the test server.                                               | No       | `2G`        |

## 📦 How Addons Work

Since Skript is a dynamic language, the syntax for many plugins (like `skript-yaml`, `SkBee`, or `skript-placeholders`) is registered only when the plugin is enabled on the server.

To ensure your validation passes:
1. Create a folder (e.g., `.github/addons`) in your repository.
2. Place the necessary `.jar` files there.
3. Specify the path to this folder in the `path-to-addons` input.

The Action will automatically copy them to the test server's `plugins` folder before starting.

## 📋 Full Workflow Example

This example demonstrates an optimized workflow that runs validation **only** when script files are modified, saving CI/CD time.

```yaml
name: Skript Tests

on: [push, pull_request]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      # Check if script files have changed
      - name: Check filters
        uses: dorny/paths-filter@v3
        id: filter
        with:
          filters: |
            skript:
              - 'scripts/**/*.sk'
              - '.github/addons/*.jar'

      # Run validator only if changes are detected
      - name: Run Skript Validator
        if: steps.filter.outputs.skript == 'true'
        uses: ParadiseCraft/skript-bash-action@main
        with:
          minecraft-version: '1.21.10'
          skript-version: '2.14.0'
          path-to-skripts: './scripts'
          path-to-addons: '.github/addons'
          server-ram: '3G'
```

## 📝 License

Distributed under the **Apache License 2.0**. See [LICENSE](LICENSE) for more information.