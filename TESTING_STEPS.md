# Testing Steps for Fleet Bundle Name Overwrite Bug

This document provides step-by-step instructions to reproduce the bug described in [GitHub issue #14907](https://github.com/rancher/dashboard/issues/14907).

## Prerequisites

1. A Rancher dashboard instance with Fleet enabled
2. Access to create GitRepos and view Fleet resources
3. This repository cloned and accessible

## Test Setup

### Step 1: Create GitRepo for Baseline Test

1. In the Rancher UI, navigate to **Fleet** → **Git Repos**
2. Click **Create** to create a new GitRepo
3. Configure the GitRepo:
   - **Name**: `test-baseline-repo`
   - **Repository URL**: `https://github.com/yonasberhe23/fleet-test`
   - **Branch**: `main`
   - **Path**: `test-bundle-without-name`
4. Click **Create**

### Step 2: Create GitRepo for Bug Test

1. Create another GitRepo:
   - **Name**: `test-bug-repo`
   - **Repository URL**: `https://github.com/yonasberhe23/fleet-test`
   - **Branch**: `main`
   - **Path**: `test-bundle-with-name`
2. Click **Create**

## Testing the Bug

### Step 3: Verify Baseline Behavior (Working)

1. Navigate to the **test-baseline-repo** GitRepo details page
2. Click on the **Bundles** tab
3. **Expected Result**: You should see a bundle listed with the name matching the GitRepo name (`test-baseline-repo`)

### Step 4: Verify Bug Behavior (Broken)

1. Navigate to the **test-bug-repo** GitRepo details page
2. Click on the **Bundles** tab
3. **Expected Result**: You should see a bundle listed with the name `custom-bundle-name`
4. **Actual Result (Bug)**: The bundle does not appear in the list

### Step 5: Verify Bundle Exists

1. Navigate to **Fleet** → **Bundles**
2. Search for bundles with the name `custom-bundle-name`
3. **Expected Result**: The bundle should exist and be visible in the global bundles list
4. **Actual Result**: The bundle exists but is not linked to the GitRepo in the UI

## Root Cause Analysis

The issue occurs because:

1. The UI links bundles to GitRepos using the `fleet.cattle.io/repo-name` label
2. This label is set to the GitRepo's name (`test-bug-repo`)
3. However, when a bundle has a custom `name` field, Fleet creates the bundle with that custom name (`custom-bundle-name`)
4. The UI's bundle linking logic in `shell/models/fleet.cattle.io.gitrepo.js` line 175 uses:
   ```javascript
   get bundles() {
     return this.$getters['matching'](FLEET.BUNDLE, { [FLEET_ANNOTATIONS.REPO_NAME]: this.name }, this.namespace);
   }
   ```
5. This query looks for bundles with the `fleet.cattle.io/repo-name` label equal to the GitRepo name, but the bundle name is different

## Expected Fix

The UI should rely on the `fleet.cattle.io/repo-name` label for linking, which should be set correctly by Fleet regardless of the bundle's custom name.

## Cleanup

After testing, you can delete both GitRepos:
1. Navigate to **Fleet** → **Git Repos**
2. Select each GitRepo and click **Delete**
3. Confirm the deletion
