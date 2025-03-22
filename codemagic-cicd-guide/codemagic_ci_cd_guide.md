
# Configuring Codemagic CI/CD for Automatic Builds with GitHub

## 1. Overview

Codemagic is a CI/CD platform designed to streamline mobile app development workflows, including automatic builds triggered by repository events like pushes or tag creations. By integrating GitHub with Codemagic via webhooks and configuring a `codemagic.yaml` file, you can automate builds and ensure artifacts (e.g., APKs) are generated and uploaded to GitHub Releases or shared via email. This guide covers setting up webhooks, configuring the `codemagic.yaml` file, testing the setup, and avoiding common pitfalls.

For more details on Codemagic’s webhook integration, refer to the [official documentation](https://docs.codemagic.io/yaml-running-builds/webhooks/).

## 2. Setting Up Webhooks Correctly

Webhooks are essential for notifying Codemagic of GitHub events (e.g., push or tag push) to trigger builds automatically. Here’s how to configure them:

### Steps to Set Up Webhooks in GitHub:

1. **Locate Your Codemagic App ID:** 
   Open your app in Codemagic. The URL will be `https://codemagic.io/app/<appId>`. Copy the `<appId>`.
   Construct the payload URL: `https://api.codemagic.io/hooks/<appId>`.

2. **Configure the Webhook in GitHub:**
   - Go to your GitHub repository.
   - Navigate to `Settings > Webhooks > Add webhook`.
   - Payload URL: Paste the URL from step 1 (e.g., `https://api.codemagic.io/hooks/<appId>`).
   - Content type: Select either `application/json` or `application/x-www-form-urlencoded`.
   - Events: Choose the events to trigger builds:
     - Branch or tag creation
     - Pushes
     - (Optional) Pull requests if needed.
   - Save the webhook.

3. **Verify Webhook Setup:**
   In Codemagic, go to your app and select the **Webhooks** tab to see received webhook events.

For more detailed instructions, see [Codemagic’s webhook setup guide](https://docs.codemagic.io/yaml-running-builds/webhooks/).

## 3. codemagic.yaml File and Setting Up Trigger Correctly

The `codemagic.yaml` file defines your build workflow and specifies when builds should trigger. Place this file in the root directory of your GitHub repository.

### Example `codemagic.yaml` File:

```yaml
workflows:
  android-build:
    name: Android Build
    max_build_duration: 60
    instance_type: mac_mini_m2
    triggering:
      events:
        - push
        - tag
      branch_patterns:
        - pattern: 'main'
          include: true
      tag_patterns:
        - pattern: 'v*'
          include: true
    environment:
      vars:
        PACKAGE_NAME: 'com.example.app'
    scripts:
      - name: Install Dependencies
        script: npm install
      - name: Build APK
        script: |
          cd android
          ./gradlew assembleRelease
    artifacts:
      - android/app/build/outputs/**/*.apk
    publishing:
      email:
        recipients:
          - user@example.com
        notify:
          success: true
          failure: true
```

### Explanation of Key Sections:

- **workflows:** Defines a named workflow (e.g., `android-build`).
- **name:** Human-readable name displayed in Codemagic.
- **max_build_duration:** Maximum build time in minutes.
- **instance_type:** Specifies the build machine (e.g., `mac_mini_m2` for Android builds).
- **triggering:**
  - **events:** Lists events that trigger the build (e.g., `push`, `tag`).
  - **branch_patterns:** Filters branches to watch (e.g., `main`).
  - **tag_patterns:** Filters tags to watch (e.g., `v*` for version tags like `v1.0.0`).
- **environment:** Sets variables or credentials for the build.
- **scripts:** Commands to execute (e.g., installing dependencies, building an APK).
- **artifacts:** Files to save (e.g., APKs).
- **publishing:** Options to share artifacts (e.g., via email).

For more on configuring triggers, see [Codemagic’s YAML triggering docs](https://docs.codemagic.io/flutter-running-builds/starting-builds-automatically/).

## 4. Testing - Using Git Push and Checking Codemagic Builds Tab

After configuring the webhook and `codemagic.yaml`, test the setup:

### Steps to Test:

1. **Push a Change:**
   Commit a change to the `main` branch (or a branch matching your `branch_patterns`):

   ```bash
   git add .
   git commit -m "Test automatic build"
   git push origin main
   ```

   Alternatively, create and push a tag:

   ```bash
   git tag v1.0.0
   git push origin v1.0.0
   ```

2. **Check Codemagic Builds Tab:**
   Open your app in Codemagic (`https://codemagic.io/app/<appId>`).
   Go to the **Builds** tab.
   Look for a new build triggered almost instantly after the push. The build status will update as it progresses.

3. **Verify Artifacts:**
   Once the build completes, check the **Artifacts** section for the APK (e.g., `android/app/build/outputs/**/*.apk`).
   Confirm receipt of the email notification (if configured).

If no build triggers, revisit your webhook and triggering settings.

## 5. Cautions and Errors to Avoid

Avoid these common issues to ensure smooth CI/CD operation:

1. **Incorrect Regex in `codemagic.yaml`:**
   - **Problem:** Setting `branch_patterns` or `tag_patterns` with wrong patterns (e.g., `v*.*` instead of `v*`) may exclude valid branches/tags.
   - **Fix:** Test patterns carefully. Use `*` for broad matches or specific names (e.g., `main`, `v1.*`).

2. **Misconfigured GitHub Webhook Events:**
   - **Problem:** Forgetting to select `Pushes` or `Branch or tag creation` in GitHub’s webhook settings.
   - **Fix:** Ensure all relevant events are checked in **Settings > Webhooks**.

3. **Webhook Payload URL Mismatch:**
   - **Problem:** Using an incorrect `<appId>` in the payload URL.
   - **Fix:** Double-check the URL matches your Codemagic app ID.

4. **Outdated Builds Not Canceled:**
   - **Problem:** Multiple builds queue up for the same branch, slowing down the pipeline.
   - **Fix:** Set `cancel_previous_builds: true` in the triggering section.

5. **Missing Permissions or Dependencies:**
   - **Problem:** Build fails due to missing executable permissions (e.g., `gradlew`) or dependencies.
   - **Fix:** Add `chmod +x android/gradlew` in scripts and ensure all dependencies are installed.

For troubleshooting, consult [Codemagic’s YAML docs](https://docs.codemagic.io/flutter-running-builds/starting-builds-automatically/).
