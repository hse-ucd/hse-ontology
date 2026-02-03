# GitHub Pages Deployment Setup

This repository is configured to automatically deploy the ontology documentation to GitHub Pages.

## Setup Instructions

### 1. Enable GitHub Pages in Your Repository

1. Go to your GitHub repository: `https://github.com/hse-ucd/hse-ontology`
2. Click on **Settings** (top menu)
3. In the left sidebar, click **Pages**
4. Under **Source**, select **GitHub Actions**
5. Click **Save**

### 2. Push Your Changes

Once GitHub Pages is enabled, simply push to the main branch:

```bash
git add .
git commit -m "Enable GitHub Pages deployment"
git push origin main
```

### 3. Access Your Deployed Site

After the workflow completes (check the **Actions** tab), your ontology documentation will be available at:

```
https://hse-ucd.github.io/hse-ontology/
```

Or if you have a custom domain configured:
```
https://your-custom-domain.com/
```

## Automatic Deployment

The workflow (`.github/workflows/deploy-pages.yml`) automatically triggers when:
- You push changes to the `main` branch that affect:
  - `ontology_v101/` directory
  - `docs/` directory
  - The workflow file itself
- You manually trigger it from the **Actions** tab

## Manual Deployment

To manually trigger a deployment:

1. Go to **Actions** tab in your GitHub repository
2. Click **Deploy Ontology Documentation to GitHub Pages**
3. Click **Run workflow**
4. Select the branch and click **Run workflow**

## File Structure

```
hse-ontology/
├── .github/
│   └── workflows/
│       └── deploy-pages.yml         # GitHub Actions workflow
├── ontology_v101/                   # Deployed to GitHub Pages root
│   ├── index-en.html               # Main documentation page
│   ├── ontology.ttl                # Ontology files
│   ├── ontology.owl
│   ├── ontology.jsonld
│   ├── resources/                  # CSS, JS, images
│   └── webvowl/                    # WebVOWL visualization
└── mappings/                        # RML mapping configuration (not deployed)
```

## Customizing the Deployment

### Deploy Different Directory

Edit `.github/workflows/deploy-pages.yml` and change the `path`:

```yaml
- name: Upload artifact
  uses: actions/upload-pages-artifact@v3
  with:
    path: './docs'  # Change this to your desired directory
```

### Deploy Multiple Directories

You can copy files to a staging directory before deployment:

```yaml
- name: Prepare deployment
  run: |
    mkdir -p _site
    cp -r ontology_v101/* _site/
    cp -r docs _site/docs

- name: Upload artifact
  uses: actions/upload-pages-artifact@v3
  with:
    path: './_site'
```

### Custom Domain

To use a custom domain:

1. Add a `CNAME` file to `ontology_v101/` with your domain:
   ```
   ontology.hse.ie
   ```

2. Configure DNS settings at your domain provider:
   - Add a CNAME record pointing to `hse-ucd.github.io`

## Troubleshooting

### Workflow Fails

Check the **Actions** tab for error messages. Common issues:
- GitHub Pages not enabled in repository settings
- Insufficient permissions (check workflow `permissions` section)
- Invalid file paths

### 404 Errors After Deployment

- Ensure `index-en.html` exists in the deployed directory
- Check that relative links in HTML files are correct
- Verify the deployment path in the workflow matches your file structure

### Changes Not Appearing

- Check that your push included the correct files
- Clear browser cache (Ctrl+Shift+R or Cmd+Shift+R)
- Wait a few minutes for GitHub's CDN to update

## Monitoring Deployments

View deployment status:
- **Actions** tab: See workflow runs and logs
- **Deployments** section (right sidebar): Track deployment history
- Check commit status badges for deployment status

## Security Note

The `GITHUB_TOKEN` is automatically provided by GitHub Actions with appropriate permissions. No additional secrets or tokens are needed for basic GitHub Pages deployment.
