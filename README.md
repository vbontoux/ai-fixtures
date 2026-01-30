# AI Fixtures

A collection of reusable AI artifacts for development workflows, including prompts, SOPs (Standard Operating Procedures), and Kiro CLI steering files.

## Contents

### SOPs (`/sops`)

Agent SOPs following the [agent-sop format](https://github.com/strands-agents/agent-sop) - structured workflows that guide AI agents through complex, repeatable tasks.

- `aws-playground-bootstrap.sop.md` - Bootstrap standardized AWS playground projects with CDK, Lambda, and optional frontend

### Prompts

Reusable prompt templates for common development tasks.

### Steerings

Kiro CLI steering files that customize agent behavior and provide project-specific context.

## Usage

### With Kiro CLI

Reference SOPs directly in conversations:
```bash
kiro-cli chat
# Then in chat: "Follow the aws-playground-bootstrap SOP to create a new project"
```

Add steerings to your projects by copying them to `.kiro/steering/` in your project root.

### With Other AI Tools

Copy and adapt the prompts and SOPs for use with other AI assistants or automation tools.

## Contributing

When adding new fixtures:

- SOPs must follow the `.sop.md` format with Overview, Parameters, and Steps sections
- Use RFC2119 keywords (MUST, SHOULD, MAY) for constraints
- Include examples where helpful
- Keep prompts focused and reusable

## License

MIT
