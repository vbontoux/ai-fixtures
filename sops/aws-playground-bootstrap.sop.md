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
- You MUST create `cdk/`, `scripts/`, `backend/`, `docs/`, `.kiro/steering/` subdirectories
- You MUST create `frontend/` subdirectory if include_frontend is true
- You MUST create `backend/tests/` directory for pytest tests
- You MUST create `frontend/tests/` directory if include_frontend is true

### 2. Create Makefile

Create a comprehensive Makefile at the project root with environment management and all project commands.

**Constraints:**
- You MUST include ENV variable management with default value "dev"
- You MUST dynamically map ENV to AWS_PROFILE based on environments parameter
- You MUST prefix all AWS CLI commands with the appropriate AWS_PROFILE for current ENV
- You MUST activate `.venv` for CDK commands (using `cdk/.venv`)
- You MUST activate `.venv` for scripts commands (using `scripts/.venv`)
- You MUST activate `.venv` for backend commands (using `backend/.venv`)
- You MUST include these targets: help, setup, cdk-init, deploy, destroy, test, test-backend, test-frontend (if applicable), run-frontend (if applicable)
- You MUST install AWS CDK locally in the setup target to enable infrastructure deployment
- You MUST document all commands in the help target
- You MUST support ENV parameter override (e.g., `make deploy ENV=staging`)

### 3. Initialize CDK Structure

Set up the CDK directory with Python virtual environment and infrastructure code.

**Constraints:**
- You MUST create Python virtual environment at `cdk/.venv`
- You MUST create `cdk/requirements.txt` with CDK dependencies
- You MUST create `cdk/app.py` as the CDK entry point
- You MUST create `cdk/cdk.json` configuration file
- You MUST create `cdk/stacks/` directory for stack modules
- You MUST create at least one main stack even if no resources are requested
- You MUST use stack naming pattern: `{project_name}-{env}-{stack_type}`
- You MUST separate stacks by service type (compute, storage, networking) to avoid CloudFormation quotas
- You MUST create API Gateway stack in `cdk/stacks/api_gateway_stack.py` if include_api_gateway is true
- You MUST create DynamoDB stack in `cdk/stacks/dynamodb_stack.py` if include_dynamodb is true
- You MUST create Cognito stack in `cdk/stacks/cognito_stack.py` if include_cognito is true
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
- You MUST create environment configuration files
- You MUST create login page with authentication form if include_cognito is true
- You MUST integrate AWS Amplify library for Cognito authentication if include_cognito is true
- You MUST create a simple menu with link to protected page if include_cognito is true
- You MUST create one protected page that requires authentication if include_cognito is true
- You MUST keep home page non-protected (accessible without login) if include_cognito is true
- You MUST configure Amplify with Cognito User Pool details in environment config if include_cognito is true
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
- You MUST format each steering file with clear guidelines

### 10. Verify Project Setup

Confirm that all components are properly initialized and ready to use.

**Constraints:**
- You MUST verify all directories and files have been created
- You MUST verify documentation folder exists with all required documentation files
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
│   │   │   └── Protected.vue
│   │   ├── router/
│   │   │   └── index.js
│   │   └── components/
│   │       └── Navigation.vue
│   ├── tests/
│   └── .env.development
└── .kiro/
    └── steering/
        ├── unit-testing.md
        ├── api-mocking.md
        ├── code-reuse.md
        ├── cdk-stack-separation.md
        ├── makefile-commands.md
        └── documentation-updates.md
```

## Troubleshooting

### Virtual Environment Creation Fails
If Python virtual environment creation fails, ensure Python 3.8+ is installed and accessible via `python3` command.

### CDK Bootstrap Issues
If `make cdk-init` fails, verify AWS credentials are properly configured and the AWS profile exists in `~/.aws/credentials` or `~/.aws/config`.

### Frontend Dependencies Installation Fails
If frontend setup fails, ensure Node.js 16+ and npm are installed and accessible in the system PATH.

### Stack Deployment Fails with Quota Errors
If CloudFormation deployment fails with quota errors, ensure stacks are properly separated by service type. Consider further splitting large stacks into smaller sub-stacks.
