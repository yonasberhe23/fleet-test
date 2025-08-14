# CDI Bundle Example (Original Issue Reproduction)

This directory contains the exact configuration from the original [GitHub issue #14907](https://github.com/rancher/dashboard/issues/14907).

## Original Issue Description

> I have tried to modify the naming of the created bundles
> 
> ```yaml
> # fleet.yaml
> defaultNamespace: cdi-system
> name: cdi
> 
> helm:
>   releaseName: cdi
>   chart: "cdi"
>   repo: "https://suse-edge.github.io/charts"
>   version: "0.5.1"
> ```
> 
> After adding the `name`, the bundle no longer appears in the GitRepo details view of the Rancher UI, which lists its bundles.

## Test Configuration

This bundle uses:
- `name: cdi` - This custom name should break the UI link
- `defaultNamespace: cdi-system`
- Helm chart: CDI from SUSE Edge charts

## Expected Behavior

The bundle should appear in the GitRepo details view under the "Bundles" tab.

## Actual Behavior (Bug)

The bundle does not appear in the GitRepo details view because the UI linking mechanism relies on the GitRepo name rather than the `fleet.cattle.io/repo-name` label.

## Testing Steps

1. Create a GitRepo pointing to this directory
2. Navigate to the GitRepo details page
3. Click on the "Bundles" tab
4. **Expected**: Bundle named "cdi" should be visible
5. **Actual**: No bundles are shown
