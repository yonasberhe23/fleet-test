# Test Bundle with Custom Name

This directory contains a Fleet bundle configuration that demonstrates the bug described in [GitHub issue #14907](https://github.com/rancher/dashboard/issues/14907).

## Issue Description

When a Fleet bundle has a custom `name` field in its `fleet.yaml`, the bundle no longer appears in the GitRepo details view in the Rancher UI.

## Test Configuration

This bundle uses:
- `name: custom-bundle-name` - This custom name should break the UI link
- `defaultNamespace: test-namespace`
- Helm chart configuration

## Expected Behavior

The bundle should appear in the GitRepo details view under the "Bundles" tab.

## Actual Behavior (Bug)

The bundle does not appear in the GitRepo details view because the UI linking mechanism relies on the GitRepo name rather than the `fleet.cattle.io/repo-name` label.
