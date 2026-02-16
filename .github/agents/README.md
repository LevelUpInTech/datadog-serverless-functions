# Datadog Deployment Advisor Agent

An AI-powered agent that provides deployment guidance and enforces Datadog standards for serverless functions.

## Overview

The Datadog Deployment Advisor is a GitHub Copilot agent that helps developers and DevOps engineers deploy and configure Datadog serverless functions correctly. It provides expert guidance on:

- AWS Lambda Forwarder deployment
- Azure Functions configuration
- CloudFormation and Terraform best practices
- Security and compliance standards
- Performance optimization
- Troubleshooting common issues

## Features

### 🚀 Deployment Guidance

The agent provides step-by-step guidance for deploying Datadog Forwarder across different platforms and tools:

- **CloudFormation**: Basic and production-ready templates
- **Terraform**: Module recommendations and best practices
- **Azure**: Event Hub and Blob Storage configurations
- **Multi-region**: StackSets and organizational deployments

### 🔒 Security Standards Enforcement

Automatically enforces security best practices:

- API key management (Secrets Manager/SSM)
- IAM least privilege principles
- Encryption requirements
- VPC configuration guidance

### 📊 Monitoring & Observability

Ensures proper monitoring configuration:

- Datadog site selection
- Tag enrichment settings
- Custom tagging standards
- Log retention policies

### 🏗️ Architecture Best Practices

Validates architectural decisions:

- ARM64 architecture requirements
- Layer-based installation
- Memory and timeout optimization
- Reserved concurrency configuration

### 🔧 Troubleshooting Support

Helps diagnose and resolve common issues:

- Forwarder connectivity problems
- Timeout errors
- API key configuration issues
- Missing tags and metadata

## Using the Agent

### For GitHub Copilot Users

This agent is automatically available to GitHub Copilot users working in this repository. Simply ask questions like:

```
How do I deploy the Datadog Forwarder to production?
```

```
Review my CloudFormation template for Datadog standards compliance
```

```
Help me troubleshoot why the forwarder isn't sending logs
```

```
What are the recommended settings for a high-volume production deployment?
```

### For Pull Request Reviews

The agent can review your deployment configurations and provide feedback on:

- Security vulnerabilities
- Missing required parameters
- Best practice violations
- Optimization opportunities

### Example Interactions

#### Deploy to AWS

**Question:** "How do I deploy the Datadog Forwarder to AWS using CloudFormation?"

The agent will provide:
1. Step-by-step deployment instructions
2. Required parameters and their values
3. Security best practices
4. Post-deployment verification steps

#### Review Configuration

**Question:** "Review this CloudFormation parameter configuration:"
```yaml
DdApiKey: my-api-key-123
DdSite: datadoghq.com
MemorySize: 512
Timeout: 60
```

The agent will identify:
- ❌ API key should be stored in Secrets Manager
- ✅ Datadog site is valid
- ⚠️ Memory size below recommended default
- ⚠️ Timeout below recommended default

#### Troubleshoot Issues

**Question:** "My forwarder is timing out when processing logs. What should I do?"

The agent will suggest:
1. Increase timeout to 120+ seconds
2. Increase memory allocation (improves CPU)
3. Check log volume and batch size
4. Review CloudWatch logs for errors

## Standards Enforced

### Required Standards (Error Level)

These must be followed for all deployments:

- ✅ API keys stored in Secrets Manager or SSM
- ✅ Proper Datadog site configuration
- ✅ Resource tagging with env, service, version
- ✅ ARM64 architecture for v4.1.0+
- ✅ IAM least privilege
- ✅ Encryption for secrets

### Recommended Standards (Warning Level)

Best practices that should be followed:

- ⚠️ Multi-region deployment using StackSets
- ⚠️ Reserved concurrency for production (minimum 10)
- ⚠️ Default function naming convention
- ⚠️ Memory: 1024 MB, Timeout: 120s
- ⚠️ 90-day log retention
- ⚠️ Layer-based installation
- ⚠️ Tag enrichment enabled

## Deployment Checklists

### AWS CloudFormation Deployment

- [ ] Verify Datadog API key is stored in Secrets Manager or SSM
- [ ] Select correct DdSite for your Datadog account
- [ ] Configure appropriate memory (1024 MB) and timeout (120s)
- [ ] Set up reserved concurrency for production (minimum 10)
- [ ] Enable tag enrichment (S3, CloudWatch, Lambda)
- [ ] Configure DdTags with env, service, version
- [ ] Deploy to all required regions using StackSets
- [ ] Set up log source triggers after deployment
- [ ] Verify forwarder is sending data to Datadog
- [ ] Configure 90-day log retention

### Terraform Deployment

- [ ] Use official Datadog Terraform modules
- [ ] Store state file securely (S3 with encryption)
- [ ] Use Terraform workspaces for environments
- [ ] Implement proper secret management
- [ ] Configure backend for team collaboration
- [ ] Use consistent naming conventions
- [ ] Tag all resources appropriately

### Azure Functions Deployment

- [ ] Configure Event Hub for activity logs
- [ ] Set up Blob storage triggers
- [ ] Use Key Vault for API key storage
- [ ] Configure Application Insights integration
- [ ] Enable managed identities
- [ ] Set up proper RBAC permissions
- [ ] Test function deployment and triggers

## Common Troubleshooting Scenarios

### Forwarder Not Sending Logs

**Symptoms:** No logs appearing in Datadog

**Solutions:**
1. Verify API key is correct and has write permissions
2. Confirm DdSite matches your Datadog account region
3. Check Lambda has network access to Datadog endpoints
4. Review CloudWatch logs for forwarder errors
5. Ensure log source triggers are configured

### Lambda Timeout Errors

**Symptoms:** Function timing out during execution

**Solutions:**
1. Increase timeout to 120-900 seconds
2. Increase memory (improves CPU performance)
3. Check if log volume exceeds capacity
4. Review batch size configuration
5. Consider reserved concurrency

### API Key Not Found

**Symptoms:** Error retrieving API key from Secrets Manager/SSM

**Solutions:**
1. Verify Secrets Manager ARN is correct
2. Check Lambda has permission to read secret
3. Ensure secret contains plaintext (not key-value)
4. Verify SSM parameter path format

### Missing Tags on Logs

**Symptoms:** Logs missing expected tags in Datadog

**Solutions:**
1. Enable DdFetchLambdaTags
2. Enable DdEnrichS3Tags
3. Enable DdEnrichCloudwatchTags
4. Verify AWS Resource Collection is enabled
5. Add custom tags using DdTags parameter

## Configuration Examples

### Basic Deployment

```bash
aws cloudformation create-stack \
  --stack-name datadog-forwarder \
  --template-url https://datadog-cloudformation-template.s3.amazonaws.com/aws/forwarder/latest.yaml \
  --parameters \
    ParameterKey=DdApiKeySecretArn,ParameterValue=arn:aws:secretsmanager:us-east-1:123456789:secret:DdApiKey \
    ParameterKey=DdSite,ParameterValue=datadoghq.com \
    ParameterKey=DdTags,ParameterValue="env:prod,service:logging,version:1.0.0" \
  --capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM
```

### Production Deployment

```bash
aws cloudformation create-stack \
  --stack-name datadog-forwarder-prod \
  --template-url https://datadog-cloudformation-template.s3.amazonaws.com/aws/forwarder/latest.yaml \
  --parameters \
    ParameterKey=DdApiKeySecretArn,ParameterValue=arn:aws:secretsmanager:us-east-1:123456789:secret:DdApiKey \
    ParameterKey=DdSite,ParameterValue=datadoghq.com \
    ParameterKey=FunctionName,ParameterValue=DatadogForwarder \
    ParameterKey=MemorySize,ParameterValue=1024 \
    ParameterKey=Timeout,ParameterValue=120 \
    ParameterKey=ReservedConcurrency,ParameterValue=10 \
    ParameterKey=LogRetentionInDays,ParameterValue=90 \
    ParameterKey=DdTags,ParameterValue="env:prod,service:logging,version:1.0.0,team:platform" \
    ParameterKey=DdEnrichS3Tags,ParameterValue=true \
    ParameterKey=DdEnrichCloudwatchTags,ParameterValue=true \
    ParameterKey=DdFetchLambdaTags,ParameterValue=true \
  --capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM
```

## Learning Resources

- [Datadog Forwarder Documentation](https://docs.datadoghq.com/serverless/forwarder/)
- [AWS Lambda Best Practices](https://docs.aws.amazon.com/lambda/latest/dg/best-practices.html)
- [CloudFormation Best Practices](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/best-practices.html)
- [Datadog Tagging Best Practices](https://docs.datadoghq.com/getting_started/tagging/)

## Version History

### v1.0.0 (Current)

- Initial release
- AWS CloudFormation deployment guidance
- Azure Functions deployment guidance
- Security standards enforcement
- Troubleshooting support
- Configuration examples

## Contributing

To improve or extend the agent's capabilities, modify the `deployment-advisor.yml` configuration file. When adding new standards or guidance:

1. Follow the existing YAML structure
2. Include clear enforcement levels (error/warning)
3. Provide actionable guidance
4. Add examples where helpful
5. Update this README with new features

## Support

For issues or questions:

1. Check the troubleshooting section above
2. Review the [Datadog documentation](https://docs.datadoghq.com/)
3. Open an issue in this repository
4. Contact Datadog support for account-specific issues
