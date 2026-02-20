# AWS Playground Project Bootstrap

## Overview

This SOP bootstraps a standardized AWS playground development project for experimenting with AWS concepts. The project includes CDK infrastructure with Python, backend Lambda functions, optional Vue.js frontend, comprehensive Makefile for all operations, and project-specific steering files.

## Parameters

- **project_name** (required): Name of the project folder to create
- **environments** (required): List of environment configurations with name and AWS profile mapping (default: [{"name": "dev", "profile": "default"}])
- **environment_variables** (optional): Additional environment-specific variables (API endpoints, region, account ID, etc.)
- **include_api_gateway** (optional, default: false): Whether to initialize CDK with API Gateway stack
- **include_dynamodb** (optional, default: false): Whether to initialize CDK with DynamoDB stack
- **include_cognito** (optional, default: false): Whether to initialize CDK with Cognito User Pool for authentication
- **include_frontend** (optional, default: false): Whether to create Vue.js frontend
- **frontend_config** (optional): Configuration for frontend including title and purpose description for home page
- **git_remote_url** (optional): Remote repository URL to configure

## Steps

### 1. Create Project Directory Structure

Create the root directory and all required subdirectories.

**Constraints:**
- You MUST create `{project_name}/` as the root directory
- You MUST create `cdk/`, `scripts/`, `backend/`, `.kiro/steering/` subdirectories
- You MUST create `docs/` subdirectory for project documentation
- You MUST create `frontend/` subdirectory if include_frontend is true
- You MUST create `backend/tests/` directory for pytest tests
- You MUST create `frontend/tests/` directory if include_frontend is true

### 2. Create Makefile

Create a comprehensive Makefile at the project root with environment management and all project commands.

**Constraints:**
- You MUST include ENV variable management with default value "dev"
- You MUST dynamically map ENV to AWS_PROFILE based on environments parameter
- You MUST prefix all AWS CLI commands with the appropriate AWS_PROFILE for current ENV
- You MUST use relative path `.venv/bin/python` for CDK commands in cdk directory
- You MUST use shell variable `$$PWD/.venv/bin/python` for CDK_PYTHON environment variable
- You MUST set `JSII_SILENCE_WARNING_UNTESTED_NODE_VERSION=1` for all CDK commands to handle Node.js version warnings
- You MUST activate `.venv` for scripts commands (using `scripts/.venv`)
- You MUST activate `.venv` for backend commands (using `backend/.venv`)
- You MUST include these targets: help, setup, cdk-init, deploy, destroy, test, test-backend, test-frontend (if applicable), run-frontend (if applicable)
- You MUST include create-user target if include_cognito is true to manually create users via AWS CLI
- You MUST include delete-user target if include_cognito is true to manually delete users via AWS CLI
- You MUST update frontend/.env.development with deployed resource IDs after successful deployment if include_frontend is true
- You MUST retrieve Cognito User Pool ID and Client ID from CloudFormation outputs and write to frontend/.env.development if include_cognito and include_frontend are true
- You MUST install AWS CDK locally in the setup target to enable infrastructure deployment
- You MUST document all commands in the help target
- You MUST support ENV parameter override (e.g., `make deploy ENV=staging`)
- You MUST configure create-user target to accept EMAIL and PASSWORD parameters if include_cognito is true
- You MUST use appropriate AWS_PROFILE for create-user command based on ENV parameter if include_cognito is true
- You MUST auto-discover Cognito User Pool ID by name pattern in create-user target if include_cognito is true

### 3. Initialize CDK Structure

Set up the CDK directory with Python virtual environment and infrastructure code.

**Constraints:**
- You MUST create Python virtual environment at `cdk/.venv`
- You MUST create `cdk/requirements.txt` with CDK dependencies (aws-cdk-lib>=2.100.0, constructs>=10.0.0)
- You MUST create `cdk/app.py` as the CDK entry point
- You MUST create `cdk/cdk.json` configuration file with app command `.venv/bin/python app.py`
- You MUST NOT include CDKv1 feature flags in cdk.json (e.g., @aws-cdk/core:enableStackNameDuplicates)
- You MUST use only CDKv2-compatible feature flags in cdk.json
- You MUST create `cdk/stacks/` directory for stack modules
- You MUST create at least one main stack even if no resources are requested
- You MUST use stack naming pattern: `{project_name}-{env}-{stack_type}`
- You MUST separate stacks by service type (compute, storage, networking) to avoid CloudFormation quotas
- You MUST create API Gateway stack in `cdk/stacks/api_gateway_stack.py` if include_api_gateway is true
- You MUST create DynamoDB stack in `cdk/stacks/dynamodb_stack.py` if include_dynamodb is true
- You MUST create Cognito stack in `cdk/stacks/cognito_stack.py` if include_cognito is true
- You MUST set user_pool_name to `{project_name}-{env}-user-pool` in Cognito stack if include_cognito is true
- You MUST configure API Gateway with Cognito authorizer if both include_api_gateway and include_cognito are true

### 4. Initialize Scripts Structure

Set up the scripts directory with Python virtual environment.

**Constraints:**
- You MUST create Python virtual environment at `scripts/.venv`
- You MUST create single `scripts/requirements.txt` for all scripts
- You MUST create at least one example utility script demonstrating the structure

### 5. Initialize Backend Structure

Create the backend directory structure for Lambda functions with testing support.

**Constraints:**
- You MUST create Python virtual environment at `backend/.venv`
- You MUST create `backend/lambda_functions/` directory
- You MUST create at least one example Lambda function with handler
- You MUST create `backend/requirements.txt` for Lambda dependencies
- You MUST create `backend/tests/` directory ready for pytest
- You MUST organize Lambda functions following AWS best practices

### 6. Initialize Frontend Structure

If include_frontend is true, create a Vue.js project with testing support.

**Constraints:**
- You MUST create Vue.js project structure with standard subdirectories
- You MUST create `frontend/tests/` directory ready for jest/vitest
- You MUST create subdirectories for dependencies, environment config, and tests
- You MUST customize home page with frontend_config title and purpose if provided
- You MUST use clean, simple default home page style
- You MUST create environment configuration files with empty values (will be populated after deployment)
- You MUST create login page with authentication form if include_cognito is true
- You MUST create sign-up/registration page with account creation form if include_cognito is true
- You MUST create email verification page (VerifyEmail.vue) if include_cognito is true
- You MUST integrate AWS Amplify library for Cognito authentication if include_cognito is true
- You MUST create a simple menu with links to login, sign-up, and protected pages if include_cognito is true
- You MUST create one protected page that requires authentication if include_cognito is true
- You MUST keep home page non-protected (accessible without login) if include_cognito is true
- You MUST configure Amplify with Cognito User Pool details in environment config if include_cognito is true
- You MUST implement complete email verification flow if include_cognito is true:
  - Sign-up redirects to verify page with email parameter
  - Verify page accepts email input and verification code
  - Verify page includes resend code functionality
  - Login page includes link to verify page for users who need to verify
  - Successful verification redirects to login page
- You MUST NOT create frontend structure if include_frontend is false

### 7. Create Git Configuration

Set up version control for the project.

**Constraints:**
- You MUST create `.gitignore` file excluding: `.venv/`, `node_modules/`, `cdk.out/`, `.env.*.local`, `__pycache__/`, `*.pyc`, IDE files
- You MUST initialize git repository
- You MUST configure git_remote_url if provided
- You SHOULD NOT create initial commit (user handles commits)

### 8. Create Documentation Structure

Create a documentation folder with initial documentation for the project.

**Constraints:**
- You MUST create `docs/` directory at the project root
- You MUST create `docs/README.md` with project overview, architecture description, and setup instructions
- You MUST create `docs/architecture.md` documenting the CDK stacks, resources, and their relationships
- You MUST create `docs/deployment.md` with deployment procedures and environment configuration details
- You MUST create `docs/development.md` with development workflow, testing procedures, and contribution guidelines
- You MUST document all created AWS resources (API Gateway, DynamoDB, Cognito, Lambda functions, etc.)
- You MUST document all Makefile commands and their usage
- You MUST include diagrams or descriptions of data flow and component interactions where applicable

### 9. Create Steering Files

Create project-specific steering markdown files in `.kiro/steering/` directory.

**Constraints:**
- You MUST create `unit-testing.md`: Unit tests MUST be done and executed at end of each implementation task
- You MUST create `api-mocking.md`: All APIs MUST be mocked for unit tests
- You MUST create `code-reuse.md`: Code SHOULD be reused as much as possible
- You MUST create `cdk-stack-separation.md`: CDK stacks MUST be separated into main/sub-stacks by service type following AWS guidelines
- You MUST create `makefile-commands.md`: All project commands MUST be in Makefile and documented in help target
- You MUST create `documentation-updates.md`: Documentation MUST be updated after each code change, feature addition, or architectural modification
- You MUST use RFC2119 keywords (MUST, SHOULD, MAY) in steering files

### 10. Create MCP Servers Configuration

Create MCP servers configuration for Kiro CLI integration.

**Constraints:**
- You MUST create `.kiro/settings/` directory
- You MUST create `.kiro/settings/mcp.json` with MCP servers configuration
- You MUST include `agent-sops` server for SOP management
- You MUST include `aws-knowledge-mcp-server` for AWS knowledge base access
- You MUST include `awslabs.aws-api-mcp-server` for AWS API operations
- You MUST set AWS_REGION environment variable for aws-api-mcp-server

### 11. Create Documentation

Create comprehensive documentation in the `docs/` directory.

**Constraints:**
- You MUST create `docs/README.md` with project overview, architecture description, and setup instructions
- You MUST create `docs/architecture.md` documenting CDK stacks, resources, and their relationships
- You MUST create `docs/deployment.md` with deployment procedures and environment configuration
- You MUST create `docs/development.md` with development workflow, testing procedures, and contribution guidelines
- You MUST document all created AWS resources (API Gateway, DynamoDB, Cognito, Lambda functions)
- You MUST document all Makefile commands and their usage
- You MUST include descriptions of data flow and component interactions

### 12. Verify Project Setup

Confirm that all components are properly initialized and ready to use.

**Constraints:**
- You MUST verify all directories and files have been created
- You MUST verify docs/ folder exists with all required documentation files
- You MUST verify .kiro/steering/ folder exists with all steering files including documentation-updates.md
- You MUST verify .kiro/settings/mcp.json exists with MCP servers configuration
- You MUST provide instructions for running `make help` to see available commands
- You MUST confirm project is ready for `make setup`, `make cdk-init`, and `make deploy`
- You MUST confirm tests can be run with `make test`

## Examples

### Example Input
```
project_name: "serverless-api-experiment"
environments: [
  {"name": "dev", "profile": "default"},
  {"name": "staging", "profile": "staging-profile"},
  {"name": "prod", "profile": "production"}
]
environment_variables: {
  "AWS_REGION": "us-east-1",
  "ACCOUNT_ID": "123456789012"
}
include_api_gateway: true
include_dynamodb: true
include_cognito: true
include_frontend: true
frontend_config: {
  "title": "Serverless API Dashboard",
  "purpose": "Testing serverless architecture patterns with API Gateway and DynamoDB"
}
git_remote_url: "https://github.com/user/serverless-api-experiment.git"
```

### Example Output Structure
```
serverless-api-experiment/
├── Makefile
├── .gitignore
├── docs/
│   ├── README.md
│   ├── architecture.md
│   ├── deployment.md
│   └── development.md
├── cdk/
│   ├── .venv/
│   ├── requirements.txt
│   ├── app.py
│   ├── cdk.json
│   └── stacks/
│       ├── main_stack.py
│       ├── api_gateway_stack.py
│       ├── dynamodb_stack.py
│       └── cognito_stack.py
├── scripts/
│   ├── .venv/
│   ├── requirements.txt
│   └── example_utility.py
├── backend/
│   ├── .venv/
│   ├── requirements.txt
│   ├── lambda_functions/
│   │   └── example_function/
│   │       └── handler.py
│   └── tests/
│       └── test_example.py
├── frontend/
│   ├── package.json
│   ├── src/
│   │   ├── views/
│   │   │   ├── Home.vue
│   │   │   ├── Login.vue
│   │   │   ├── SignUp.vue
│   │   │   ├── VerifyEmail.vue
│   │   │   └── Protected.vue
│   │   ├── router/
│   │   │   └── index.js
│   │   └── components/
│   │       └── Navigation.vue
│   ├── tests/
│   └── .env.development
└── .kiro/
    ├── settings/
    │   └── mcp.json
    └── steering/
        ├── unit-testing.md
        ├── api-mocking.md
        ├── code-reuse.md
        ├── cdk-stack-separation.md
        ├── makefile-commands.md
        └── documentation-updates.md
```

## Code Examples

### Example MCP Servers Configuration

```json
{
  "mcpServers": {
    "agent-sops": {
      "command": "strands-agents-sops",
      "args": ["mcp", "--sop-paths", "~/.kiro/mysop"]
    },
    "aws-knowledge-mcp-server": {
      "command": "uvx",
      "args": ["fastmcp", "run", "https://knowledge-mcp.global.api.aws"]
    },
    "awslabs.aws-api-mcp-server": {
      "command": "uvx",
      "args": [
        "awslabs.aws-api-mcp-server@latest"
      ],
      "env": {
        "AWS_REGION": "us-east-1"
      },
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

### Example Makefile CDK Commands

```makefile
cdk-init:
	@echo "Bootstrapping CDK for environment: $(ENV) with profile: $(AWS_PROFILE)"
	cd cdk && JSII_SILENCE_WARNING_UNTESTED_NODE_VERSION=1 CDK_PYTHON=$$PWD/.venv/bin/python npx --prefix ../frontend aws-cdk bootstrap --profile $(AWS_PROFILE)
	@echo "Synthesizing CDK stacks..."
	cd cdk && JSII_SILENCE_WARNING_UNTESTED_NODE_VERSION=1 CDK_PYTHON=$$PWD/.venv/bin/python npx --prefix ../frontend aws-cdk synth --profile $(AWS_PROFILE)

deploy:
	@echo "Deploying to environment: $(ENV) with profile: $(AWS_PROFILE)"
	cd cdk && JSII_SILENCE_WARNING_UNTESTED_NODE_VERSION=1 CDK_PYTHON=$$PWD/.venv/bin/python npx --prefix ../frontend aws-cdk deploy --all --require-approval never --profile $(AWS_PROFILE)
	@echo "Updating frontend environment configuration..."
	@USER_POOL_ID=$$(aws cloudformation describe-stacks --stack-name {project_name}-$(ENV)-cognito --profile $(AWS_PROFILE) --query 'Stacks[0].Outputs[?OutputKey==`UserPoolId`].OutputValue' --output text 2>/dev/null || echo ""); \
	USER_POOL_CLIENT_ID=$$(aws cloudformation describe-stacks --stack-name {project_name}-$(ENV)-cognito --profile $(AWS_PROFILE) --query 'Stacks[0].Outputs[?OutputKey==`UserPoolClientId`].OutputValue' --output text 2>/dev/null || echo ""); \
	AWS_REGION=$$(aws configure get region --profile $(AWS_PROFILE)); \
	if [ -n "$$USER_POOL_ID" ]; then \
		echo "VITE_USER_POOL_ID=$$USER_POOL_ID" > frontend/.env.development; \
		echo "VITE_USER_POOL_CLIENT_ID=$$USER_POOL_CLIENT_ID" >> frontend/.env.development; \
		echo "VITE_API_URL=" >> frontend/.env.development; \
		echo "VITE_AWS_REGION=$$AWS_REGION" >> frontend/.env.development; \
		echo "Frontend configuration updated successfully"; \
	else \
		echo "Warning: Could not retrieve Cognito outputs. Frontend configuration not updated."; \
	fi

destroy:
	@echo "Destroying environment: $(ENV) with profile: $(AWS_PROFILE)"
	cd cdk && JSII_SILENCE_WARNING_UNTESTED_NODE_VERSION=1 CDK_PYTHON=$$PWD/.venv/bin/python npx --prefix ../frontend aws-cdk destroy --all --profile $(AWS_PROFILE)
```

### Example Makefile create-user Command (if include_cognito is true)

```makefile
create-user:
	@if [ -z "$(EMAIL)" ] || [ -z "$(PASSWORD)" ]; then \
		echo "Error: EMAIL and PASSWORD are required"; \
		echo "Usage: make create-user EMAIL=user@example.com PASSWORD=YourPassword123!"; \
		exit 1; \
	fi
	@echo "Creating user in Cognito for environment: $(ENV) with profile: $(AWS_PROFILE)"
	@USER_POOL_ID=$$(aws cognito-idp list-user-pools --max-results 10 --profile $(AWS_PROFILE) --query "UserPools[?Name=='{project_name}-$(ENV)-user-pool'].Id" --output text); \
	if [ -z "$$USER_POOL_ID" ]; then \
		echo "Error: User pool not found for environment $(ENV)"; \
		exit 1; \
	fi; \
	aws cognito-idp admin-create-user \
		--user-pool-id $$USER_POOL_ID \
		--username $(EMAIL) \
		--user-attributes Name=email,Value=$(EMAIL) Name=email_verified,Value=true \
		--temporary-password $(PASSWORD) \
		--message-action SUPPRESS \
		--profile $(AWS_PROFILE); \
	aws cognito-idp admin-set-user-password \
		--user-pool-id $$USER_POOL_ID \
		--username $(EMAIL) \
		--password $(PASSWORD) \
		--permanent \
		--profile $(AWS_PROFILE)
	@echo "User created successfully with email: $(EMAIL)"

delete-user:
	@if [ -z "$(EMAIL)" ]; then \
		echo "Error: EMAIL is required"; \
		echo "Usage: make delete-user EMAIL=user@example.com"; \
		exit 1; \
	fi
	@echo "Deleting user from Cognito for environment: $(ENV) with profile: $(AWS_PROFILE)"
	@USER_POOL_ID=$$(aws cognito-idp list-user-pools --max-results 10 --profile $(AWS_PROFILE) --query "UserPools[?Name=='{project_name}-$(ENV)-user-pool'].Id" --output text); \
	if [ -z "$$USER_POOL_ID" ]; then \
		echo "Error: User pool not found for environment $(ENV)"; \
		exit 1; \
	fi; \
	aws cognito-idp admin-delete-user \
		--user-pool-id $$USER_POOL_ID \
		--username $(EMAIL) \
		--profile $(AWS_PROFILE)
	@echo "User deleted successfully: $(EMAIL)"
```

### Example cdk.json

```json
{
  "app": ".venv/bin/python app.py",
  "context": {
    "aws-cdk:enableDiffNoFail": true,
    "@aws-cdk/core:stackRelativeExports": true
  }
}
```

### Example Cognito Stack with Named User Pool (if include_cognito is true)

```python
from aws_cdk import Stack, CfnOutput
from aws_cdk import aws_cognito as cognito
from constructs import Construct
import os

class CognitoStack(Stack):
    def __init__(self, scope: Construct, construct_id: str, **kwargs) -> None:
        super().__init__(scope, construct_id, **kwargs)
        
        env_name = os.environ.get("ENV", "dev")
        
        self.user_pool = cognito.UserPool(
            self, "UserPool",
            user_pool_name=f"{project_name}-{env_name}-user-pool",
            self_sign_up_enabled=True,
            sign_in_aliases=cognito.SignInAliases(email=True),
            auto_verify=cognito.AutoVerifiedAttrs(email=True),
            standard_attributes=cognito.StandardAttributes(
                email=cognito.StandardAttribute(required=True, mutable=True)
            )
        )
        
        self.user_pool_client = self.user_pool.add_client(
            "UserPoolClient",
            auth_flows=cognito.AuthFlow(user_password=True, user_srp=True)
        )
        
        CfnOutput(self, "UserPoolId", value=self.user_pool.user_pool_id)
        CfnOutput(self, "UserPoolClientId", value=self.user_pool_client.user_pool_client_id)
```

### Example Frontend Email Verification Flow (if include_cognito is true)

**SignUp.vue - Redirect to verify page:**
```javascript
const handleSignUp = async () => {
  try {
    await signUp({
      username: email.value,
      password: password.value,
      options: {
        userAttributes: {
          email: email.value
        }
      }
    })
    success.value = true
    error.value = ''
    setTimeout(() => router.push({ path: '/verify', query: { email: email.value } }), 2000)
  } catch (e) {
    error.value = e.message
    success.value = false
  }
}
```

**VerifyEmail.vue - Complete verification page:**
```vue
<template>
  <div class="verify">
    <h2>Verify Email</h2>
    <p v-if="email">Enter the verification code sent to {{ email }}</p>
    <form @submit.prevent="handleVerify">
      <input v-model="email" type="email" placeholder="Email" required />
      <input v-model="code" type="text" placeholder="Verification Code" required />
      <button type="submit">Verify</button>
    </form>
    <p v-if="error" class="error">{{ error }}</p>
    <button @click="resendCode" class="resend" :disabled="!email">Resend Code</button>
  </div>
</template>

<script>
import { ref } from 'vue'
import { confirmSignUp, resendSignUpCode } from 'aws-amplify/auth'
import { useRouter, useRoute } from 'vue-router'

export default {
  setup() {
    const code = ref('')
    const error = ref('')
    const router = useRouter()
    const route = useRoute()
    const email = ref(route.query.email || '')

    const handleVerify = async () => {
      try {
        await confirmSignUp({ username: email.value, confirmationCode: code.value })
        router.push('/login')
      } catch (e) {
        error.value = e.message
      }
    }

    const resendCode = async () => {
      try {
        await resendSignUpCode({ username: email.value })
        error.value = ''
        alert('Verification code resent!')
      } catch (e) {
        error.value = e.message
      }
    }

    return { code, email, error, handleVerify, resendCode }
  }
}
</script>
```

**Login.vue - Add link to verify page:**
```vue
<p class="link">Need to verify your email? <router-link to="/verify">Click here</router-link></p>
```

**router/index.js - Add verify route:**
```javascript
import VerifyEmail from '../views/VerifyEmail.vue'

const routes = [
  { path: '/', component: Home },
  { path: '/login', component: Login },
  { path: '/signup', component: SignUp },
  { path: '/verify', component: VerifyEmail },
  { path: '/protected', component: Protected, meta: { requiresAuth: true } }
]
```

## Troubleshooting

### Virtual Environment Creation Fails
If Python virtual environment creation fails, ensure Python 3.8+ is installed and accessible via `python3` command.

### CDK Bootstrap Issues
If `make cdk-init` fails, verify AWS credentials are properly configured and the AWS profile exists in `~/.aws/credentials` or `~/.aws/config`.

### CDK Module Not Found Error
If you see `ModuleNotFoundError: No module named 'aws_cdk'`, ensure:
1. The `cdk.json` file uses `.venv/bin/python app.py` as the app command (not `python3 app.py`)
2. The Makefile uses `$$PWD/.venv/bin/python` for CDK_PYTHON environment variable
3. Run `make setup` to install dependencies in the virtual environment

### CDK Feature Flag Error
If you see errors about unsupported feature flags like `@aws-cdk/core:enableStackNameDuplicates`, remove CDKv1 feature flags from `cdk.json`. Only use CDKv2-compatible flags.

### Node.js Version Warning
If you see warnings about untested Node.js versions, ensure `JSII_SILENCE_WARNING_UNTESTED_NODE_VERSION=1` is set in all CDK commands in the Makefile.

### Frontend Dependencies Installation Fails
If frontend setup fails, ensure Node.js 16+ and npm are installed and accessible in the system PATH.

### Stack Deployment Fails with Quota Errors
If CloudFormation deployment fails with quota errors, ensure stacks are properly separated by service type. Consider further splitting large stacks into smaller sub-stacks.

### Cannot Create Users After Deployment
If you need to manually create users in Cognito, use `make create-user EMAIL=user@example.com PASSWORD=TempPass123!` to add users via AWS CLI. Users will need to verify their email before logging in.

### Frontend Shows "Network Error" When Signing Up or Logging In
If the frontend cannot connect to Cognito:
1. Ensure infrastructure is deployed: `make deploy`
2. Check that `frontend/.env.development` has been populated with User Pool ID and Client ID
3. Restart the frontend dev server to load new environment variables
4. Verify the AWS region in `.env.development` matches your deployment region

### Email Verification Code Not Received
If users don't receive verification emails:
1. Check AWS SES configuration if in sandbox mode
2. Use `make delete-user EMAIL=user@example.com` to remove the user
3. Try signing up again
4. Use the "Resend Code" button on the verify page
