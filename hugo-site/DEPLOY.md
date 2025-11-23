# Deployment Guide

## Option 1: GitHub Pages (Recommended)

1. Create a new GitHub repository for your site
2. Push the hugo-site folder to your repository
3. Create `.github/workflows/hugo.yml`:

```yaml
name: Deploy Hugo site to Pages

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

defaults:
  run:
    shell: bash

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          extended: true
      - name: Build
        run: hugo --minify
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

4. Enable GitHub Pages in your repository settings (Settings > Pages > Source: GitHub Actions)
5. Push your changes and the site will auto-deploy!

## Option 2: Netlify

1. Sign up for Netlify (free)
2. Connect your GitHub repository
3. Configure build settings:
   - Build command: `hugo`
   - Publish directory: `public`
   - Hugo version: `0.152.2` (or latest)
4. Click Deploy!

Your site will be live at yoursite.netlify.app (you can add a custom domain)

## Option 3: Manual Deployment

1. Build the site:
   ```
   cd hugo-site
   hugo
   ```

2. Upload the contents of the `public/` folder to your web host

## Custom Domain

If you have a custom domain (like parulpandey.com):

1. Copy your CNAME file to the static/ folder (already done!)
2. Configure DNS settings with your domain registrar
3. For GitHub Pages: Add custom domain in Settings > Pages

## Testing Before Deploy

Always test your site locally before deploying:

```
hugo server
```

Visit http://localhost:1313/ to preview.
