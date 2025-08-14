# Fleet Bundle Name Overwrite Bug Test Repository (2.10 Version)

This repository contains test cases to reproduce the bug described in [GitHub issue #14907](https://github.com/rancher/dashboard/issues/14907) in the Rancher Dashboard 2.10 version.

## Issue Summary

When a Fleet bundle has a custom `name` field in its `fleet.yaml`, the bundle no longer appears in the GitRepo details view in the Rancher UI.

## Root Cause (2.10 Version)

The issue occurs because:

1. The `FleetBundles` component uses `this.value.bundles` which calls the GitRepo model's `bundles` getter
2. The getter looks for bundles with the `fleet.cattle.io/repo-name` label equal to the GitRepo name
3. When a bundle has a custom `name` field, Fleet creates the bundle with that custom name, but the label still uses the GitRepo name
4. This breaks the link between the GitRepo and its bundles in the UI

**Problematic Code:**
```javascript
// shell/models/fleet.cattle.io.gitrepo.js line 308
get bundles() {
  return this.$getters['matching'](FLEET.BUNDLE, { 'fleet.cattle.io/repo-name': this.name }, this.namespace);
}
```

## Test Cases

### 1. Baseline Test (Working)
- **Directory**: `test-bundle-without-name/`
- **Configuration**: No custom `name` field in `fleet.yaml`
- **Expected**: Bundle appears correctly in GitRepo details view

### 2. Bug Test (Broken)
- **Directory**: `test-bundle-with-name/`
- **Configuration**: Custom `name: custom-bundle-name` in `fleet.yaml`
- **Expected**: Bundle should appear in GitRepo details view
- **Actual**: Bundle does not appear (bug)

### 3. Original Issue Reproduction
- **Directory**: `cdi-bundle-example/`
- **Configuration**: Exact configuration from the original issue
- **Expected**: Bundle named "cdi" should appear in GitRepo details view
- **Actual**: Bundle does not appear (bug)

## Quick Test Instructions

1. **Create GitRepo for baseline test**:
   - Name: `test-baseline-repo`
   - Repository: `https://github.com/yonasberhe23/fleet-test`
   - Path: `test-bundle-without-name`

2. **Create GitRepo for bug test**:
   - Name: `test-bug-repo`
   - Repository: `https://github.com/yonasberhe23/fleet-test`
   - Path: `test-bundle-with-name`

3. **Create GitRepo for original issue test**:
   - Name: `cdi-test-repo`
   - Repository: `https://github.com/yonasberhe23/fleet-test`
   - Path: `cdi-bundle-example`

4. **Verify the bug**:
   - Navigate to `test-baseline-repo` → Bundles tab → Bundle should appear ✅
   - Navigate to `test-bug-repo` → Bundles tab → Bundle should NOT appear ❌ (bug)
   - Navigate to `cdi-test-repo` → Bundles tab → Bundle should NOT appear ❌ (bug)

## Files Structure

```
├── test-bundle-without-name/     # Baseline test (working)
│   └── fleet.yaml               # No custom name
├── test-bundle-with-name/        # Bug test (broken)
│   └── fleet.yaml               # Custom name: custom-bundle-name
├── cdi-bundle-example/          # Original issue reproduction
│   └── fleet.yaml               # Custom name: cdi
├── TESTING_STEPS_2.10.md        # Detailed testing instructions for 2.10
└── README.md                    # This file
```

## Detailed Testing

See [TESTING_STEPS_2.10.md](TESTING_STEPS_2.10.md) for comprehensive testing instructions specific to the 2.10 version.

## Expected Fix

The UI should rely on the `fleet.cattle.io/repo-name` label for linking, which should be set correctly by Fleet regardless of the bundle's custom name. The label should contain the GitRepo name, not the bundle name.
