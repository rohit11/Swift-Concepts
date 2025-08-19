# Complete CI/CD, Testing, and App Store Submission Guide

## Table of Contents
1. [CI/CD Pipeline Setup](#cicd-pipeline-setup)
2. [Playwright Testing](#playwright-testing)
3. [Appium Mobile Testing](#appium-mobile-testing)
4. [Fastlane Automation](#fastlane-automation)
5. [Apple App Store Submission](#apple-app-store-submission)
6. [Google Play Store Submission](#google-play-store-submission)
7. [Complete Workflow Example](#complete-workflow-example)

---

## CI/CD Pipeline Setup

### What is CI/CD?
Continuous Integration (CI) and Continuous Deployment (CD) automate the process of building, testing, and deploying applications.

### Popular CI/CD Platforms

#### GitHub Actions
```yaml
# .github/workflows/ios-ci.yml
name: iOS CI/CD

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: macos-latest
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup Xcode
      uses: maxim-lobanov/setup-xcode@v1
      with:
        xcode-version: '15.0'
    
    - name: Install Dependencies
      run: |
        cd ios
        pod install
    
    - name: Build and Test
      run: |
        xcodebuild -workspace MyApp.xcworkspace \
          -scheme MyApp \
          -destination 'platform=iOS Simulator,name=iPhone 15' \
          test
```

#### GitLab CI
```yaml
# .gitlab-ci.yml
stages:
  - test
  - build
  - deploy

variables:
  XCODE_VERSION: "15.0"

test_ios:
  stage: test
  image: xcode:15.0
  script:
    - cd ios
    - pod install
    - xcodebuild -workspace MyApp.xcworkspace \
        -scheme MyApp \
        -destination 'platform=iOS Simulator,name=iPhone 15' \
        test
  only:
    - main
    - develop
```

#### Jenkins
```groovy
// Jenkinsfile
pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh 'cd ios && pod install'
            }
        }
        
        stage('Build and Test') {
            steps {
                sh '''
                    xcodebuild -workspace ios/MyApp.xcworkspace \
                        -scheme MyApp \
                        -destination 'platform=iOS Simulator,name=iPhone 15' \
                        test
                '''
            }
        }
        
        stage('Archive') {
            steps {
                sh '''
                    xcodebuild -workspace ios/MyApp.xcworkspace \
                        -scheme MyApp \
                        -configuration Release \
                        -archivePath build/MyApp.xcarchive \
                        archive
                '''
            }
        }
    }
}
```

---

## Playwright Testing

### What is Playwright?
Playwright is a cross-browser testing framework that supports web applications and can be used for testing mobile web apps.

### Installation
```bash
npm init playwright@latest
npm install -D @playwright/test
```

### Configuration
```typescript
// playwright.config.ts
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  testDir: './tests',
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: 'html',
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry',
  },
  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    },
    {
      name: 'mobile-chrome',
      use: { ...devices['Pixel 5'] },
    },
    {
      name: 'mobile-safari',
      use: { ...devices['iPhone 12'] },
    },
  ],
  webServer: {
    command: 'npm run start',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
  },
});
```

### Test Examples
```typescript
// tests/mobile-app.spec.ts
import { test, expect } from '@playwright/test';

test.describe('Mobile App Tests', () => {
  test('should load main page on mobile', async ({ page }) => {
    await page.goto('/');
    await expect(page.locator('h1')).toContainText('Welcome');
  });

  test('should handle touch gestures', async ({ page }) => {
    await page.goto('/');
    
    // Swipe gesture
    await page.locator('.swipeable').swipe('left');
    
    // Tap gesture
    await page.locator('.button').tap();
    
    await expect(page.locator('.result')).toBeVisible();
  });

  test('should work offline', async ({ page }) => {
    await page.goto('/');
    
    // Simulate offline mode
    await page.context().setOffline(true);
    
    await page.reload();
    await expect(page.locator('.offline-indicator')).toBeVisible();
  });
});
```

### Running Tests
```bash
# Run all tests
npx playwright test

# Run specific test file
npx playwright test mobile-app.spec.ts

# Run with specific device
npx playwright test --project=mobile-chrome

# Run in headed mode
npx playwright test --headed

# Generate report
npx playwright show-report
```

---

## Appium Mobile Testing

### What is Appium?
Appium is an open-source automation tool for testing native, hybrid, and mobile web applications.

### Installation
```bash
npm install -g appium
npm install -g appium-doctor
appium-doctor
```

### Appium Server Setup
```javascript
// appium-server.js
const { AppiumServer } = require('appium');

const server = new AppiumServer({
  host: 'localhost',
  port: 4723,
  log: './appium.log',
  logLevel: 'info'
});

server.start();
```

### iOS Configuration
```javascript
// ios-config.js
const iosConfig = {
  platformName: 'iOS',
  platformVersion: '15.0',
  deviceName: 'iPhone 15',
  automationName: 'XCUITest',
  app: '/path/to/your/app.ipa',
  udid: 'device-udid-here',
  xcodeOrgId: 'your-team-id',
  xcodeSigningId: 'iPhone Developer'
};
```

### Android Configuration
```javascript
// android-config.js
const androidConfig = {
  platformName: 'Android',
  platformVersion: '12.0',
  deviceName: 'Android Emulator',
  automationName: 'UiAutomator2',
  app: '/path/to/your/app.apk',
  appPackage: 'com.yourapp.package',
  appActivity: 'com.yourapp.MainActivity'
};
```

### Test Examples
```javascript
// appium-tests.js
const wd = require('wd');

describe('Mobile App Tests', () => {
  let driver;

  beforeAll(async () => {
    driver = await wd.promiseChainRemote('http://localhost:4723/wd/hub');
    
    // iOS Configuration
    await driver.init({
      platformName: 'iOS',
      platformVersion: '15.0',
      deviceName: 'iPhone 15',
      automationName: 'XCUITest',
      app: '/path/to/your/app.ipa'
    });
  });

  afterAll(async () => {
    await driver.quit();
  });

  test('should navigate through app', async () => {
    // Find and tap button
    const button = await driver.elementByAccessibilityId('login-button');
    await button.click();

    // Wait for screen to load
    await driver.waitForElementByAccessibilityId('home-screen', 5000);

    // Verify element is present
    const homeElement = await driver.elementByAccessibilityId('home-screen');
    expect(await homeElement.isDisplayed()).toBe(true);
  });

  test('should handle form input', async () => {
    // Find input field
    const input = await driver.elementByAccessibilityId('email-input');
    
    // Type text
    await input.type('test@example.com');

    // Verify input
    const value = await input.text();
    expect(value).toBe('test@example.com');
  });

  test('should perform gestures', async () => {
    // Swipe gesture
    await driver.execute('mobile: swipe', {
      direction: 'left',
      element: await driver.elementByAccessibilityId('swipeable-area')
    });

    // Scroll gesture
    await driver.execute('mobile: scroll', {
      direction: 'down',
      element: await driver.elementByAccessibilityId('scrollable-area')
    });
  });
});
```

### Running Appium Tests
```bash
# Start Appium server
appium

# Run tests with Jest
npm test

# Run specific test file
npm test -- appium-tests.js
```

---

## Fastlane Automation

### What is Fastlane?
Fastlane is a tool that automates common development tasks like building, testing, and deploying mobile apps.

### Installation
```bash
# macOS
brew install fastlane

# Ruby
gem install fastlane

# Or via Bundler
bundle add fastlane
```

### Fastfile Configuration
```ruby
# fastlane/Fastfile
default_platform(:ios)

platform :ios do
  desc "Run tests"
  lane :test do
    scan(
      scheme: "MyApp",
      devices: ["iPhone 15"],
      clean: true
    )
  end

  desc "Build app"
  lane :build do
    gym(
      scheme: "MyApp",
      configuration: "Release",
      export_method: "app-store",
      output_directory: "builds",
      output_name: "MyApp.ipa"
    )
  end

  desc "Deploy to TestFlight"
  lane :beta do
    build
    pilot(
      skip_waiting_for_build_processing: true
    )
  end

  desc "Deploy to App Store"
  lane :release do
    build
    deliver(
      force: true,
      skip_metadata: false,
      skip_screenshots: false
    )
  end

  desc "Submit to App Store"
  lane :submit do
    release
    app_store_connect_api_key(
      key_id: ENV["APP_STORE_CONNECT_KEY_ID"],
      issuer_id: ENV["APP_STORE_CONNECT_ISSUER_ID"],
      key_content: ENV["APP_STORE_CONNECT_API_KEY"]
    )
  end
end

platform :android do
  desc "Run tests"
  lane :test do
    gradle(task: "test")
  end

  desc "Build APK"
  lane :build do
    gradle(
      task: "assemble",
      build_type: "Release"
    )
  end

  desc "Build AAB"
  lane :build_aab do
    gradle(
      task: "bundle",
      build_type: "Release"
    )
  end

  desc "Deploy to Play Store"
  lane :deploy do
    build_aab
    upload_to_play_store(
      track: 'internal',
      aab: '../app/build/outputs/bundle/release/app-release.aab'
    )
  end
end
```

### Appfile Configuration
```ruby
# fastlane/Appfile
app_identifier("com.yourapp.identifier")
apple_id("your@email.com")
team_id("TEAM_ID")

# For Android
json_key_file("path/to/play-store-credentials.json")
package_name("com.yourapp.package")
```

### Environment Variables
```bash
# .env
APP_STORE_CONNECT_KEY_ID=your_key_id
APP_STORE_CONNECT_ISSUER_ID=your_issuer_id
APP_STORE_CONNECT_API_KEY=your_api_key
MATCH_PASSWORD=your_match_password
FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD=your_app_specific_password
```

### Running Fastlane
```bash
# Run specific lane
fastlane ios test
fastlane ios beta
fastlane ios release
fastlane android deploy

# Run with parameters
fastlane ios beta build_number:123

# Run with environment
fastlane ios beta --env production
```

---

## Apple App Store Submission

### Prerequisites
1. Apple Developer Account ($99/year)
2. App Store Connect access
3. Xcode with proper certificates
4. App Store Connect API Key (for automation)

### Manual Submission Process

#### 1. Prepare App
```bash
# Archive app in Xcode
# Product → Archive
# Or via command line:
xcodebuild -workspace MyApp.xcworkspace \
  -scheme MyApp \
  -configuration Release \
  -archivePath build/MyApp.xcarchive \
  archive
```

#### 2. Export IPA
```bash
# Export from archive
xcodebuild -exportArchive \
  -archivePath build/MyApp.xcarchive \
  -exportPath build/export \
  -exportOptionsPlist exportOptions.plist
```

#### 3. App Store Connect Setup
- Create new app
- Fill app information
- Upload screenshots
- Set pricing
- Configure in-app purchases

#### 4. Submit for Review
- Upload build via Xcode or Transporter
- Submit for review
- Wait for approval (1-7 days)

### Automated Submission with Fastlane

#### Screenshots
```ruby
# fastlane/Snapfile
devices([
  "iPhone 15 Pro Max",
  "iPhone 15 Pro",
  "iPhone 15 Plus",
  "iPhone 15",
  "iPad Pro (12.9-inch) (6th generation)",
  "iPad Pro (11-inch) (4th generation)"
])

languages([
  "en-US",
  "de-DE",
  "fr-FR",
  "es-ES"
])

scheme("MyApp")
workspace("MyApp.xcworkspace")
```

#### Metadata
```ruby
# fastlane/Deliverfile
app_identifier "com.yourapp.identifier"
username "your@email.com"

# App Information
name(
  'en-US' => 'My App Name',
  'de-DE' => 'Meine App Name'
)

subtitle(
  'en-US' => 'App Subtitle',
  'de-DE' => 'App Untertitel'
)

keywords(
  'en-US' => 'keyword1, keyword2, keyword3',
  'de-DE' => 'schlüsselwort1, schlüsselwort2, schlüsselwort3'
)

privacy_url(
  'en-US' => 'https://yourapp.com/privacy',
  'de-DE' => 'https://yourapp.com/privacy-de'
)

support_url(
  'en-US' => 'https://yourapp.com/support',
  'de-DE' => 'https://yourapp.com/support-de'
)

marketing_url(
  'en-US' => 'https://yourapp.com',
  'de-DE' => 'https://yourapp.com/de'
)

release_notes(
  'en-US' => 'Bug fixes and performance improvements',
  'de-DE' => 'Fehlerbehebungen und Leistungsverbesserungen'
)
```

#### Complete Submission Lane
```ruby
desc "Submit to App Store"
lane :submit do
  # Ensure clean build
  clean_build_artifacts
  
  # Build app
  build
  
  # Upload to App Store Connect
  upload_to_app_store(
    force: true,
    skip_metadata: false,
    skip_screenshots: false,
    precheck_include_in_app_purchases: false,
    api_key: app_store_connect_api_key(
      key_id: ENV["APP_STORE_CONNECT_KEY_ID"],
      issuer_id: ENV["APP_STORE_CONNECT_ISSUER_ID"],
      key_content: ENV["APP_STORE_CONNECT_API_KEY"]
    )
  )
  
  # Notify team
  slack(
    message: "App submitted to App Store successfully!",
    success: true
  )
end
```

---

## Google Play Store Submission

### Prerequisites
1. Google Play Console account ($25 one-time fee)
2. App signing key
3. Privacy policy
4. Content rating questionnaire

### Manual Submission Process

#### 1. Prepare App Bundle
```bash
# Build AAB
./gradlew bundleRelease

# Or via command line
./gradlew assembleRelease
```

#### 2. Play Console Setup
- Create new app
- Fill store listing
- Upload screenshots
- Set content rating
- Configure pricing

#### 3. Submit for Review
- Upload AAB/APK
- Submit for review
- Wait for approval (1-7 days)

### Automated Submission with Fastlane

#### Play Console Configuration
```ruby
# fastlane/Playfile
json_key_file("path/to/play-store-credentials.json")
package_name("com.yourapp.package")
```

#### Metadata Configuration
```ruby
# fastlane/Deliverfile
package_name "com.yourapp.package"

# Store Listing
title(
  'en-US' => 'My App Name',
  'de-DE' => 'Meine App Name'
)

short_description(
  'en-US' => 'Short app description',
  'de-DE' => 'Kurze App-Beschreibung'
)

full_description(
  'en-US' => 'Full app description with features and benefits',
  'de-DE' => 'Vollständige App-Beschreibung mit Funktionen und Vorteilen'
)

video_url(
  'en-US' => 'https://youtube.com/watch?v=video_id',
  'de-DE' => 'https://youtube.com/watch?v=video_id'
)

# Graphics
feature_graphic("path/to/feature_graphic.png")
icon("path/to/icon.png")
```

#### Complete Deployment Lane
```ruby
desc "Deploy to Play Store"
lane :deploy do
  # Build AAB
  build_aab
  
  # Upload to Play Store
  upload_to_play_store(
    track: 'production',
    aab: '../app/build/outputs/bundle/release/app-release.aab',
    release_status: 'completed',
    version_name: '1.0.0',
    version_code: 1
  )
  
  # Notify team
  slack(
    message: "App deployed to Play Store successfully!",
    success: true
  )
end
```

---

## Complete Workflow Example

### GitHub Actions Workflow
```yaml
# .github/workflows/complete-pipeline.yml
name: Complete CI/CD Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

env:
  XCODE_VERSION: '15.0'
  ANDROID_SDK_ROOT: ${{ secrets.ANDROID_SDK_ROOT }}

jobs:
  test-ios:
    runs-on: macos-latest
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup Xcode
      uses: maxim-lobanov/setup-xcode@v1
      with:
        xcode-version: ${{ env.XCODE_VERSION }}
    
    - name: Install Dependencies
      run: |
        cd ios
        pod install
    
    - name: Run Tests
      run: |
        xcodebuild -workspace ios/MyApp.xcworkspace \
          -scheme MyApp \
          -destination 'platform=iOS Simulator,name=iPhone 15' \
          test
    
    - name: Build Archive
      run: |
        xcodebuild -workspace ios/MyApp.xcworkspace \
          -scheme MyApp \
          -configuration Release \
          -archivePath build/MyApp.xcarchive \
          archive
    
    - name: Export IPA
      run: |
        xcodebuild -exportArchive \
          -archivePath build/MyApp.xcarchive \
          -exportPath build/export \
          -exportOptionsPlist ios/exportOptions.plist
    
    - name: Upload IPA
      uses: actions/upload-artifact@v3
      with:
        name: ios-app
        path: build/export/MyApp.ipa

  test-android:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup Java
      uses: actions/setup-java@v3
      with:
        distribution: 'zulu'
        java-version: '17'
    
    - name: Setup Android SDK
      uses: android-actions/setup-android@v2
    
    - name: Run Tests
      run: |
        cd android
        ./gradlew test
    
    - name: Build AAB
      run: |
        cd android
        ./gradlew bundleRelease
    
    - name: Upload AAB
      uses: actions/upload-artifact@v3
      with:
        name: android-app
        path: android/app/build/outputs/bundle/release/app-release.aab

  deploy-ios:
    needs: test-ios
    runs-on: macos-latest
    if: github.ref == 'refs/heads/main'
    steps:
    - uses: actions/checkout@v3
    
    - name: Download IPA
      uses: actions/download-artifact@v3
      with:
        name: ios-app
    
    - name: Setup Ruby
      uses: ruby/setup-ruby@v1
      with:
        ruby-version: '3.0'
        bundler-cache: true
    
    - name: Install Fastlane
      run: |
        gem install fastlane
    
    - name: Deploy to TestFlight
      env:
        APP_STORE_CONNECT_KEY_ID: ${{ secrets.APP_STORE_CONNECT_KEY_ID }}
        APP_STORE_CONNECT_ISSUER_ID: ${{ secrets.APP_STORE_CONNECT_ISSUER_ID }}
        APP_STORE_CONNECT_API_KEY: ${{ secrets.APP_STORE_CONNECT_API_KEY }}
      run: |
        fastlane ios beta

  deploy-android:
    needs: test-android
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
    - uses: actions/checkout@v3
    
    - name: Download AAB
      uses: actions/download-artifact@v3
      with:
        name: android-app
    
    - name: Setup Ruby
      uses: ruby/setup-ruby@v1
      with:
        ruby-version: '3.0'
        bundler-cache: true
    
    - name: Install Fastlane
      run: |
        gem install fastlane
    
    - name: Deploy to Play Store
      env:
        PLAY_STORE_CREDENTIALS: ${{ secrets.PLAY_STORE_CREDENTIALS }}
      run: |
        echo "$PLAY_STORE_CREDENTIALS" > play-store-credentials.json
        fastlane android deploy
```

### Local Development Workflow
```bash
#!/bin/bash
# deploy.sh

echo "🚀 Starting deployment process..."

# Run tests
echo "🧪 Running tests..."
fastlane ios test
fastlane android test

# Build apps
echo "🔨 Building apps..."
fastlane ios build
fastlane android build_aab

# Deploy to test environments
echo "📱 Deploying to test environments..."
fastlane ios beta
fastlane android deploy track:internal

# Deploy to production (uncomment when ready)
# echo "🚀 Deploying to production..."
# fastlane ios submit
# fastlane android deploy track:production

echo "✅ Deployment complete!"
```

### Environment Configuration
```bash
# .env.production
FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD=your_password
MATCH_PASSWORD=your_match_password
FASTLANE_APPLE_ID=your@email.com
FASTLANE_TEAM_ID=your_team_id
FASTLANE_ITC_TEAM_ID=your_itc_team_id

# Android
FASTLANE_KEYSTORE_PATH=path/to/keystore.jks
FASTLANE_KEY_ALIAS=your_key_alias
FASTLANE_STORE_PASSWORD=your_store_password
FASTLANE_KEY_PASSWORD=your_key_password
```

---

## Best Practices and Tips

### 1. Security
- Never commit sensitive credentials
- Use environment variables and secrets
- Rotate API keys regularly
- Use App Store Connect API keys instead of passwords

### 2. Testing
- Run tests on multiple devices
- Include accessibility testing
- Test offline scenarios
- Performance testing

### 3. Automation
- Automate everything possible
- Use semantic versioning
- Implement rollback strategies
- Monitor deployment success rates

### 4. Documentation
- Keep deployment guides updated
- Document environment variables
- Maintain troubleshooting guides
- Version control all configurations

### 5. Monitoring
- Track build times
- Monitor test success rates
- Alert on deployment failures
- Track app store review times

---

## Troubleshooting Common Issues

### iOS Issues
```bash
# Certificate issues
fastlane match nuke development
fastlane match development

# Provisioning profile issues
fastlane sigh --force

# Xcode build issues
xcodebuild clean
rm -rf ~/Library/Developer/Xcode/DerivedData
```

### Android Issues
```bash
# Gradle issues
./gradlew clean
./gradlew --stop

# Keystore issues
keytool -list -v -keystore your_keystore.jks

# Build issues
./gradlew assembleDebug --stacktrace
```

### Fastlane Issues
```bash
# Update fastlane
gem update fastlane

# Clear fastlane cache
fastlane clean

# Check environment
fastlane env
```

---

This comprehensive guide covers all aspects of CI/CD, testing, and app store submission. Remember to adapt the configurations to your specific project requirements and always test thoroughly before deploying to production.
