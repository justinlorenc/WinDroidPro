# Add GitHub Workflow via Termux

## 🚀 Quick Guide: Add Workflow File from Termux

### Method 1: Using GitHub CLI (Easiest)

#### Step 1: Install GitHub CLI
```bash
pkg update
pkg install gh
```

#### Step 2: Login to GitHub
```bash
gh auth login
# Select: GitHub.com
# Select: HTTPS
# Select: Login with a web browser
# Copy the code shown
# Press Enter
# Browser will open - paste code and authorize
```

#### Step 3: Clone Your Repository
```bash
cd ~
gh repo clone YOUR_USERNAME/WinDroidPro
cd WinDroidPro
```

#### Step 4: Create Workflow File
```bash
# Create directory
mkdir -p .github/workflows

# Create workflow file
cat > .github/workflows/build.yml << 'EOF'
name: Build APK

on:
  push:
    branches: [ main, master ]
  pull_request:
    branches: [ main, master ]
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
        cache: gradle
    
    - name: Setup Android SDK
      uses: android-actions/setup-android@v2
    
    - name: Grant execute permission for gradlew
      run: chmod +x gradlew
    
    - name: Cache Gradle packages
      uses: actions/cache@v3
      with:
        path: |
          ~/.gradle/caches
          ~/.gradle/wrapper
        key: ${{ runner.os }}-gradle-${{ hashFiles('**/*.gradle*', '**/gradle-wrapper.properties') }}
        restore-keys: |
          ${{ runner.os }}-gradle-
    
    - name: Build Debug APK
      run: ./gradlew assembleDebug --stacktrace
    
    - name: Upload Debug APK
      uses: actions/upload-artifact@v3
      with:
        name: WinDroidPro-Debug
        path: app/build/outputs/apk/debug/*.apk
        retention-days: 90
    
    - name: Build Release APK (unsigned)
      run: ./gradlew assembleRelease --stacktrace
      continue-on-error: true
    
    - name: Upload Release APK
      uses: actions/upload-artifact@v3
      if: success()
      with:
        name: WinDroidPro-Release
        path: app/build/outputs/apk/release/*.apk
        retention-days: 90
EOF
```

#### Step 5: Commit and Push
```bash
git add .github/workflows/build.yml
git commit -m "Add GitHub Actions workflow"
git push
```

#### Step 6: Verify
```bash
# Check on GitHub
gh repo view --web
# Go to Actions tab - you should see the workflow!
```

---

### Method 2: Using Git (Traditional)

#### Step 1: Install Git
```bash
pkg update
pkg install git
```

#### Step 2: Configure Git
```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

#### Step 3: Clone Repository
```bash
cd ~
git clone https://github.com/YOUR_USERNAME/WinDroidPro.git
cd WinDroidPro
```

#### Step 4: Create Workflow File
```bash
# Create directory
mkdir -p .github/workflows

# Create workflow file (same as Method 1)
nano .github/workflows/build.yml
# Or use vim, or cat with heredoc as shown above
```

#### Step 5: Commit and Push
```bash
git add .github/workflows/build.yml
git commit -m "Add GitHub Actions workflow"
git push
```

When prompted for password, use a **Personal Access Token** (not your password):
1. Go to GitHub.com on browser
2. Settings → Developer settings → Personal access tokens → Tokens (classic)
3. Generate new token
4. Select "repo" scope
5. Copy token
6. Use as password in Termux

---

### Method 3: Using GitHub Web Interface (No Termux Needed)

#### Step 1: Go to Your Repository
Open browser and go to:
```
https://github.com/YOUR_USERNAME/WinDroidPro
```

#### Step 2: Create Workflow File
1. Click "Add file" → "Create new file"
2. In filename box, type: `.github/workflows/build.yml`
3. GitHub will automatically create the folders
4. Paste the workflow content (see below)
5. Scroll down and click "Commit new file"

#### Workflow Content to Paste:
```yaml
name: Build APK

on:
  push:
    branches: [ main, master ]
  pull_request:
    branches: [ main, master ]
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
        cache: gradle
    
    - name: Setup Android SDK
      uses: android-actions/setup-android@v2
    
    - name: Grant execute permission for gradlew
      run: chmod +x gradlew
    
    - name: Cache Gradle packages
      uses: actions/cache@v3
      with:
        path: |
          ~/.gradle/caches
          ~/.gradle/wrapper
        key: ${{ runner.os }}-gradle-${{ hashFiles('**/*.gradle*', '**/gradle-wrapper.properties') }}
        restore-keys: |
          ${{ runner.os }}-gradle-
    
    - name: Build Debug APK
      run: ./gradlew assembleDebug --stacktrace
    
    - name: Upload Debug APK
      uses: actions/upload-artifact@v3
      with:
        name: WinDroidPro-Debug
        path: app/build/outputs/apk/debug/*.apk
        retention-days: 90
    
    - name: Build Release APK (unsigned)
      run: ./gradlew assembleRelease --stacktrace
      continue-on-error: true
    
    - name: Upload Release APK
      uses: actions/upload-artifact@v3
      if: success()
      with:
        name: WinDroidPro-Release
        path: app/build/outputs/apk/release/*.apk
        retention-days: 90
```

---

### Method 4: Quick Script (Termux)

Save this as `add_workflow.sh`:

```bash
#!/data/data/com.termux/files/usr/bin/bash

echo "Adding GitHub Actions Workflow"
echo "==============================="
echo ""

# Check if git is installed
if ! command -v git &> /dev/null; then
    echo "Installing git..."
    pkg install -y git
fi

# Get repository URL
read -p "Enter your GitHub username: " USERNAME
read -p "Enter repository name (default: WinDroidPro): " REPO
REPO=${REPO:-WinDroidPro}

# Clone repository
echo "Cloning repository..."
cd ~
if [ -d "$REPO" ]; then
    echo "Repository already exists, using it..."
    cd "$REPO"
    git pull
else
    git clone "https://github.com/$USERNAME/$REPO.git"
    cd "$REPO"
fi

# Create workflow directory
echo "Creating workflow directory..."
mkdir -p .github/workflows

# Create workflow file
echo "Creating workflow file..."
cat > .github/workflows/build.yml << 'WORKFLOW'
name: Build APK

on:
  push:
    branches: [ main, master ]
  pull_request:
    branches: [ main, master ]
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
        cache: gradle
    
    - name: Setup Android SDK
      uses: android-actions/setup-android@v2
    
    - name: Grant execute permission for gradlew
      run: chmod +x gradlew
    
    - name: Cache Gradle packages
      uses: actions/cache@v3
      with:
        path: |
          ~/.gradle/caches
          ~/.gradle/wrapper
        key: ${{ runner.os }}-gradle-${{ hashFiles('**/*.gradle*', '**/gradle-wrapper.properties') }}
        restore-keys: |
          ${{ runner.os }}-gradle-
    
    - name: Build Debug APK
      run: ./gradlew assembleDebug --stacktrace
    
    - name: Upload Debug APK
      uses: actions/upload-artifact@v3
      with:
        name: WinDroidPro-Debug
        path: app/build/outputs/apk/debug/*.apk
        retention-days: 90
    
    - name: Build Release APK (unsigned)
      run: ./gradlew assembleRelease --stacktrace
      continue-on-error: true
    
    - name: Upload Release APK
      uses: actions/upload-artifact@v3
      if: success()
      with:
        name: WinDroidPro-Release
        path: app/build/outputs/apk/release/*.apk
        retention-days: 90
WORKFLOW

# Commit and push
echo "Committing changes..."
git add .github/workflows/build.yml
git commit -m "Add GitHub Actions workflow"

echo ""
echo "Pushing to GitHub..."
echo "You may need to enter your GitHub credentials"
echo "Use Personal Access Token as password!"
git push

echo ""
echo "Done! Check your repository Actions tab."
```

Run it:
```bash
chmod +x add_workflow.sh
./add_workflow.sh
```

---

## 🎯 Recommended Method

**For Termux: Use Method 3 (Web Interface)**

Why?
- ✅ No git setup needed
- ✅ No authentication issues
- ✅ Works immediately
- ✅ Just copy and paste
- ✅ Takes 2 minutes

**Steps:**
1. Open browser on your phone
2. Go to your GitHub repository
3. Click "Add file" → "Create new file"
4. Type: `.github/workflows/build.yml`
5. Paste the workflow content
6. Click "Commit new file"
7. Done!

---

## ✅ Verify It Works

After adding the workflow:

1. Go to your repository on GitHub
2. Click "Actions" tab
3. You should see "Build APK" workflow
4. Click "Run workflow" button
5. Select branch (main/master)
6. Click green "Run workflow"
7. Wait 5-10 minutes
8. Download APK from artifacts!

---

## 🐛 Troubleshooting

### Git Authentication Failed
**Solution:** Use Personal Access Token
1. GitHub.com → Settings → Developer settings
2. Personal access tokens → Tokens (classic)
3. Generate new token
4. Select "repo" scope
5. Copy token
6. Use as password in Termux

### Permission Denied
**Solution:**
```bash
chmod +x gradlew
git add gradlew
git commit -m "Make gradlew executable"
git push
```

### Workflow Not Showing
**Solution:**
- Check file is at: `.github/workflows/build.yml`
- Check file extension is `.yml` not `.txt`
- Refresh Actions tab
- Wait a few seconds for GitHub to detect it

---

## 💡 Quick Copy-Paste

Just copy this entire workflow and paste in GitHub web interface:

```yaml
name: Build APK
on:
  push:
    branches: [ main, master ]
  workflow_dispatch:
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - uses: actions/setup-java@v3
      with:
        java-version: '17'
        distribution: 'temurin'
    - uses: android-actions/setup-android@v2
    - run: chmod +x gradlew
    - run: ./gradlew assembleDebug
    - uses: actions/upload-artifact@v3
      with:
        name: app-debug
        path: app/build/outputs/apk/debug/*.apk
```

This is a minimal version that works!

---

**Easiest way: Use GitHub web interface - takes 2 minutes! 🚀**