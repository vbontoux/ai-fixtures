# AWS Playground Project Bootstrap SOP Creation Prompt

Create an SOP that bootstraps a standardized AWS playground development project for experimenting with concepts.

## Project Structure

Root directory (named after project):
- **Makefile**: All commands for deploy, test, compile. Manages ENV variable (default: "dev" → AWS_PROFILE "default"). All AWS CLI commands must use the ENV-mapped AWS_PROFILE dynamically.

- **cdk/**: Python CDK infrastructure
  - `.venv` for CDK dependencies
  - Makefile activates venv dynamically for CDK commands
  - Minimal CDK app with at least one stack (even if no resources requested initially)
  - Stack naming: `{project_name}-{env}-{stack_type}` (e.g., `myapp-dev-compute-stack`)
  - Main/sub-stack separation by service type (compute, storage, networking, etc.) to avoid CloudFormation quotas

- **scripts/**: Python utility scripts (deployment helpers, data migration, testing utilities)
  - Single `.venv` with single `requirements.txt` for all scripts

- **backend/**: Lambda functions
  - Organized following AWS best practices
  - Separate `tests/` directory ready for immediate use with pytest

- **frontend/**: Vue.js application (optional)
  - Standard Vue.js structure with subdirectories for dependencies, env config, tests
  - Separate `tests/` directory ready for immediate use with jest/vitest
  - Clean, simple default home page style

- **.kiro/steering/**: Project-specific steering files (5 files, see below)

- **.gitignore**: Exclude `.venv/`, `node_modules/`, `cdk.out/`, `.env.*.local`, IDE files

## Makefile Commands

Required targets:
- `help`: Display all commands with descriptions
- `setup`: Create all venvs, install all dependencies (Python, npm)
- `cdk-init`: Bootstrap CDK (separate from setup, user runs once)
- `deploy`: Deploy infrastructure (usage: `make deploy ENV=staging`)
- `destroy`: Destroy infrastructure
- `test`: Run all tests (backend + frontend if applicable)
- `test-backend`: Run backend tests only
- `test-frontend`: Run frontend tests only (if applicable)
- `run-frontend`: Start frontend dev server (if applicable)

## Steering Files (.kiro/steering/)

Create 5 steering files (follow Kiro IDE template if possible, otherwise simple markdown with clear rules):

1. **unit-testing.md**: Unit tests MUST be done and executed at end of each implementation task
2. **api-mocking.md**: All APIs MUST be mocked for unit tests
3. **code-reuse.md**: Code SHOULD be reused as much as possible
4. **cdk-stack-separation.md**: CDK stacks MUST be separated into main/sub-stacks by service type following AWS guidelines
5. **makefile-commands.md**: All project commands MUST be in Makefile and documented in help target

## SOP Input Parameters

The SOP must ask for:
1. **project_name** (required): Folder name for root directory
2. **environments** (required): List of ENV names with matching AWS_PROFILE (default: dev=default)
3. **environment_variables** (optional): Any additional environment-specific variables needed (API endpoints, region, account ID, etc.)
4. **include_api_gateway** (optional): Initialize CDK with API Gateway?
5. **include_dynamodb** (optional): Initialize CDK with DynamoDB?
6. **include_frontend** (optional): Create Vue.js frontend?
7. **frontend_config** (optional, if frontend=yes): Title and purpose description for home page
8. **git_remote_url** (optional): Remote repository URL to configure

## Expected Outcome

After running `make setup` and `make cdk-init`, the user should be able to immediately:
- Run `make deploy` to deploy infrastructure
- Run `make test` to execute tests
- Run `make run-frontend` to start frontend (if applicable)
- Commit to git (user handles initial commit and push)

## SOP Format Requirements

Follow the agent-sop-format.md standard:
- Use `.sop.md` extension
- Include Overview, Parameters, and Steps sections
- Use RFC2119 keywords (MUST, SHOULD, MAY) in constraints
- Provide context for negative constraints (MUST NOT, SHOULD NOT)
- Use snake_case for parameter names
- Include examples


