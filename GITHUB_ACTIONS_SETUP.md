# GitHub Actions Setup - Step by Step Guide

## 🎯 Build WinDroid Pro for FREE using GitHub Actions

**No PC needed! No credit card! Completely free!**

This guide shows you how to build WinDroid Pro APK using GitHub Actions - even from your Android phone!

---

## 📱 Method 1: From Android Phone (Easiest)

### Step 1: Create GitHub Account

1. Open browser on your phone
2. Go to https://github.com
3. Click "Sign up"
4. Enter email, password, username
5. Verify email
6. **Done!** (No credit card needed)

### Step 2: Create Repository

1. Click the "+" icon (top right)
2. Click "New repository"
3. Repository name: `WinDroidPro`
4. Description: `Advanced Windows Emulator for Android`
5. Select "Public" (for unlimited free builds)
6. Click "Create repository"

### Step 3: Upload Files

1. Click "uploading an existing file"
2. Extract WinDroidPro-Complete.zip on your phone
3. Select all files from WinDroidPro folder
4. Drag and drop (or click "choose your files")
5. Scroll down, click "Commit changes"
6. Wait for upload to complete

### Step 4: Create Workflow File

1. Click "Add file" → "Create new file"
2. File name: `.github/workflows/build.yml`
3. Paste this code:

```yaml
name: Build APK

on:
  push:
    branches: [ main ]
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
    
    - name: Set up JDK 17
      uses: actions/setup-java@v3
      with:
        java-version: '17'
        distribution: 'temurin'
    
    - name: Setup Android SDK
      uses: android-actions/setup-android@v2
    
    - name: Grant execute permission
      run: chmod +x gradlew
    
    - name: Build Debug APK
      run: ./gradlew assembleDebug
    
    - name: Upload APK
      uses: actions/upload-artifact@v3
      with:
        name: WinDroidPro-Debug
        path: app/build/outputs/apk/debug/*.apk
```

4. Click "Commit changes"

### Step 5: Run Build

1. Click "Actions" tab (top menu)
2. Click "Build APK" workflow
3. Click "Run workflow" button (right side)
4. Click green "Run workflow" button
5. Wait 5-10 minutes for build to complete
6. Refresh page to see progress

### Step 6: Download APK

1. Click on the completed workflow (green checkmark)
2. Scroll down to "Artifacts" section
3. Click "WinDroidPro-Debug" to download
4. Extract the ZIP file
5. Install the APK on your phone!

**🎉 Done! You built the APK without a PC!**

---

## 💻 Method 2: From PC (Alternative)

### Step 1: Install Git

**Windows:**
- Download from https://git-scm.com/download/win
- Install with default settings

**Mac:**
```bash
brew install git
```

**Linux:**
```bash
sudo apt install git
```

### Step 2: Setup Git

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

### Step 3: Create GitHub Account

1. Go to https://github.com
2. Sign up (free, no credit card)

### Step 4: Create Repository

1. Click "+" → "New repository"
2. Name: `WinDroidPro`
3. Public repository
4. Create repository

### Step 5: Push Code

```bash
# Extract WinDroidPro-Complete.zip
cd WinDroidPro

# Initialize git
git init

# Add all files
git add .

# Commit
git commit -m "Initial commit"

# Add remote
git remote add origin https://github.com/YOUR_USERNAME/WinDroidPro.git

# Push
git push -u origin main
```

### Step 6: Create Workflow

Create `.github/workflows/build.yml` with the workflow code above, then:

```bash
git add .github/workflows/build.yml
git commit -m "Add build workflow"
git push
```

### Step 7: Build

1. Go to GitHub repository
2. Click "Actions" tab
3. Click "Build APK"
4. Click "Run workflow"
5. Wait for completion
6. Download APK from artifacts

---

## 🔄 Automatic Builds

The workflow is configured to build automatically on every push:

```bash
# Make changes to code
git add .
git commit -m "Update code"
git push

# Build starts automatically!
# Check Actions tab for progress
```

---

## 📊 Build Status

### Check Build Progress:

1. Go to "Actions" tab
2. See running/completed builds
3. Click on build to see logs
4. Green checkmark = success
5. Red X = failed (check logs)

### Build Time:
- First build: 8-12 minutes
- Subsequent builds: 5-8 minutes
- Cached builds: 3-5 minutes

---

## 🎨 Customize Workflow

### Build Release APK:

Change `assembleDebug` to `assembleRelease`:

```yaml
- name: Build Release APK
  run: ./gradlew assembleRelease
```

### Build Multiple Variants:

```yaml
- name: Build All Variants
  run: |
    ./gradlew assembleDebug
    ./gradlew assembleRelease
```

### Add Signing:

```yaml
- name: Sign APK
  run: |
    echo "${{ secrets.KEYSTORE }}" | base64 -d > keystore.jks
    jarsigner -keystore keystore.jks -storepass ${{ secrets.KEYSTORE_PASSWORD }} app/build/outputs/apk/release/app-release-unsigned.apk alias
```

---

## 🔐 Add Secrets (For Signing)

### Step 1: Encode Keystore

```bash
base64 keystore.jks > keystore.txt
```

### Step 2: Add to GitHub

1. Go to repository Settings
2. Click "Secrets and variables" → "Actions"
3. Click "New repository secret"
4. Name: `KEYSTORE`
5. Value: Contents of keystore.txt
6. Add another secret: `KEYSTORE_PASSWORD`

---

## 📱 Download APK to Phone

### Method 1: Direct Download

1. Open GitHub on phone browser
2. Go to Actions → Completed workflow
3. Click artifact to download
4. Extract and install

### Method 2: Release

1. Create a release on GitHub
2. Upload APK as release asset
3. Share release link
4. Anyone can download

### Method 3: QR Code

1. Get artifact download link
2. Generate QR code at https://qr-code-generator.com
3. Scan with phone
4. Download APK

---

## 🚀 Advanced Features

### Build on Schedule:

```yaml
on:
  schedule:
    - cron: '0 0 * * 0'  # Every Sunday at midnight
```

### Build on Tag:

```yaml
on:
  push:
    tags:
      - 'v*'
```

### Matrix Builds:

```yaml
strategy:
  matrix:
    variant: [debug, release]
steps:
  - run: ./gradlew assemble${{ matrix.variant }}
```

---

## 🐛 Troubleshooting

### Build Failed - Gradle Error

**Solution:**
```yaml
- name: Make gradlew executable
  run: chmod +x gradlew
```

### Build Failed - Out of Memory

**Solution:**
```yaml
- name: Build with more memory
  run: ./gradlew assembleDebug -Dorg.gradle.jvmargs=-Xmx4g
```

### Build Failed - NDK Not Found

**Solution:**
```yaml
- name: Install NDK
  run: |
    echo "y" | $ANDROID_HOME/cmdline-tools/latest/bin/sdkmanager "ndk;26.1.10909125"
```

### Artifact Not Found

**Solution:**
- Check build logs for errors
- Verify APK path is correct
- Ensure build completed successfully

---

## 💡 Tips & Tricks

### Speed Up Builds:

1. **Use Caching:**
```yaml
- name: Cache Gradle
  uses: actions/cache@v3
  with:
    path: |
      ~/.gradle/caches
      ~/.gradle/wrapper
    key: ${{ runner.os }}-gradle-${{ hashFiles('**/*.gradle*') }}
```

2. **Use Gradle Daemon:**
```yaml
- name: Build with daemon
  run: ./gradlew assembleDebug --daemon
```

### Reduce Build Time:

1. Cache dependencies
2. Use incremental builds
3. Disable unnecessary tasks
4. Use parallel execution

### Monitor Usage:

1. Go to Settings → Billing
2. Check Actions minutes used
3. Public repos = unlimited
4. Private repos = 2000 min/month free

---

## 📈 Build Statistics

### Free Tier Limits:

| Account Type | Minutes/Month | Storage |
|--------------|---------------|---------|
| Public Repo | Unlimited | 500 MB |
| Private Repo | 2,000 | 500 MB |
| Pro | 3,000 | 1 GB |
| Team | 3,000 | 2 GB |

### Build Resources:

- **CPU**: 2 cores
- **RAM**: 7 GB
- **Storage**: 14 GB SSD
- **OS**: Ubuntu 22.04

---

## 🎯 Best Practices

1. **Use Public Repos** for unlimited builds
2. **Cache Dependencies** to speed up builds
3. **Use Workflow Dispatch** for manual triggers
4. **Add Status Badge** to README
5. **Clean Old Artifacts** to save space
6. **Use Secrets** for sensitive data
7. **Test Locally** before pushing

---

## 📊 Status Badge

Add to README.md:

```markdown
![Build Status](https://github.com/YOUR_USERNAME/WinDroidPro/workflows/Build%20APK/badge.svg)
```

---

## 🎉 Success!

You now have:
- ✅ Free automated builds
- ✅ No PC required
- ✅ No credit card needed
- ✅ Unlimited builds (public repo)
- ✅ APK downloads
- ✅ Build history

**Start building now! 🚀**

---

## 📞 Need Help?

### Resources:
- GitHub Actions Docs: https://docs.github.com/actions
- Android Build Docs: https://developer.android.com/studio/build
- Community Forum: https://github.community

### Common Issues:
- Check Actions tab for build logs
- Read error messages carefully
- Search GitHub issues for solutions
- Ask in GitHub Discussions

---

**Happy Building! 🎊**