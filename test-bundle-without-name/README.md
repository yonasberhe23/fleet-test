# Test Bundle without Custom Name (Baseline)

This directory contains a Fleet bundle configuration that demonstrates the baseline behavior (working correctly).

## Configuration

This bundle uses:
- No `name` field - uses the GitRepo name by default
- `defaultNamespace: test-namespace`
- Helm chart configuration

## Expected Behavior

The bundle should appear in the GitRepo details view under the "Bundles" tab.

## Actual Behavior (Working)

The bundle appears correctly in the GitRepo details view because the bundle name matches the GitRepo name.
