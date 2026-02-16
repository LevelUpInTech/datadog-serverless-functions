# Using the Datadog Deployment Helper Agent

This repository includes a custom AI agent that helps you understand, deploy, and maintain Datadog serverless functions following Datadog's standards and best practices.

## Quick Start

The **Datadog Deployment Helper** agent is located in `.github/agents/datadog-deployment-helper.yml` and can be accessed through GitHub Copilot (when agent support is available).

### What Can The Agent Help With?

1. **📚 Understanding the Codebase**
   - Explain how the AWS Forwarder processes logs
   - Detail the Azure Functions implementation
   - Navigate and understand the code structure
   - Understand the trace forwarder components

2. **🚀 AWS Deployments**
   - CloudFormation deployments (recommended)
   - Terraform deployments
   - Multi-region deployments using StackSets
   - Multi-account deployments for AWS Organizations

3. **☁️ Azure Deployments**
   - EventHub log monitoring setup
   - Blob storage log monitoring
   - Azure CLI deployments
   - Azure Portal deployments

4. **⚙️ Configuration & Best Practices**
   - Secure API key management
   - Tagging strategies
   - Performance optimization
   - Security hardening
   - Monitoring and alerting setup

5. **🔧 Troubleshooting**
   - Deployment failures
   - Missing logs in Datadog
   - Throttling and performance issues
   - Authentication problems
   - Cost optimization

## Example Interactions

### Deploy to AWS
```
@datadog-deployment-helper Help me deploy the Datadog Forwarder to AWS using CloudFormation
```

The agent will provide:
- Pre-deployment checklist
- Step-by-step CloudFormation deployment instructions
- Configuration best practices
- Testing and verification steps
- How to set up log source triggers

### Understand the Code
```
@datadog-deployment-helper Explain how the AWS logs monitoring lambda_function.py works
```

The agent will explain:
- The entry point and event routing
- Core modules and their purposes
- Processing flow from event to Datadog
- Key features like batching and scrubbing

### Troubleshoot Issues
```
@datadog-deployment-helper I'm getting throttling errors on my forwarder
```

The agent will help:
- Diagnose the root cause
- Provide immediate mitigation steps
- Suggest long-term optimizations
- Show commands to monitor and verify the fix

### Multi-Region Deployment
```
@datadog-deployment-helper Help me deploy the Forwarder to multiple AWS regions using StackSets
```

### Azure Deployment
```
@datadog-deployment-helper Guide me through deploying the Azure EventHub log forwarder
```

## Key Features

### 🎯 Comprehensive Deployment Guides
- Detailed step-by-step instructions
- Exact commands with placeholders
- Validation checks after each step
- Multi-region and multi-account scenarios

### 🛡️ Datadog Standards Enforcement
The agent ensures all deployments follow:
- Secure API key storage (Secrets Manager/SSM)
- Proper tagging: `env:prod,service:log-forwarder,team:platform`
- ARM64 architecture for AWS Lambda v4.1.0+
- Recommended resource configurations (memory, timeout, concurrency)
- CloudWatch logging with appropriate retention

### 📖 Code Navigation
- Detailed explanations of Python, Node.js, and Go codebases
- Module-level breakdowns
- Processing flow diagrams
- Testing strategy explanations

### 🔍 Troubleshooting Expertise
- Common issue diagnosis
- AWS CLI commands for investigation
- Metric checking and monitoring
- Performance optimization techniques

## Agent Capabilities

The agent has access to:
- **Repository files**: Can read and analyze all code
- **Code search**: Can find specific patterns and implementations
- **Command execution**: Can help run validation commands
- **Documentation**: Can reference official Datadog docs
- **GitHub API**: Can review workflows and configurations

## Documentation

For more details, see:
- `.github/agents/README.md` - Full agent documentation
- `.github/agents/datadog-deployment-helper.yml` - Agent configuration

## Official Datadog Documentation

The agent references these official resources:
- [Datadog Forwarder](https://docs.datadoghq.com/serverless/forwarder/)
- [Send AWS Services Logs](https://docs.datadoghq.com/logs/guide/send-aws-services-logs-with-the-datadog-lambda-function/)
- [Azure Logging Guide](https://docs.datadoghq.com/logs/guide/azure-logging-guide/)
- [Serverless Monitoring](https://docs.datadoghq.com/serverless/)

## Standards Enforced

### Security ✅
- Never hardcode API keys
- Use AWS Secrets Manager or SSM Parameter Store
- Apply least privilege IAM policies
- Enable encryption at rest

### Tagging ✅
- Consistent structure: `env:value,service:value,version:value,team:value`
- Required tags on all resources
- Proper metadata in forwarded logs

### Performance ✅
- Memory: 1024 MB recommended for Forwarder
- Reserved concurrency: minimum 10
- ARM64 architecture for AWS Lambda v4.1.0+
- Appropriate timeout configuration (120s default)

### Monitoring ✅
- CloudWatch Logs with 90-day retention
- Datadog monitors for errors and throttling
- Performance metrics tracking
- Cost monitoring alerts

## Getting Help

If you need help with the agent or have questions:
1. Try asking the agent directly with `@datadog-deployment-helper`
2. Review the example interactions above
3. Check `.github/agents/README.md` for detailed documentation
4. Open an issue in this repository for agent improvements

## Version

Current version: v1.0.0 (2026-02-16)

Includes:
- AWS Lambda deployment guidance
- Azure Functions deployment guidance
- Codebase navigation and explanation
- Troubleshooting support
- Datadog standards enforcement
