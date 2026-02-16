# Datadog Serverless Functions - Custom Agents

This directory contains custom AI agents that help with understanding, deploying, and maintaining the Datadog serverless functions in this repository.

## Available Agents

### Datadog Deployment Helper

**File:** `datadog-deployment-helper.yml`

An expert guide for working with Datadog serverless functions that provides:

- **Codebase Understanding**: Detailed explanations of the repository structure, code architecture, and how different components work together
- **Step-by-Step Deployment**: Comprehensive deployment guides for AWS Lambda and Azure Functions
- **Datadog Standards**: Ensures all deployments follow Datadog's best practices for security, observability, and performance
- **Troubleshooting**: Helps diagnose and resolve common deployment and operational issues

#### What the Agent Can Help With

1. **Understanding the Codebase**
   - Explain how the AWS Forwarder works
   - Detail the Azure Functions implementation
   - Navigate the code structure
   - Understand the trace forwarder
   
2. **AWS Lambda Deployment**
   - CloudFormation deployments (single and multi-region)
   - Terraform deployments
   - Manual deployments
   - StackSets for multi-account scenarios
   
3. **Azure Functions Deployment**
   - EventHub log monitoring setup
   - Blob storage log monitoring
   - Azure CLI deployment
   - Portal-based deployment
   
4. **Configuration and Best Practices**
   - Proper API key management (Secrets Manager, SSM)
   - Tagging strategies
   - Performance optimization
   - Security hardening
   - Monitoring setup
   
5. **Troubleshooting**
   - Deployment failures
   - Missing logs in Datadog
   - Throttling and performance issues
   - Authentication problems
   - Cost optimization

#### How to Use

If you're using GitHub Copilot with agent support, you can interact with this agent by referencing it in your conversations:

```
@datadog-deployment-helper Help me deploy the Datadog Forwarder to AWS in multiple regions
```

```
@datadog-deployment-helper Explain how the logs monitoring code processes CloudWatch logs
```

```
@datadog-deployment-helper I'm getting throttling errors on my forwarder, help me fix it
```

#### Example Interactions

**Example 1: Initial Deployment**
```
User: Help me deploy the Datadog Forwarder to AWS
Agent: I'll guide you through deploying the Datadog Forwarder using CloudFormation...
        [Provides detailed step-by-step instructions]
```

**Example 2: Understanding Code**
```
User: How does the scrubber module work?
Agent: The datadog_scrubber.py module handles security and compliance...
        [Explains the scrubbing patterns, configuration, and usage]
```

**Example 3: Troubleshooting**
```
User: My logs aren't showing up in Datadog
Agent: Let me help you diagnose this. Please run these checks...
        [Provides diagnostic steps and solutions]
```

## Agent Capabilities

The Datadog Deployment Helper agent has access to:

- **File operations**: Can read and analyze code in the repository
- **Code search**: Can search for specific patterns and implementations
- **Command execution**: Can help you run tests, linters, and validation commands
- **Documentation**: Can fetch and reference Datadog's official documentation
- **GitHub API**: Can review workflows, issues, and pull requests

## Standards and Best Practices

The agent enforces these Datadog standards:

### Security
- Never hardcode API keys
- Use Secrets Manager or SSM Parameter Store
- Apply least privilege IAM policies
- Enable encryption at rest

### Tagging
- Consistent tag structure: `env:value,service:value,version:value,team:value`
- Required tags on all resources
- Proper metadata in logs

### Performance
- Recommended memory: 1024 MB for Forwarder
- Reserved concurrency: minimum 10
- ARM64 architecture (AWS Lambda v4.1.0+)
- Proper timeout configuration

### Monitoring
- CloudWatch Logs with 90-day retention
- Datadog monitors for errors and throttling
- Performance metrics tracking
- Cost monitoring

## Contributing

To improve or extend the agents:

1. Edit the agent YAML file in this directory
2. Test the agent with various queries
3. Update the examples in the agent configuration
4. Update this README if adding new capabilities

## Documentation References

The agent references these official Datadog resources:

- [Datadog Forwarder](https://docs.datadoghq.com/serverless/forwarder/)
- [Send AWS Services Logs](https://docs.datadoghq.com/logs/guide/send-aws-services-logs-with-the-datadog-lambda-function/)
- [Azure Logging Guide](https://docs.datadoghq.com/logs/guide/azure-logging-guide/)
- [Serverless Monitoring](https://docs.datadoghq.com/serverless/)

## Feedback

If you have suggestions for improving these agents or encounter any issues, please open an issue in this repository.

## Version History

- **v1.0.0** (2026-02-16): Initial release of Datadog Deployment Helper agent
  - AWS Lambda deployment guidance
  - Azure Functions deployment guidance
  - Codebase navigation and explanation
  - Troubleshooting support
  - Datadog standards enforcement
