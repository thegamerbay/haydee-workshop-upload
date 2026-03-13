# Haydee Workshop Upload Action

[![GitHub release (latest by date)](https://img.shields.io/github/v/release/thegamerbay/haydee-workshop-upload)](https://github.com/thegamerbay/haydee-workshop-upload/releases)
[![Lint Workflows and Action](https://github.com/thegamerbay/haydee-workshop-upload/actions/workflows/lint.yml/badge.svg)](https://github.com/thegamerbay/haydee-workshop-upload/actions/workflows/lint.yml)
[![Update Major Tag](https://github.com/thegamerbay/haydee-workshop-upload/actions/workflows/update-major-tag.yml/badge.svg)](https://github.com/thegamerbay/haydee-workshop-upload/actions/workflows/update-major-tag.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

A GitHub Action that automatically packs a Haydee mod into a `.pack` file using the official `packer.exe`, and uploads it directly to the Steam Workshop.

## 🚀 Features
* **Automated Packing:** Automatically runs Haydee's `packer.exe` on your provided folders.
* **SteamCMD Integration:** Downloads, bootstraps, and caches SteamCMD for fast deployments.
* **Workshop Publishing:** Generates the required `workshop.vdf` manifest and uploads your item.
* **Dependency Management:** Automatically installs required Windows dependencies (DirectX, OpenAL, VCRedist) via Chocolatey.

## ⚠️ Prerequisites
1. **Windows Runner:** This action relies on Windows executables (`packer.exe`, SteamCMD) and PowerShell scripts. Your workflow job **must** run on `windows-latest`.
2. **Steam Account Requirements:** The account used in the `STEAM_USERNAME` and `STEAM_PASSWORD` secrets must meet the following criteria to allow automated, non-interactive uploads via SteamCMD:
   * **Game Ownership:** The account *must* own the game Haydee in its library.
   * **Steam Guard Disabled:** To allow SteamCMD to log in and upload without prompting for a 2FA mobile code or email code, Steam Guard **must be fully disabled** on this account.
   * *(Recommended)* Since disabling Steam Guard on your main account is a massive security risk, it is highly advised to create a separate "bot" Steam account, configure Steam Family Sharing to give it access to Haydee (or buy a second copy), disable Steam Guard on it, and add its developer rights to your mod project.

## 🛠 Usage

Create a workflow file in your mod repository (e.g., `.github/workflows/deploy.yml`):

```yaml
name: Deploy Haydee Mod

on:
  push:
    tags:
      - 'v*' # Triggers on version tags

jobs:
  deploy:
    runs-on: windows-latest
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Upload Mod to Steam Workshop
        uses: thegamerbay/haydee-workshop-upload@v1
        with:
          id: '1234567890' # Replace with your Workshop item ID (0 for new mods)
          folder: 'Outfits'
          pack_name: 'MyAwesomeMod.pack'
          priority: '2'
          changelog: 'Release ${{ github.ref_name }}'
        env:
          STEAM_USERNAME: ${{ secrets.STEAM_USERNAME }}
          STEAM_PASSWORD: ${{ secrets.STEAM_PASSWORD }}
```

For a complete working example, you can also check out the [`.github/workflows/example.yml`](.github/workflows/example.yml) file in this repository.

## 📥 Inputs

| Input | Required | Default | Description |
| --- | --- | --- | --- |
| `id` | Yes |  | Workshop item ID. Specify `0` if this is a brand new mod. |
| `folder` | Yes |  | Folder name(s) with prepared mod files (e.g., `'Outfits'` or `'Outfits, Maps'`). |
| `pack_name` | Yes |  | Resulting `.pack` file name (e.g., `'SynthwaveOutfit.pack'`). |
| `priority` | No | `2` | Package priority. Note: Base game files have priority `1`. |
| `changelog` | No | `Automatic update...` | The changelog text to be posted on the Steam Workshop page. |

## 🔐 Environment Variables

This action requires Steam credentials to authenticate and upload items via SteamCMD:

| Variable | Description |
| --- | --- |
| `STEAM_USERNAME` | Your Steam account username. Store this in your repository's **Secrets**. |
| `STEAM_PASSWORD` | Your Steam account password. Store this in your repository's **Secrets**. |

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](https://github.com/thegamerbay/haydee-workshop-upload/blob/main/LICENSE) file for details.
