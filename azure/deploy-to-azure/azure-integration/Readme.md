# Azure Integration Deployment

This deployment template configures the Datadog-Azure integration to collect metrics, logs, and resource metadata from your Azure environment.

## Quick Deploy

### Subscription Level

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/CustomDeploymentBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FDataDog%2Fdatadog-serverless-functions%2Fmaster%2Fazure%2Fdeploy-to-azure%2Fazure-integration%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FDataDog%2Fdatadog-serverless-functions%2Fmaster%2Fazure%2Fdeploy-to-azure%2Fazure-integration%2FCreateUIDefinition.json)

### Management Group Level

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/CustomDeploymentBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FDataDog%2Fdatadog-serverless-functions%2Fmaster%2Fazure%2Fdeploy-to-azure%2Fazure-integration%2Fazuredeploy-managementgroups.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FDataDog%2Fdatadog-serverless-functions%2Fmaster%2Fazure%2Fdeploy-to-azure%2Fazure-integration%2FCreateUIDefinition-managementgroups.json)

## Deploying the Datadog Agent

The Azure integration template above configures metric and log collection at the platform level. To collect system-level metrics, logs, and APM traces from your Azure VMs and containers, you also need to deploy the Datadog Agent.

### Prerequisites

Before deploying the Datadog Agent:

1. Complete the Azure integration deployment above
2. Obtain your Datadog API key from [https://app.datadoghq.com/organization-settings/api-keys](https://app.datadoghq.com/organization-settings/api-keys)
3. Determine your Datadog site (e.g., `datadoghq.com`, `datadoghq.eu`, `us3.datadoghq.com`, `us5.datadoghq.com`)

### Option 1: Deploy Agent to Azure VMs using VM Extensions

#### Using Azure Portal

1. Navigate to your VM in the Azure Portal
2. Select **Extensions + applications** from the left menu
3. Click **+ Add**
4. Search for "Datadog Agent" in the marketplace
5. Select the Datadog Agent extension and click **Create**
6. Configure the extension with:
   - **API Key**: Your Datadog API key
   - **Datadog Site**: Your Datadog site (e.g., `datadoghq.com`)
7. Click **Review + create**, then **Create**

#### Using Azure CLI

For **Linux VMs**:

```bash
az vm extension set \
  --resource-group <resource-group-name> \
  --vm-name <vm-name> \
  --name DatadogLinuxAgent \
  --publisher Datadog.Agent \
  --settings '{"site":"datadoghq.com"}' \
  --protected-settings '{"api_key":"<your-datadog-api-key>"}'
```

For **Windows VMs**:

```bash
az vm extension set \
  --resource-group <resource-group-name> \
  --vm-name <vm-name> \
  --name DatadogWindowsAgent \
  --publisher Datadog.Agent \
  --settings '{"site":"datadoghq.com"}' \
  --protected-settings '{"api_key":"<your-datadog-api-key>"}'
```

#### Using PowerShell

For **Linux VMs**:

```powershell
Set-AzVMExtension `
  -ResourceGroupName "<resource-group-name>" `
  -VMName "<vm-name>" `
  -Name "DatadogLinuxAgent" `
  -Publisher "Datadog.Agent" `
  -ExtensionType "DatadogLinuxAgent" `
  -TypeHandlerVersion "1.0" `
  -Settings @{"site"="datadoghq.com"} `
  -ProtectedSettings @{"api_key"="<your-datadog-api-key>"}
```

For **Windows VMs**:

```powershell
Set-AzVMExtension `
  -ResourceGroupName "<resource-group-name>" `
  -VMName "<vm-name>" `
  -Name "DatadogWindowsAgent" `
  -Publisher "Datadog.Agent" `
  -ExtensionType "DatadogWindowsAgent" `
  -TypeHandlerVersion "1.0" `
  -Settings @{"site"="datadoghq.com"} `
  -ProtectedSettings @{"api_key"="<your-datadog-api-key>"}
```

### Option 2: Deploy Agent to Azure Virtual Machine Scale Sets (VMSS)

#### Using Azure CLI

```bash
az vmss extension set \
  --resource-group <resource-group-name> \
  --vmss-name <vmss-name> \
  --name DatadogLinuxAgent \
  --publisher Datadog.Agent \
  --settings '{"site":"datadoghq.com"}' \
  --protected-settings '{"api_key":"<your-datadog-api-key>"}'
```

After adding the extension, update existing instances:

```bash
az vmss update-instances \
  --resource-group <resource-group-name> \
  --name <vmss-name> \
  --instance-ids '*'
```

### Option 3: Deploy Agent to Azure Container Instances (ACI)

Add the Datadog Agent as a sidecar container in your container group:

```json
{
  "name": "datadog-agent",
  "properties": {
    "image": "gcr.io/datadoghq/agent:7",
    "environmentVariables": [
      {
        "name": "DD_API_KEY",
        "secureValue": "<your-datadog-api-key>"
      },
      {
        "name": "DD_SITE",
        "value": "datadoghq.com"
      },
      {
        "name": "DD_LOGS_ENABLED",
        "value": "true"
      },
      {
        "name": "DD_LOGS_CONFIG_CONTAINER_COLLECT_ALL",
        "value": "true"
      }
    ],
    "resources": {
      "requests": {
        "cpu": 0.5,
        "memoryInGb": 0.5
      }
    }
  }
}
```

**Note**: The example uses Agent version 7. For production deployments, pin to a specific patch version (e.g., `7.50.0`) instead of using `latest` or just the major version to ensure stability and avoid unexpected breaking changes.

### Option 4: Deploy Agent to Azure Kubernetes Service (AKS)

#### Using Helm (Recommended)

1. Add the Datadog Helm repository:

```bash
helm repo add datadog https://helm.datadoghq.com
helm repo update
```

2. Create a Kubernetes secret for the API key:

```bash
kubectl create secret generic datadog-secret \
  --from-literal api-key=<your-datadog-api-key> \
  --namespace datadog
```

3. Create a `values.yaml` file:

```yaml
datadog:
  apiKeyExistingSecret: datadog-secret
  site: datadoghq.com
  logs:
    enabled: true
    containerCollectAll: true
  apm:
    portEnabled: true
  processAgent:
    enabled: true
```

4. Install the Datadog Agent:

```bash
helm install datadog-agent datadog/datadog \
  -f values.yaml \
  --namespace datadog \
  --create-namespace
```

#### Using the Datadog Operator

1. Install the Datadog Operator:

```bash
helm install datadog-operator datadog/datadog-operator \
  --namespace datadog \
  --create-namespace
```

2. Create a Kubernetes secret for the API key:

```bash
kubectl create secret generic datadog-secret \
  --from-literal api-key=<your-datadog-api-key> \
  --namespace datadog
```

3. Create a DatadogAgent resource:

```yaml
apiVersion: datadoghq.com/v2alpha1
kind: DatadogAgent
metadata:
  name: datadog
  namespace: datadog
spec:
  global:
    credentials:
      apiSecret:
        secretName: datadog-secret
        keyName: api-key
    site: datadoghq.com
  features:
    apm:
      enabled: true
    logCollection:
      enabled: true
```

### Option 5: Deploy Agent to Azure App Service

For Azure App Service on Linux, add the following application settings:

1. Navigate to your App Service in Azure Portal
2. Go to **Configuration** > **Application settings**
3. Add the following settings:
   - `DD_API_KEY`: `<your-datadog-api-key>`
   - `DD_SITE`: `datadoghq.com`
   - `DD_SERVICE`: `<your-service-name>`
   - `DD_ENV`: `<your-environment>`

4. Install the Datadog Agent using a startup script or include it in your container image

For more details, see the [Azure App Service documentation](https://docs.datadoghq.com/serverless/azure_app_services/).

### Verifying Agent Installation

After deploying the Agent, verify it's running and sending data:

1. Check the Infrastructure List in Datadog: [https://app.datadoghq.com/infrastructure](https://app.datadoghq.com/infrastructure)
2. Look for your Azure VMs/containers appearing with the `azure` tag
3. Run the Agent status command on the VM:
   - Linux: `sudo datadog-agent status`
   - Windows: `& "$env:ProgramFiles\Datadog\Datadog Agent\bin\agent.exe" status`

### Configuring the Agent

#### Common Configuration Options

Create or modify the Agent configuration file:
- Linux: `/etc/datadog-agent/datadog.yaml`
- Windows: `C:\ProgramData\Datadog\datadog.yaml`

Example configuration:

```yaml
api_key: <your-datadog-api-key>
site: datadoghq.com

# Tagging
tags:
  - env:production
  - team:platform
  - azure:true

# Log collection
logs_enabled: true

# APM
apm_config:
  enabled: true

# Process monitoring
process_config:
  enabled: true
```

**Security Note**: For production environments, avoid storing the API key directly in the configuration file. Instead, use environment variables or Azure Key Vault integration. For example, set `DD_API_KEY` as an environment variable and remove the `api_key` line from the configuration file. See the [Datadog Agent documentation](https://docs.datadoghq.com/agent/guide/secrets-management/) for secrets management best practices.

#### Azure-Specific Tags

The Agent automatically collects Azure metadata tags when running on Azure VMs:
- `cloud_provider:azure`
- `region:<azure-region>`
- `instance-type:<vm-size>`
- `resource_group:<resource-group-name>`

### Troubleshooting

#### Agent Not Reporting

1. Verify the API key is correct
2. Check network connectivity to Datadog:
   ```bash
   curl https://api.datadoghq.com/api/v1/validate
   ```
3. Review Agent logs:
   - Linux: `/var/log/datadog/agent.log`
   - Windows: `C:\ProgramData\Datadog\logs\agent.log`

#### VM Extension Installation Fails

1. Check Azure Activity Log for error details
2. Verify the extension version is compatible with your OS
3. Ensure the VM has internet access
4. Check if there are existing conflicting extensions

#### Integration Between Agent and Azure Integration

The Datadog Agent and Azure integration work together:
- **Azure Integration** (this template): Collects Azure platform metrics (CPU, memory, network at the hypervisor level) and Azure service metrics
- **Datadog Agent**: Collects system-level metrics from inside the VM, application metrics, logs, and traces

Both should be deployed for complete visibility into your Azure environment.

## Additional Resources

- [Datadog Azure Integration Documentation](https://docs.datadoghq.com/integrations/azure/)
- [Datadog Agent Documentation](https://docs.datadoghq.com/agent/)
- [Azure VM Extensions Documentation](https://docs.microsoft.com/azure/virtual-machines/extensions/overview)
- [Monitoring Azure with Datadog](https://docs.datadoghq.com/integrations/azure/)
