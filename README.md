# Fleet Bundle Name Overwrite Bug Test Repository

This repository contains test cases to reproduce the bug described in [GitHub issue #14907](https://github.com/rancher/dashboard/issues/14907).

## Issue Summary

When a Fleet bundle has a custom `name` field in its `fleet.yaml`, the bundle no longer appears in the GitRepo details view in the Rancher UI.

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

3. **Verify the bug**:
   - Navigate to `test-baseline-repo` → Bundles tab → Bundle should appear ✅
   - Navigate to `test-bug-repo` → Bundles tab → Bundle should NOT appear ❌ (bug)

## Root Cause

The UI links bundles to GitRepos using the `fleet.cattle.io/repo-name` label, which is set to the GitRepo's name. However, when a bundle has a custom `name` field, Fleet creates the bundle with that custom name, but the label still uses the GitRepo name, breaking the link.

## Files Structure

```
├── test-bundle-without-name/     # Baseline test (working)
│   ├── fleet.yaml               # No custom name
│   ├── manifests/
│   │   └── configmap.yaml       # Test ConfigMap
│   └── README.md
├── test-bundle-with-name/        # Bug test (broken)
│   ├── fleet.yaml               # Custom name: custom-bundle-name
│   ├── manifests/
│   │   └── configmap.yaml       # Test ConfigMap
│   └── README.md
├── cdi-bundle-example/          # Original issue reproduction
│   ├── fleet.yaml               # Custom name: cdi
│   └── README.md
├── TESTING_STEPS.md             # Detailed testing instructions
└── README.md                    # This file
```

## Detailed Testing

See [TESTING_STEPS.md](TESTING_STEPS.md) for comprehensive testing instructions.
