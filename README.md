# 3D Museum

![License: GPL-3.0-only](https://img.shields.io/badge/License-GPL--3.0-only-blue.svg)
![Static site](https://img.shields.io/badge/hosting-GitHub%20Pages%20ready-2f855a.svg)
![No backend](https://img.shields.io/badge/backend-none-64748b.svg)

**A free, open-source 3D catalogue template that anyone can publish.** Build a digital museum, craft catalogue, heritage collection, classroom showcase, or personal collection with a simple Google Sheets CSV backend and a customizable Three.js frontend.

No application server or database is required for the static version. The collection is driven by `source.json`, and the 3D models, environment, and opening theme are configurable.

<p align="center">
  <a href="https://anggaconni.github.io/3D-Museum/editor.html">
    <img src="https://img.shields.io/badge/OPEN%20THE%20JSON%20EDITOR-Configure%20%26%20export%20source.json-D9B56D?style=for-the-badge&labelColor=101A20" alt="Open the 3D Museum JSON Editor">
  </a>
</p>

<p align="center"><strong>Are you having trouble editing JSON?</strong><br>Use the visual editor instead: change the fields, validate the configuration, export <code>source.json</code>, and replace the file in your own repository. The editor does not publish changes automatically and does not need a GitHub token.</p>

## Start here: use the template

You can make an independent copy of this project without forking the original repository.

1. Open the [3D-Museum repository](https://github.com/AnggaConni/3D-Museum).
2. Click the green **Use this template** button near the top-right of the repository page.
3. Choose **Create a new repository** from the menu.
4. Select your GitHub account or organization as the owner. Enter your new repository name, for example `my-community-museum`, and choose Public or Private visibility as appropriate.
5. Click **Create repository from template**. GitHub creates a new repository containing the template files. This is a separate repository, not a fork that automatically receives upstream changes.
6. In your new repository, customize `source.json` using the editor below. Replace the demo Google Sheets URL with your own published CSV URL before sharing your catalogue.
7. Open **Settings → Pages**. Under **Build and deployment**, choose **Deploy from a branch**, select branch `main` and folder `/(root)`, then click **Save**. If your default branch has a different name, select that branch instead.
8. Wait for GitHub Pages to finish deploying. Visit the Pages URL shown in **Settings → Pages**. Your editor will usually be available at `https://YOUR-USERNAME.github.io/YOUR-REPOSITORY/editor.html`.

> **Important:** after using the template, the copied `source.json` may still point to the sample collection. Update the sheet URL and verify the model/image asset rights before publishing your own version.

## Visual setup guide

Follow the illustrated guide to publish a Google Sheet as CSV and connect it to the museum. The full image is available as [HOWTO.png](HOWTO.png).

![Step-by-step tutorial: publish a Google Sheet as CSV and configure source.json](HOWTO.png)

## The easiest way to configure it: JSON Editor

**Open the [3D Museum JSON Editor](https://anggaconni.github.io/3D-Museum/editor.html).**

1. When the editor is hosted on GitHub Pages, click **Load site config** to load that repository's current `source.json`. You can also click **Import JSON** and select a local `source.json` file.
2. Edit the collection name and published Google Sheets URL.
3. Choose a title theme: `egyptian_tomb`, `ich_crafting`, or `neo_museum`. You can edit the title, subtitle, and footer text for the selected preset.
4. Configure model paths, enabled/disabled status, target size, and the surrounding GLB environment.
5. Review the generated JSON and its validation notes. Click **Download source.json** or **Copy JSON**.
6. In your own repository, open `source.json`, click the pencil **Edit** button, replace the entire contents with the exported JSON, and click **Commit changes**.
7. Wait for GitHub Pages to redeploy, then refresh the site.

The editor runs in the browser. It has no backend, does not require a sign-in, does not ask for a GitHub token, and never commits changes on your behalf. You can also download `editor.html` and use **Import JSON** if the editor cannot automatically fetch a same-folder `source.json` file.

## Manual setup

If you prefer to edit configuration by hand, open `source.json` and update the relevant fields. You do **not** need to edit `index.html` for normal source, theme, model-path, or environment adjustments.

### Google Sheets data source

Create a Google Sheet with headers in the first row:

| Column | Required? | Example |
|---|---|---|
| `id` | Recommended | `001` |
| `title` | Yes | `Traditional Fishing Boat` |
| `description` | Optional | `A traditional wooden boat...` |
| `category` | Yes | `museum`, `underwater`, or `ancient_craft` |
| `image_url` | Optional | A publicly accessible image URL |
| `year` | Optional | `1950` |

Aliases such as `nama`, `judul`, `kategori`, `gambar`, `deskripsi`, and `tahun` are also supported.

In Google Sheets, choose **File → Share → Publish to web**. Select the relevant sheet/tab and **Comma-separated values (.csv)**, publish it, and copy the published CSV URL. Merely sharing the sheet with “Anyone with the link” is not the same as publishing it to the web.

Example:

```json
{
  "sourceName": "My Museum Collection",
  "sheetUrl": "https://docs.google.com/spreadsheets/d/e/YOUR_PUBLISHED_ID/pub?output=csv"
}
```

Keep Google's published URL. The application converts common `/pubhtml` URLs to a CSV endpoint where possible, but a true published CSV URL is preferred.

> **Public-data warning:** published sheet content can be read by visitors. Never place passwords, private personal details, API keys, or confidential records in a sheet published to the web.

### Theme presets

Set `theme.active` to one of the keys below. The preset also stores its title and subtitle text.

- `egyptian_tomb` — bright limestone/papyrus, antique gold and lapis-blue accents.
- `ich_crafting` — light cotton-paper palette, woven geometry, botanical green and terracotta accents.
- `neo_museum` — the original dark cyberpunk grid and neon-cyan style.

For example, changing `"active": "egyptian_tomb"` to `"active": "ich_crafting"` selects the ICH crafting title screen.

### 3D category models

Models are listed under `models` in `source.json`. Each entry supports `id`, `category`, `enabled`, `url`, and `targetSize`.

- `targetSize: 8` scales the model to a shared maximum bounding-box dimension while keeping its proportions.
- Set `enabled: false` to disable a configured model without deleting its entry.
- Use paths such as `./models/shipwreck.glb`. For filenames containing spaces, URL-encode them, for example `./models/african%20hut.glb`.
- The current 3D map has three configured scene slots: `underwater`, `ancient_craft`, and `museum`. Adding a new model entry alone will not create a new visible category; adding a new category requires a matching scene slot in `index.html`.

### 3D environment

The surrounding environment is configured separately from category models:

```json
"environment": {
  "enabled": true,
  "url": "./amphitheater/amphitheatre.glb",
  "targetWidth": 270,
  "rotationY": 0,
  "position": [0, 0, 0]
}
```

- `targetWidth` sets the horizontal footprint in Three.js scene units while preserving proportions.
- `rotationY` rotates around the vertical axis in degrees.
- `position` provides an optional `[x, y, z]` offset.
- Set `enabled` to `false` to disable the environment model.

## How it works

- `index.html` loads `source.json` at startup.
- Google Sheets Publish to web serves catalogue rows as CSV.
- PapaParse parses the CSV, with a small built-in parser as a fallback if the PapaParse CDN is unavailable.
- The loader validates headers and expected categories, attempts the published CSV directly, then tries best-effort proxy fallbacks with timeouts.
- Three.js and GLTFLoader render local GLB models. GSAP powers animations; OrbitControls provides camera movement.
- GitHub Pages hosts the static site. No server-side application or database is needed for this setup.

## Technologies and third-party services

- **Three.js / GLTFLoader:** 3D rendering and GLB model loading.
- **OrbitControls:** camera interaction.
- **GSAP:** animation.
- **PapaParse:** CSV parsing, with a built-in fallback parser.
- **Tailwind CSS CDN:** interface utilities.
- **Google Fonts:** typography.
- **Google Sheets Publish to web:** collection data source.
- **AllOrigins and Codetabs:** optional public proxy fallbacks if direct browser CSV access fails. These are community services and may be unavailable or rate-limited.

An internet connection is needed for the hosted data, public CDNs, and proxy fallbacks.

## Troubleshooting

- **Stuck on Connecting:** check the browser DevTools Console for the failed data-source attempts. Public proxy services are not guaranteed to be available.
- **No live records:** check that the sheet/tab is published as CSV and that its header contains a `title`-type column and a `category`-type column.
- **A model does not load:** verify its path in `source.json`, check filename spelling and capitalization, and confirm the file exists in the repository.
- **An extra model does not appear:** the current scene supports only the three categories documented above. A new category needs a corresponding scene implementation in `index.html`.
- **An image is missing:** check the image URL and confirm that the image is publicly accessible.
- **The editor did not load the site's JSON:** click **Import JSON** and choose `source.json` from your computer. This is also the workaround when opening the editor directly as a local file.

## Custom domain (optional)

The free GitHub Pages address is enough to get started. Buying a domain is optional.

For a Porkbun domain setup guide, see [The Ultimate Zero-Cost Web Architecture: Connecting a Porkbun Domain to GitHub Pages](https://horizon-scanning.org/blog/article/the-ultimate-zero-cost-web-architecture-connecting-a-porkbun-domain-to-github-pages.html). You can also follow GitHub's [official custom-domain guide](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site).

## License and asset rights

The **application source code** is licensed under the [GNU General Public License v3.0 only (GPL-3.0-only)](LICENSE). You may use, study, modify, and redistribute the covered code under that license's terms. When you distribute covered modified versions, comply with GPLv3's source and notice requirements.

**Third-party assets are separate.** The GLB files, photos, icons, fonts, and other imported content may have different licenses or creator requirements. Check each asset's own license and keep the required attribution. The software license does not automatically grant relicensing rights for assets that the project does not own.

Issues and improvements are welcome. Built by Angga Conni Saputra.
