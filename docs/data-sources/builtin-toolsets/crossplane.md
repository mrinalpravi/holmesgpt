# Crossplane

By enabling this toolset, HolmesGPT will be able to troubleshoot Crossplane-managed infrastructure by inspecting providers, compositions, claims, composite resources, and managed resources across the full resource hierarchy.

## Prerequisites

Crossplane must be installed on your Kubernetes cluster. HolmesGPT uses `kubectl` to query Crossplane custom resources, so no additional CLI tools are required.

HolmesGPT needs read access to Crossplane CRDs. If you use Kubernetes RBAC, ensure the service account has permissions to `get` and `list` the following API groups:

```yaml
# Add to your ClusterRole
- apiGroups: ["pkg.crossplane.io"]
  resources: ["providers", "providerrevisions"]
  verbs: ["get", "list"]
- apiGroups: ["apiextensions.crossplane.io"]
  resources: ["compositeresourcedefinitions", "compositions"]
  verbs: ["get", "list"]
# For managed resources, add the specific API groups used by your providers.
# Example for AWS provider:
- apiGroups: ["s3.aws.upbound.io", "rds.aws.upbound.io", "ec2.aws.upbound.io"]
  resources: ["*"]
  verbs: ["get", "list"]
```

## Configuration

=== "Holmes CLI"

    Add the following to **~/.holmes/config.yaml**:

    ```yaml
    toolsets:
        crossplane/core:
            enabled: true
    ```

    --8<-- "snippets/toolset_refresh_warning.md"

    To test, run:

    ```bash
    holmes ask "Which Crossplane managed resources are failing and why?"
    ```

=== "Robusta Helm Chart"

    ```yaml
    holmes:
        customClusterRoleRules:
            - apiGroups: ["pkg.crossplane.io"]
              resources: ["providers", "providerrevisions"]
              verbs: ["get", "list"]
            - apiGroups: ["apiextensions.crossplane.io"]
              resources: ["compositeresourcedefinitions", "compositions"]
              verbs: ["get", "list"]
        toolsets:
            crossplane/core:
                enabled: true
    ```

    --8<-- "snippets/helm_upgrade_command.md"

## Common Use Cases

```bash
holmes ask "Which Crossplane managed resources are failing and why?"
```

```bash
holmes ask "Are all Crossplane providers healthy?"
```

```bash
holmes ask "Trace the claim my-database in namespace production and find which managed resource is broken"
```

```bash
holmes ask "Why is my S3 bucket not becoming ready?"
```

## Capabilities

--8<-- "snippets/toolset_capabilities_intro.md"

| Tool Name | Description |
|-----------|-------------|
| crossplane_list_providers | List all installed Crossplane providers with their health status |
| crossplane_get_provider | Get detailed status of a specific provider including conditions |
| crossplane_list_provider_revisions | List provider revisions to check for upgrade issues |
| crossplane_list_provider_configs | List ProviderConfigs to check credential configurations |
| crossplane_get_provider_config | Get detailed ProviderConfig including credential source |
| crossplane_list_xrds | List all CompositeResourceDefinitions (XRDs) |
| crossplane_get_xrd | Get details of a specific XRD including schema and versions |
| crossplane_list_compositions | List all Compositions |
| crossplane_get_composition | Get details of a Composition including resource templates |
| crossplane_get_claim | Get a claim's status, conditions, and composite resource reference |
| crossplane_get_composite_resource | Get a composite resource with its composed resource references |
| crossplane_list_managed_resources | List managed resources of a specific kind with sync status |
| crossplane_get_managed_resource | Get full details of a managed resource including conditions and events |
| crossplane_get_resource_events | Get Kubernetes events for a specific Crossplane resource |
| crossplane_list_managed_by_composite | List all managed resources owned by a specific composite resource |
