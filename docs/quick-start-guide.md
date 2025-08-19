# Quick Start Guide

Get your project documented in under 10 minutes with the Claude Code Documentation Crew - an intelligent multi-agent system that analyzes your codebase and generates comprehensive documentation automatically.

## Prerequisites

Before you begin, ensure you have:

- **Claude Code CLI** - Required for running the documentation workflow
  - Check if installed: `claude-code --version`
  - Install from: [Claude Code CLI Installation Guide](https://claude.ai/code)
- **Git** - For cloning the project
  - Check if installed: `git --version`
- **Docker** (optional) - For local Kroki diagram service
  - Check if installed: `docker --version`
  - Only needed if you want faster, private diagram generation

## Installation

### Step 1: Clone the Documentation Crew

```bash
# Clone the documentation crew project
git clone https://github.com/your-org/claude-code-documentation-crew.git

# Navigate into the documentation crew directory
cd claude-code-documentation-crew
```

> **Important**: You must run all documentation commands FROM the documentation crew directory. The tool analyzes OTHER projects by pointing to their paths.

### Step 2: Verify Installation

Test that the tool is accessible:

```bash
# Test the documentation workflow is available
/docs:generate --help
```

If you see command help output, you're ready to proceed. If you get "command not found", ensure you're in the documentation crew directory and Claude Code CLI is properly installed.

## Optional: Local Kroki Docker Setup

**Kroki Docker is optional** - the tool automatically falls back to the public kroki.io API if no local service is detected.

### Benefits of Local Setup:
- **Faster diagram generation** (no network round trips)
- **Better privacy** (diagrams processed locally)
- **No internet dependency** for diagram features

### Quick Setup:

```bash
# Start local Kroki service (optional)
docker run -d -p 8001:8000 yuzutech/kroki

# Verify it's running (optional)
curl http://localhost:8001/health
```

### Without Local Setup:
If you skip Docker setup, the tool automatically uses kroki.io:
- Slightly slower diagram generation
- Requires internet connection for diagrams
- Full functionality maintained

## Generate Your First Documentation

### Step 1: Run the Documentation Generator

From the documentation crew directory, start the documentation workflow:

```bash
/docs:generate
```

The system will ask you several questions:

1. **Which project do you want to document?**
   - Enter the full path to YOUR project (e.g., `/Users/yourname/my-project`)
   - Default: current directory (but you're in the crew directory, so specify your actual project)

2. **Which documents do you need?**
   - Choose from: API Reference, User Guide, Architecture Overview, Security Documentation, Deployment Guide
   - For first-time users, try "User Guide" or "Architecture Overview"

3. **Target audience?**
   - Choose: Developers, End users, Business stakeholders, Operations team
   - Pick the primary audience for your documentation

4. **Output documentation location?**
   - Default: `{project-path}/docs` (creates docs folder in YOUR target project)
   - Or specify a custom path

5. **Include diagrams?**
   - Yes: Enhanced documentation with visual diagrams
   - No: Text-only documentation (faster generation)

6. **Document format?**
   - Markdown (recommended): Works with GitHub, GitLab, etc.
   - HTML also available

### Step 2: Wait for Processing

The tool will:
1. **Analyze your codebase** (~1-3 minutes)
2. **Generate questions and validate findings** (~2-4 minutes)
3. **Create documentation** (~1-2 minutes)
4. **Generate diagrams** (~1-3 minutes, if enabled)

**Progress indicators:**
- You'll see agent status updates
- File creation notifications
- Validation checkpoints

### Step 3: Verify Success

Check your project's documentation:

```bash
# Navigate to your project
cd /path/to/your/project

# Check generated documentation
ls docs/

# View the documentation
cat docs/user-guide.md  # or your chosen document type
```

**Success indicators:**
- Documentation files exist in your specified output location
- Content includes project-specific details (not generic templates)
- If diagrams were requested, `assets/` folder contains `.svg` files

## Quick Troubleshooting

### "Command not found" Error

**Problem**: `/docs:generate` not recognized

**Solutions**:
1. Ensure you're in the documentation crew directory
2. Verify Claude Code CLI installation: `claude-code --version`
3. Try running from the exact clone location

### "No project found" or Analysis Errors

**Problem**: Tool can't analyze your project

**Solutions**:
1. Verify the project path you entered exists
2. Ensure the project has source code files (not just README)
3. Check file permissions on your project directory

### Kroki/Diagram Issues

**Problem**: Diagram generation fails

**Solutions**:
1. **With Docker**: Check `docker ps` shows Kroki container running
2. **Without Docker**: Ensure internet connection for kroki.io fallback
3. **Skip diagrams**: Re-run with "Include diagrams? No" for faster results

### "Permission denied" Errors

**Problem**: Cannot write to output location

**Solutions**:
1. Check write permissions on your project directory
2. Try a different output location (e.g., `/tmp/docs`)
3. Run with appropriate permissions for your system

### Slow Performance

**Expected times**:
- Small projects (< 50 files): 3-5 minutes total
- Medium projects (50-200 files): 5-8 minutes total
- Large projects (200+ files): 8-15 minutes total

**If slower than expected**:
1. Check internet connection (for kroki.io fallback)
2. Set up local Kroki Docker for faster diagrams
3. Choose "No" for diagrams to reduce processing time

## What's Next?

Once you have basic documentation:

1. **Review the output** - Check accuracy and completeness
2. **Try different document types** - Generate API Reference, Architecture Overview
3. **Customize for different audiences** - Generate developer vs. end-user focused docs
4. **Set up regular generation** - Integrate into your development workflow
5. **Explore advanced options** - Security documentation, deployment guides

## Quick Reference

**Basic command**: `/docs:generate` (run from documentation crew directory)

**Essential paths**:
- Documentation crew location: Where you cloned this tool
- Your project path: What you want documented
- Output location: Where documentation gets saved (usually your project's `/docs`)

**Kroki setup**: `docker run -d -p 8001:8000 yuzutech/kroki` (optional)

**Support**: Check the full User Guide for detailed options and advanced configuration.

---

*Generated documentation quality depends on your project's code structure and comments. Well-commented, organized codebases produce the best documentation results.*