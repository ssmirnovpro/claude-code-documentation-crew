# Contributing to Claude Code Documentation Crew

Thank you for your interest in contributing to the Claude Code Documentation Crew! This project demonstrates how to build sophisticated multi-agent systems for automatic documentation generation using Claude Code CLI.

## 🎯 How to Contribute

We welcome contributions in several areas:

- **🐛 Bug reports** - Issues with documentation generation quality or process
- **✨ Feature requests** - New agent capabilities or workflow improvements  
- **📝 Documentation** - Improvements to guides, examples, or agent instructions
- **🔧 Agent enhancements** - Better prompts, new agent types, or workflow optimizations
- **🧪 Testing** - Test documentation generation on diverse project types
- **💡 Examples** - Sample projects that showcase different use cases

## 🚀 Getting Started

### Prerequisites

- **Claude Code CLI** (latest version) - [Installation Guide](https://claude.ai/code)
- **Git** for version control
- **Docker** (optional) for local Kroki diagram service
- **Basic understanding** of Claude Code CLI workflows and agent systems

### Development Setup

1. **Fork and Clone**
   ```bash
   git fork https://github.com/your-org/claude-code-documentation-crew.git
   cd claude-code-documentation-crew
   ```

2. **Verify Setup**
   ```bash
   # Test the documentation workflow
   /docs:generate --help
   ```

3. **Install Local Kroki** (recommended for testing)
   ```bash
   docker run -d -p 8001:8000 yuzutech/kroki
   ```

### Project Structure

Understanding the project layout is crucial for contributions:

```
claude-code-documentation-crew/
├── .claude/                    # Claude Code CLI configuration
│   ├── agents/                # Agent instruction files
│   ├── commands/              # CLI command definitions  
│   └── workflows/             # Workflow orchestration
├── docs/                      # Generated documentation
├── .ccdocs/                   # Temporary workspace (auto-created)
└── CLAUDE.md                  # Project instructions
```

## 🛠️ Development Workflow

### Testing Your Changes

1. **Create Test Projects**
   ```bash
   mkdir test-projects/
   # Create sample projects in different languages
   ```

2. **Test Documentation Generation**
   ```bash
   # Test on a sample project
   /docs:generate ./test-projects/sample-node-app
   ```

3. **Validate Output Quality**
   - Check generated documentation accuracy
   - Verify diagrams render properly  
   - Ensure content matches project structure
   - Test different document types and audiences

### Agent Development

When modifying or creating agents:

1. **Agent Instructions** (`.claude/agents/*.md`)
   - Use clear, specific prompts
   - Include examples and constraints
   - Define input/output formats precisely
   - Test with various project types

2. **Agent Coordination** (`.claude/workflows/*.md`)
   - Maintain the 8-phase workflow structure
   - Ensure proper handoff protocols
   - Include error handling and validation
   - Document agent dependencies

3. **Testing Agent Changes**
   ```bash
   # Test specific agent modifications
   /docs:generate ./test-project --verbose
   # Check .ccdocs/ for agent outputs
   ```

## 📋 Contribution Guidelines

### Code Standards

- **Agent Instructions**: Clear, actionable prompts with specific constraints
- **Workflow Files**: Maintain consistent phase structure and handoff protocols
- **Documentation**: Keep project docs updated when adding features
- **Testing**: Verify changes work across different project types

### Commit Message Format

```
type(scope): brief description

Detailed explanation of changes made.

Examples:
feat(agent): add security reviewer capabilities
fix(workflow): improve error handling in phase 5  
docs(readme): update quick start guide
test(samples): add Python project test case
```

### Pull Request Process

1. **Create Feature Branch**
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. **Make Changes and Test**
   - Implement your changes
   - Test with multiple project types
   - Update documentation if needed

3. **Submit Pull Request**
   - Clear title and description
   - Reference related issues
   - Include testing details
   - Add screenshots for UI changes

4. **Review Process**
   - Maintainer review and feedback
   - Address any requested changes
   - Merge after approval

## 🧪 Testing Guidelines

### Manual Testing

Test documentation generation on projects with:

- **Different languages**: JavaScript, Python, Java, Go, etc.
- **Various sizes**: Small (< 50 files), medium (50-200), large (200+)
- **Project types**: APIs, web apps, libraries, microservices
- **Document types**: User guides, API docs, architecture overviews

### Quality Checks

Verify generated documentation includes:

- **Accurate content** that matches actual code
- **Proper structure** with clear sections and flow
- **Working diagrams** (if requested) with readable sizing
- **Audience-appropriate** language and detail level
- **Complete coverage** of main project features

### Test Cases to Cover

```bash
# Test different document types
/docs:generate ./sample-api --type="API Reference"
/docs:generate ./sample-app --type="User Guide"  
/docs:generate ./sample-service --type="Architecture Overview"

# Test different audiences
/docs:generate ./project --audience="Developers"
/docs:generate ./project --audience="End users"

# Test with/without diagrams
/docs:generate ./project --diagrams
/docs:generate ./project --no-diagrams
```

## 🎨 Agent Enhancement Guidelines

### Adding New Agents

1. **Create Agent File** (`.claude/agents/new-agent.md`)
   ```markdown
   # New Agent Name
   
   description: Brief description starting with 'Use this agent when...'
   
   ## Purpose
   Detailed explanation of agent capabilities
   
   ## Instructions
   Specific, actionable prompts
   ```

2. **Update Workflow** (`.claude/workflows/docs-generation.md`)
   - Add agent to selection criteria
   - Define when agent is used
   - Specify input/output requirements

3. **Test Integration**
   - Verify agent coordination
   - Check handoff protocols
   - Validate output quality

### Improving Existing Agents

- **Enhance prompts** for better output quality
- **Add error handling** for edge cases
- **Improve efficiency** without sacrificing quality
- **Expand capabilities** while maintaining focus

## 📝 Documentation Contributions

### Types of Documentation Improvements

- **User guides** - Clearer instructions and examples
- **Agent documentation** - Better prompt engineering
- **Workflow explanations** - Process clarification
- **Troubleshooting** - Common issues and solutions
- **Examples** - Sample projects and outputs

### Documentation Standards

- **Clear headings** and logical structure
- **Step-by-step instructions** with code examples
- **Screenshots** for complex UI interactions
- **Links** to related sections and external resources
- **Consistent formatting** with existing docs

## 🐛 Bug Reports

### Reporting Issues

Use the issue template and include:

- **Project type** and size being documented
- **Command used** and options selected
- **Expected behavior** vs. actual results
- **Error messages** (full output preferred)
- **Generated files** (if relevant) as attachments
- **System info** (OS, Claude Code CLI version)

### Quality Issues

For documentation quality problems:

- **Specific examples** of inaccurate content
- **Source code context** that was misunderstood
- **Suggested improvements** for agent prompts
- **Sample outputs** showing the issue

## 💡 Feature Requests

### Proposing New Features

- **Use case description** - What problem does this solve?
- **Proposed solution** - How should it work?
- **Agent involvement** - Which agents would be affected?
- **Implementation ideas** - Technical approach suggestions
- **Examples** - Sample inputs and expected outputs

### Popular Enhancement Areas

- **New document types** (deployment guides, troubleshooting docs)
- **Language support** (additional programming languages)
- **Integration options** (CI/CD, IDE plugins)
- **Customization features** (templates, styling)
- **Performance improvements** (faster processing)

## 🔒 Security Considerations

When contributing, be mindful of:

- **No sensitive data** in test projects or examples
- **Prompt injection** prevention in agent instructions  
- **File access controls** in workspace management
- **External service** security (Kroki integration)

## 🌟 Recognition

Contributors will be recognized in:

- **README contributors** section
- **Release notes** for significant contributions
- **Documentation credits** for major improvements
- **GitHub contributors** graph and statistics

## 📞 Getting Help

- **GitHub Discussions** - Questions and general discussion
- **GitHub Issues** - Bug reports and feature requests
- **Documentation** - Check existing guides first
- **Examples** - Review sample projects and outputs

## 🎉 Thank You!

Every contribution helps make the Claude Code Documentation Crew better for everyone. Whether you're fixing a typo, improving an agent prompt, or adding new capabilities, your efforts are appreciated!

---

*This contributing guide was generated by the Documentation Crew as part of demonstrating its capabilities for creating comprehensive project documentation.*