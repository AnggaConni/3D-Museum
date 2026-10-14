# 3D Museum

A browser-based 3D exhibition that reads collection metadata from a Google Sheets CSV published to the web. Built with Three.js and designed to be adapted by museums, schools, community groups, and heritage projects.

**The Google Sheets URL and 3D category models are configured in `source.json`**, so users can change the data source or swap model files without editing application code.

## Visual setup guide

Follow the illustrated guide below to publish your Google Sheet as a CSV and connect it to the museum. The full image is available as [HOWTO.png](HOWTO.png).

![Step-by-step tutorial: Publish a Google Sheet as CSV and configure source.json](HOWTO.png)


## Quick setup / Cara menggunakan

1. **Fork or copy this repository** and publish it using GitHub Pages (or another static web host).
2. Create a Google Sheet with column headers in the first row:

   | Column | Required? | Example |
   |---|---|---|
   | `id` | Recommended | `001` |
   | `title` | Yes | `Traditional Fishing Boat` |
   | `description` | Optional | `A traditional wooden boat...` |
   | `category` | Yes | `museum`, `underwater`, or `ancient_craft` |
   | `image_url` | Optional | Public image URL or Google Drive share URL |
   | `year` | Optional | `1950` |

   Aliases such as `nama`, `judul`, `kategori`, `gambar`, `deskripsi`, and `tahun` are also supported.
3. In Google Sheets, choose **File → Share → Publish to web**. Select the relevant sheet/tab and **Comma-separated values (.csv)**, publish it, then copy the published CSV URL. Merely sharing a sheet with “Anyone with the link” is not the same as publishing it to the web.
4. Open `source.json` and replace the `sheetUrl` placeholder with the full URL from Google. **You do not need to edit `index.html`.**
5. Commit the change and open the published website. The app reads `source.json` at startup.

Example `source.json`:

```json
{
  "_instructions": "Copy your Google Sheets Publish to web CSV URL into sheetUrl. Keep this file beside index.html.",
  "sourceName": "My Museum Collection",
  "sheetUrl": "https://docs.google.com/spreadsheets/d/e/YOUR_PUBLISHED_ID/pub?output=csv",
  "models": [
    {
      "id": "shipwreck",
      "category": "underwater",
      "enabled": true,
      "url": "./models/shipwreck.glb",
      "targetSize": 8
    },
    {
      "id": "african-hut",
      "category": "ancient_craft",
      "enabled": true,
      "url": "./models/african%20hut.glb",
      "targetSize": 8
    },
    {
      "id": "museum",
      "category": "museum",
      "enabled": true,
      "url": "./models/museum.glb",
      "targetSize": 8
    },
    {
      "id": "artifact",
      "category": null,
      "enabled": false,
      "url": "./models/artifact.glb",
      "targetSize": 8
    },
    {
      "id": "statue",
      "category": null,
      "enabled": false,
      "url": "./models/statue.glb",
      "targetSize": 8
    }
  ]
}
```

Keep Google's exact published URL, including `output=csv` when provided.

### 3D model configuration

- The three active models match the existing Google Sheets categories: `underwater`, `ancient_craft`, and `museum`.
- `targetSize: 8` normalizes each model to the same largest bounding-box dimension while preserving its original proportions. The model is centered and placed on the category platform.
- `enabled: false` disables a configured model. The `artifact.glb` and `statue.glb` files are included as inactive extras because they are not assigned to one of the three current categories.
- Put replacement `.glb` files in the `models/` folder and change only the matching `url` in `source.json`. Use URL-encoded spaces, such as `african%20hut.glb`, for filenames containing spaces.
- Large GLB files may take longer to load on mobile connections. Optimize/compress them before adding substantially larger assets.

### 3D environment configuration

The generated city skyline has been replaced by a configurable GLB environment, currently `amphitheater/amphitheatre.glb`. The scene uses a bright blue sky, warm late-afternoon directional light, hemispheric fill light, and the environment's own floor to avoid overlapping the old grid plane. Change the `environment` object in `source.json` to disable or replace it:

```json
"environment": {
  "enabled": true,
  "url": "./amphitheater/amphitheatre.glb",
  "targetWidth": 270,
  "rotationY": 0,
  "position": [0, 0, 0]
}
```

- `targetWidth` controls the environment's horizontal footprint in Three.js scene units; proportions are preserved.
- `rotationY` rotates the model around its vertical axis, in degrees.
- `position` is an optional `[x, y, z]` offset after the model is centered and aligned to ground level.
- Set `enabled` to `false` to hide the environment model.
- The background environment is excluded from hover/click raycasting so internal names inside the GLB cannot override the three category tooltips.

### Title screen branding

Edit the `titleScreen` object in `source.json` to change the opening screen without touching `index.html`:

```json
"titleScreen": {
  "titleLine1": "NEO",
  "titleLine2": "MUSEUM",
  "subtitle": "Nyah Lasem Heritage",
  "footerText": "SYSTEM ARCHITECT: ANGGA CONNI SAPUTRA // BUILD 2026"
}
```

- `titleLine1` and `titleLine2` are the two title lines; set `titleLine2` to an empty string for a single-line heading.
- `subtitle` controls the heritage/project subtitle.
- `footerText` controls the small footer at the bottom of the opening screen.
- These values are inserted as plain text, so they are not interpreted as HTML.

## How it works

- `index.html` loads `source.json` at startup.
- `source.json` contains the published CSV URL, collection display name, and enabled/disabled GLB model mapping.
- Three.js `GLTFLoader` loads the selected local `.glb` files; each active category model is scaled to a shared maximum dimension, centered, and placed on its display base.
- PapaParse parses the published CSV, with a small built-in CSV parser as a fallback if the PapaParse CDN is unavailable.
- The loader validates CSV headers and expected category values before showing the collection. It tries Google Sheets directly, then two proxy fallbacks, each with a timeout; free public proxies can still be temporarily unavailable.
- The sheet must be published for browser-based access. **Do not publish passwords, private data, API keys, or confidential records**; published output may be readable by visitors.
- Google publishing settings, browser CORS restrictions, network connectivity, and third-party service availability may affect loading.

## Want your own domain?

You can keep using the free `https://YOUR-USERNAME.github.io/3D-Museum/` address. **Buying a personal domain is optional**—it is useful if you want a shorter, branded address such as `museum.example.com`.

### Quick guide: Porkbun domain + GitHub Pages

1. **Publish the website on GitHub Pages first.** Put the project files in your repository's root folder, including `index.html`, `source.json`, `favicon.svg`, and `HOWTO.png`. Open **Settings → Pages**, choose **Deploy from a branch**, select the `main` branch and `/(root)`, then save. Wait until GitHub provides your Pages URL.
2. **Buy a domain (optional).** Visit [Porkbun](https://porkbun.com/), search for a domain name, choose an available one, and complete checkout. You need the domain registration; you do not need to buy a separate hosting package for this static site because GitHub Pages serves the website.
3. **Add your custom domain in GitHub first.** In the repository, open **Settings → Pages**. Under **Custom domain**, enter the domain you plan to use, such as `www.example.com` or `example.com`, and click **Save**.
4. **Configure DNS at Porkbun.** Open the domain's DNS management and add the appropriate records. For a `www` subdomain, add a `CNAME` record with host `www` pointing to `YOUR-USERNAME.github.io` (do not include the repository path). For a root/apex domain such as `example.com`, add the four GitHub Pages `A` records below with host `@`:

   ```text
   185.199.108.153
   185.199.109.153
   185.199.110.153
   185.199.111.153
   ```

   Remove conflicting default records for the same host if present. DNS changes can take up to 24 hours to propagate. GitHub's official guide covers the current record types and optional IPv6 records: [Managing a custom domain for GitHub Pages](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site).
5. **Check HTTPS.** Return to **Settings → Pages** after DNS has propagated. Once GitHub finishes checking the domain, enable **Enforce HTTPS** when the option becomes available.

For a detailed, illustrated walkthrough, see Angga's article: [The Ultimate Zero-Cost Web Architecture: Connecting a Porkbun Domain to GitHub Pages](https://horizon-scanning.org/blog/article/the-ultimate-zero-cost-web-architecture-connecting-a-porkbun-domain-to-github-pages.html).

> **Tip:** You can do all the steps for hosting this museum without buying a domain. Start with the free GitHub Pages address, and connect a custom domain later whenever you want a personal web address.

## Branding, metadata, and favicon

The page includes general SEO and social-sharing metadata: page title and description, Open Graph tags, Twitter/X card tags, theme color, language, and browser referrer policy. When adapting this repository, update the title, description, and author in the `<head>` of `index.html` to match your project.

The custom `favicon.svg` is linked from `index.html`. Replace it with your own icon if you rebrand the museum. For social preview images, add an absolute, publicly accessible image URL using `og:image` and `twitter:image`; avoid hard-coding a canonical URL until you know the final website address.

## Technologies and free tiers

- **Google Sheets Publish to web (CSV):** simple data source under Google's standard service limits.
- **Three.js:** 3D scene and rendering.
- **OrbitControls:** camera interaction.
- **GLTFLoader:** loads the local `.glb` category models.
- **GSAP:** animation.
- **Tailwind CSS CDN:** interface styling.
- **PapaParse:** CSV parsing.
- **Google Fonts:** typography.
- **AllOrigins and Codetabs (fallbacks):** public third-party proxies attempted only if the browser cannot fetch the published CSV directly. They are best-effort dependencies, have request timeouts, and are not guaranteed to be available.

Third-party libraries are loaded from public CDNs, so an internet connection is needed.

## Troubleshooting

- **Configuration error:** validate `source.json` JSON syntax and check that `sheetUrl` contains the full published CSV URL.
- **No live data:** publish the correct sheet/tab as CSV, and ensure categories match `underwater`, `ancient_craft`, or `museum`.
- **A category tooltip shows a strange model-part name:** the app now resolves hover/clicks to the parent category group, rather than using internal names from GLB meshes.
- **The amphitheatre background does not appear:** verify `environment.url` in `source.json` and confirm `amphitheater/amphitheatre.glb` exists in the repository. Check the browser Console for `[ENVIRONMENT]` messages.
- **A 3D category model is missing:** check its `url` in `source.json`, confirm the file exists in `models/`, and make sure its `enabled` value is `true` and its `category` matches one of the current categories.
- **Browser fetch failure:** confirm `source.json` uses the published CSV URL (normally ending in `/pub?output=csv`), not the HTML page URL ending in `/pubhtml`. The app now converts `/pubhtml` URLs automatically, but the configured source should still be a published sheet.
- **Stuck on Connecting:** open the browser DevTools Console. Each retrieval route has a timeout and logs its own failure reason; public proxy services can still experience outages.
- **PapaParse unavailable:** a built-in CSV parser is used as a fallback. If the source is HTML or the CSV is missing `category` and `title`-type headers, the loader rejects it with a diagnostic instead of pretending it is live data.
- **Images not shown:** verify the image URL is public. Google Drive images may need public viewing permissions.

## License and reuse

See [LICENSE](LICENSE). Retain attribution and check the license before redistribution or commercial use.

Built by Angga Conni Saputra.
