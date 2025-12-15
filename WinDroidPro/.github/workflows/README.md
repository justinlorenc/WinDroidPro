# GitHub Actions Workflows

This directory contains GitHub Actions workflow files for automated building.

## Available Workflows

### build.yml - Build APK

Automatically builds the WinDroid Pro APK when you push code or manually trigger it.

**Triggers:**
- Push to `main` or `master` branch
- Pull requests
- Manual trigger from Actions tab

**What it does:**
1. Sets up Java 17
2. Sets up Android SDK
3. Caches Gradle dependencies (faster builds)
4. Builds Debug APK
5. Builds Release APK (unsigned)
6. Uploads APKs as artifacts

**How to use:**

### Method 1: Automatic Build (on push)
```bash
git add .
git commit -m "Update code"
git push
# Build starts automatically!
```

### Method 2: Manual Build
1. Go to your GitHub repository
2. Click "Actions" tab
3. Click "Build APK" workflow
4. Click "Run workflow" button
5. Select branch (usually `main`)
6. Click green "Run workflow" button
7. Wait 5-10 minutes
8. Download APK from artifacts

### Method 3: On Pull Request
Create a pull request and the build will run automatically.

## Download Built APK

After build completes:
1. Go to the workflow run (Actions tab)
2. Scroll down to "Artifacts" section
3. Click "WinDroidPro-Debug" to download
4. Extract ZIP file
5. Install APK on your device

## Build Time

- **First build**: 8-12 minutes (downloading dependencies)
- **Cached builds**: 3-5 minutes (using cached dependencies)

## Artifacts Retention

- APKs are kept for **90 days**
- Download them before they expire
- You can change retention in the workflow file

## Customization

### Build only Debug:
Remove the Release build steps from `build.yml`

### Build only Release:
Remove the Debug build steps from `build.yml`

### Change retention period:
Modify `retention-days: 90` to your preferred number

### Add signing:
Add secrets to repository and modify workflow to sign APK

## Troubleshooting

### Build Failed?
1. Click on the failed workflow
2. Check the logs for errors
3. Common issues:
   - Gradle sync failed: Check `build.gradle` files
   - NDK not found: Workflow should handle this automatically
   - Out of memory: Increase heap size in `gradle.properties`

### Artifact not found?
1. Make sure build completed successfully (green checkmark)
2. Check that APK path is correct in workflow
3. Verify build didn't fail silently

## Free Tier Limits

- **Public repositories**: Unlimited builds
- **Private repositories**: 2,000 minutes/month free
- **Storage**: 500 MB for artifacts

## Status Badge

Add this to your README.md to show build status:

```markdown
![Build Status](https://github.com/YOUR_USERNAME/WinDroidPro/workflows/Build%20APK/badge.svg)
```

Replace `YOUR_USERNAME` with your GitHub username.

## Need Help?

- Read `GITHUB_ACTIONS_SETUP.md` in project root
- Check GitHub Actions documentation: https://docs.github.com/actions
- Ask in GitHub Discussions