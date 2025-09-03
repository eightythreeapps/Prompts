# GitHub Wiki Submodule Setup Guide

This document provides step-by-step instructions for recreating the GitHub Wiki submodule setup used in the Kanora Media Server project. This setup enables automated documentation management with version control integration.

## Overview

This setup creates a comprehensive documentation system that:
- Uses GitHub Wiki as a git submodule for local editing
- Provides automated documentation generation and updates
- Integrates with development workflow via npm scripts
- Maintains documentation currency through git hooks
- Enables collaborative documentation with version control

## Prerequisites

- Git repository hosted on GitHub
- Node.js and npm installed
- GitHub repository with Wiki enabled
- Write access to the repository

## Step-by-Step Setup

### 1. Enable GitHub Wiki

1. Navigate to your GitHub repository
2. Go to Settings → Features
3. Enable "Wikis" feature
4. Visit the Wiki tab and create initial page (can be temporary)

### 2. Add Wiki as Git Submodule

```bash
# From your project root directory
git submodule add https://github.com/[username]/[repository].wiki.git docs/wiki

# Initialize and update submodule
git submodule update --init --recursive

# Verify submodule is properly configured
git submodule status
```

This creates:
- `docs/wiki/` directory containing the wiki repository
- `.gitmodules` file with submodule configuration

### 3. Configure Git Submodule

Edit `.gitmodules` to ensure correct configuration:

```ini
[submodule "docs/wiki"]
  path = docs/wiki
  url = https://github.com/[username]/[repository].wiki.git
```

### 4. Create Documentation Automation Script

Create `scripts/docs-automation.js` with the following core structure:

```javascript
#!/usr/bin/env node

import { execSync } from 'child_process';
import { readFileSync, writeFileSync, existsSync } from 'fs';
import { join, dirname } from 'path';
import { fileURLToPath } from 'url';

const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);
const projectRoot = join(__dirname, '..');
const wikiPath = join(projectRoot, 'docs', 'wiki');

/**
 * Execute shell command and return output
 */
function exec(command, options = {}) {
  try {
    return execSync(command, { 
      encoding: 'utf8', 
      cwd: projectRoot,
      ...options 
    }).trim();
  } catch (error) {
    console.error(`Error executing: ${command}`);
    console.error(error.message);
    process.exit(1);
  }
}

/**
 * Get current project version from package.json
 */
function getProjectVersion() {
  const packageJson = JSON.parse(readFileSync(join(projectRoot, 'package.json'), 'utf8'));
  return packageJson.version;
}

/**
 * Update version information in documentation
 */
function updateVersionInfo() {
  const version = getProjectVersion();
  const gitInfo = {
    lastCommit: exec('git log -1 --format="%h - %s (%an, %ar)"'),
    branch: exec('git branch --show-current'),
    totalCommits: exec('git rev-list --count HEAD')
  };
  
  const versionPage = `# Version Information

## Current Version: ${version}

### Git Information
- **Branch**: ${gitInfo.branch}
- **Last Commit**: ${gitInfo.lastCommit}
- **Total Commits**: ${gitInfo.totalCommits}

---
*Last updated: ${new Date().toISOString()}*
`;

  writeFileSync(join(wikiPath, 'Version-Information.md'), versionPage);
  console.log('✅ Version information updated');
}

/**
 * Generate session documentation template
 */
function generateSessionTemplate(sessionTitle, objective = '') {
  const today = new Date().toISOString().split('T')[0];
  const filename = `Development-Session-${today}-${sessionTitle.replace(/\s+/g, '-')}.md`;
  
  const template = `# Development Session: ${today} - ${sessionTitle}

**Date**: ${today}  
**Duration**: [Duration]  
**Developer**: [Your name]  

## 📋 Session Overview

### Objective
${objective || '[Primary goals for this session]'}

### Context
[Any background or continuing work from previous sessions]

## 🎯 Tasks Accomplished

### ✅ Completed
- [ ] Task 1: Description

### 🔄 In Progress
- [ ] Task 2: Current status and what remains

## 🔧 Technical Decisions

### Architecture
[Any design decisions made]

### Implementation Approach
[How you solved problems, trade-offs made]

## 📁 Files Changed

### Added
- \`path/to/file.js\` - Purpose and functionality

### Modified
- \`existing/file.js\` - Summary of changes made

## 📝 Knowledge & Learnings

### Code Insights
[What you learned about the existing codebase]

### Best Practices
[Patterns or practices established]

## 🔮 Next Steps

### Immediate (Next Session)
- [ ] Task to continue

---
*This session was documented as part of the automated development workflow.*
`;

  const filePath = join(wikiPath, filename);
  writeFileSync(filePath, template);
  console.log(`✅ Session template created: ${filename}`);
  return filename;
}

/**
 * Commit and push documentation changes
 */
function commitAndPush() {
  console.log('📝 Committing documentation changes...');
  
  // Change to wiki directory and commit
  const wikiGitCommands = [
    'git add .',
    'git commit -m "docs: automated documentation update"',
    'git push origin master'
  ];
  
  wikiGitCommands.forEach(command => {
    try {
      exec(command, { cwd: wikiPath });
    } catch (error) {
      if (!error.message.includes('nothing to commit')) {
        throw error;
      }
    }
  });
  
  // Update submodule reference in main repo
  exec('git add docs/wiki');
  try {
    exec('git commit -m "docs: update wiki submodule reference"');
  } catch (error) {
    if (!error.message.includes('nothing to commit')) {
      console.log('⚠️  No changes to commit in main repository');
    }
  }
  
  console.log('✅ Documentation changes committed');
}

/**
 * Main automation function
 */
function main() {
  const command = process.argv[2] || 'all';
  
  console.log('🚀 Starting documentation automation...');
  
  if (!existsSync(wikiPath)) {
    console.error('❌ Wiki submodule not found. Please run the setup first.');
    process.exit(1);
  }
  
  switch (command) {
    case 'version':
      updateVersionInfo();
      break;
    case 'session':
      const sessionTitle = process.argv[3] || 'Development Work';
      const objective = process.argv[4] || '';
      generateSessionTemplate(sessionTitle, objective);
      break;
    case 'commit':
      commitAndPush();
      break;
    case 'all':
    default:
      updateVersionInfo();
      commitAndPush();
      break;
  }
  
  console.log('✨ Documentation automation completed!');
}

// Run if called directly
if (process.argv[1] === __filename) {
  main();
}

export { main, updateVersionInfo, generateSessionTemplate, commitAndPush };
```

### 5. Add NPM Scripts

Add the following scripts to your `package.json`:

```json
{
  "scripts": {
    "docs:wiki": "node scripts/docs-automation.js",
    "docs:wiki:version": "node scripts/docs-automation.js version",
    "docs:wiki:commit": "node scripts/docs-automation.js commit",
    "docs:wiki:session": "node scripts/docs-automation.js session"
  }
}
```

### 6. Create Initial Wiki Structure

Create these core wiki pages in `docs/wiki/`:

#### Home.md
```markdown
# Project Name Wiki

Welcome to the comprehensive documentation for [Project Name].

## 🚀 Getting Started
- [Installation Guide](Installation-Guide)
- [Quick Start](Quick-Start)
- [Configuration](Configuration)

## 📚 Documentation
- [API Reference](API-Reference)
- [Database Schema](Database-Schema)
- [Development Guide](Development-Guide)

## 🔧 Development
- [Development Sessions](Development-Sessions)
- [Version Information](Version-Information)

---
*This wiki is automatically maintained and updated with each development session.*
```

#### Development-Sessions.md
```markdown
# Development Sessions

This page tracks all development sessions, providing a searchable history of project evolution, technical decisions, and lessons learned.

## Recent Sessions

*Sessions will be automatically listed here*

## Session Guidelines

### Creating a Session
```bash
npm run docs:wiki:session "Session Title" "Clear objective description"
```

### During Development
- Use TodoWrite tool to track progress
- Document technical decisions and rationale
- Record files modified and their purposes

### Completing a Session
- Update session document with accomplishments
- Run quality gates: `npm run test`, `npm run lint`, `npm run build`
- Commit session documentation

---
*Development session management is a core part of the development workflow.*
```

### 7. Set Up Git Hooks (Optional)

Create `.husky/post-commit` for automatic documentation updates:

```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

# Update documentation after commits
npm run docs:wiki:version
```

Make it executable:
```bash
chmod +x .husky/post-commit
```

### 8. Initial Commit and Verification

```bash
# Add and commit the submodule
git add .gitmodules docs/wiki
git commit -m "Add GitHub Wiki as submodule with automation"

# Test the setup
npm run docs:wiki:version

# Create a test session
npm run docs:wiki:session "Setup Verification" "Testing the wiki setup"

# Commit changes
npm run docs:wiki:commit

# Verify on GitHub Wiki
```

## Usage Workflow

### Daily Development
```bash
# Start a development session
npm run docs:wiki:session "Feature Name" "Implementation objective"

# During development - use TodoWrite tool extensively

# End of session - commit documentation
npm run docs:wiki:commit
```

### Maintenance Tasks
```bash
# Update version information
npm run docs:wiki:version

# Generate comprehensive documentation
npm run docs:wiki

# Manual commit when needed
npm run docs:wiki:commit
```

## Submodule Management

### Working with Submodules
```bash
# Update submodule to latest
cd docs/wiki
git pull origin master
cd ../..
git add docs/wiki
git commit -m "Update wiki submodule"

# Clone repository with submodules
git clone --recurse-submodules [repository-url]

# Initialize submodules in existing clone
git submodule update --init --recursive
```

### Troubleshooting Submodules
```bash
# Reset submodule if corrupted
git submodule deinit -f docs/wiki
git rm --cached docs/wiki
rm -rf docs/wiki
git submodule add https://github.com/[username]/[repository].wiki.git docs/wiki
git submodule update --init

# Fix detached HEAD in submodule
cd docs/wiki
git checkout master
cd ../..
```

## Customization Options

### Adding Documentation Types
Extend `scripts/docs-automation.js` with new functions:
- API documentation generation
- Database schema extraction
- Test coverage reports
- Architecture diagrams

### Integration with CI/CD
Add documentation checks to your CI pipeline:
```yaml
- name: Verify Documentation
  run: |
    npm run docs:wiki:version
    git diff --exit-code docs/wiki/ || (echo "Documentation out of date" && exit 1)
```

### Custom Templates
Modify `generateSessionTemplate()` function to match your team's needs:
- Add project-specific sections
- Include compliance checklists
- Integrate with issue tracking systems

## Benefits of This Setup

1. **Version Control Integration**: Full git history for all documentation
2. **Local Development**: Edit documentation with your preferred tools
3. **Automated Currency**: Documentation stays current automatically
4. **Collaborative**: Standard git workflow for documentation contributions
5. **Searchable History**: Development sessions create knowledge base
6. **Zero Overhead**: Integrated into existing development workflow

## Migration from Existing Wiki

If you have existing GitHub Wiki content:

```bash
# Clone existing wiki content
git clone https://github.com/[username]/[repository].wiki.git temp-wiki

# Copy content to submodule
cp -r temp-wiki/* docs/wiki/

# Commit migrated content
cd docs/wiki
git add .
git commit -m "Migrate existing wiki content"
git push origin master

# Clean up
rm -rf temp-wiki
```

## Security Considerations

- Submodule URLs use HTTPS (can be changed to SSH for private repos)
- Automation script has minimal permissions (read package.json, write to wiki)
- Git hooks run in controlled environment
- No sensitive data stored in automation scripts

---

This setup provides a robust, automated documentation system that scales with your project and integrates seamlessly with your development workflow.
