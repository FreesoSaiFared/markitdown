# Feature-x GitHub Automation Guide

This guide documents the comprehensive GitHub automation setup for the feature-x branch and related workflows.

## Overview

The feature-x automation system provides a complete GitHub workflow management solution with the following capabilities:

- **Automated PR Management**: Auto-approval, auto-merge, and lifecycle management
- **Quality Gates**: Security scanning, code quality checks, and coverage analysis
- **Branch Protection**: Automated branch protection rules and enforcement
- **Monitoring & Analytics**: Real-time monitoring and performance analytics
- **Worktree Management**: Git worktree automation for parallel development

## Workflows

### 1. feature-x-automation.yml

**Primary workflow for automated PR management.**

**Triggers:**
- Push to `feature-x` branch
- Pull requests targeting `feature-x`
- Pull request events from `feature-x` to `main`

**Key Features:**
- Comprehensive testing and validation
- Quality score calculation (0-100)
- Auto-approval based on quality thresholds
- Auto-merge with squash strategy
- Branch cleanup after merge
- Real-time PR status updates

**Configuration:**
```yaml
env:
  AUTO_APPROVE_ENABLED: true
  MERGE_METHOD: squash
  DELETE_BRANCH_AFTER_MERGE: true
```

**Quality Thresholds:**
- Tests: Must pass
- Quality Score: 90+ for auto-merge
- Security: Must pass
- Coverage: 80+ preferred, 70+ required

### 2. quality-gates.yml

**Security and quality validation workflow.**

**Triggers:**
- Push to `feature-x` and `main`
- Pull requests
- Daily schedule (2 AM UTC)

**Security Checks:**
- Bandit static analysis
- Safety dependency scanning
- npm audit (if applicable)

**Quality Checks:**
- Code formatting (Black, isort)
- Linting (Flake8)
- Type checking (MyPy)
- Test coverage analysis
- Performance benchmarks

**Quality Score Calculation:**
- Base: 70 points
- Coverage >80%: +20 points
- Coverage >60%: +10 points
- Security pass: +10 points
- Maximum: 100 points

### 3. branch-protection.yml

**Automated branch protection management.**

**Features:**
- Main branch protection with:
  - Required PR reviews (1 reviewer)
  - Required status checks
  - Conversation resolution required
  - Strict status checks required
- Feature-x special permissions:
  - Auto-approval enabled
  - Permissive settings for automation
  - Force push allowed

### 4. auto-release.yml

**Automated release management.**

**Triggers:**
- Push to `main`
- Manual dispatch

**Features:**
- Semantic versioning
- Automatic changelog generation
- GitHub release creation
- Release notifications

### 5. monitoring.yml

**Workflow monitoring and analytics.**

**Features:**
- Workflow metrics collection
- Performance analytics
- Health checks
- Automated reporting
- Issue cleanup

### 6. worktree-setup.yml

**Git worktree management automation.**

**Actions:**
- Create worktrees for branches
- List existing worktrees
- Cleanup stale worktrees
- Sync all worktrees

## Usage Instructions

### Setting Up Auto-Managed Development

1. **Create feature branch:**
   ```bash
   git checkout -b feature-x
   ```

2. **Create worktree (optional):**
   ```bash
   git worktree add ../markitdown-feature-x feature-x
   cd ../markitdown-feature-x
   ```

3. **Push changes:**
   ```bash
   git push origin feature-x
   ```

4. **Create PR:**
   ```bash
   gh pr create --base main --head feature-x --title "Feature-x changes" --body "Automated PR"
   ```

5. **Monitor automation:**
   - PR will be automatically evaluated
   - Quality gates will run
   - Auto-approval if thresholds met
   - Auto-merge if all conditions satisfied

### Manual Workflow Triggers

**Branch Protection:**
```bash
gh workflow run branch-protection.yml
```

**Worktree Management:**
```bash
# Create worktree
gh workflow run worktree-setup.yml --field action=create --field branch_name=feature-x

# List worktrees
gh workflow run worktree-setup.yml --field action=list

# Cleanup worktrees
gh workflow run worktree-setup.yml --field action=cleanup
```

**Analytics Reports:**
```bash
# Generate summary report
gh workflow run monitoring.yml --field report_type=summary

# Generate detailed report
gh workflow run monitoring.yml --field report_type=detailed
```

**Release Management:**
```bash
# Create patch release
gh workflow run auto-release.yml --field release_type=patch --field create_release=true

# Create minor release
gh workflow run auto-release.yml --field release_type=minor --field create_release=true
```

## Configuration

### Environment Variables

**Feature-x Automation:**
- `AUTO_APPROVE_ENABLED`: Enable/disable auto-approval (default: true)
- `MERGE_METHOD`: Merge strategy - squash, merge, or rebase (default: squash)
- `DELETE_BRANCH_AFTER_MERGE`: Clean up branch after merge (default: true)

### Required Permissions

Workflows require these GitHub permissions:
- `issues: write` - Create and update issues
- `pull-requests: write` - Manage PRs and reviews
- `checks: write` - Set status checks
- `contents: write` - Push changes, manage branches
- `actions: read` - Read workflow runs

### Branch Protection Settings

**Main Branch:**
- Require PR reviews (1 reviewer)
- Require up-to-date branches before merge
- Require status checks to pass before merging
- Require conversation resolution before merging
- Restrict pushes that create matching branches

**Feature-x Branch:**
- Allow auto-approval
- Permit force pushes
- Allow branch deletions
- Flexible status checks

## Monitoring and Maintenance

### Health Monitoring

The system performs automatic health checks every 6 hours:
- Workflow availability
- Recent activity monitoring
- Security configuration validation
- Success rate tracking

### Analytics

Metrics collected include:
- Workflow success rates
- Average execution duration
- Quality score trends
- Security scan results
- Coverage statistics

### Alerts

Automatic alerts are generated for:
- Health score below 80%
- Workflow failures
- Security vulnerabilities
- Quality degradation

## Troubleshooting

### Common Issues

**Auto-merge not working:**
- Check quality score (must be 90+)
- Verify all tests pass
- Ensure security scan passes
- Check branch protection settings

**Workflow failures:**
- Review workflow logs
- Check permissions
- Verify secrets and tokens
- Validate branch configuration

**Quality gates failing:**
- Fix test failures
- Improve code coverage
- Address security issues
- Format code properly

### Manual Overrides

**Bypass auto-merge:**
- Add "do-not-merge" label to PR
- Set draft status on PR
- Disable auto-approval in workflow

**Force merge:**
- Remove required status checks
- Use admin bypass
- Merge manually via GitHub UI

## Best Practices

1. **Code Quality:**
   - Maintain 90+ quality score
   - Keep coverage above 80%
   - Follow style guidelines
   - Write comprehensive tests

2. **Security:**
   - Regular dependency updates
   - Address vulnerabilities promptly
   - Use secure coding practices
   - Monitor security scan results

3. **Workflow Management:**
   - Review workflow logs regularly
   - Monitor performance metrics
   - Update workflows as needed
   - Maintain proper permissions

4. **Development Process:**
   - Use feature branches
   - Create descriptive PRs
   - Review automation decisions
   - Monitor automation health

## Integration with Claude Code

This automation system integrates seamlessly with Claude Code and git worktrees:

1. **Parallel Development:**
   ```bash
   # Create multiple worktrees
   git worktree add ../markitdown-feature-1 feature-1
   git worktree add ../markitdown-feature-2 feature-2

   # Work in isolation
   cd ../markitdown-feature-1
   claude  # Claude Code session
   ```

2. **Claude Flow Integration:**
   ```bash
   # Auto-manage PRs with Claude Flow
   claude-flow github gh-coordinator "auto-manage PRs for feature-x" --auto-approve --branch feature-x
   ```

3. **Context Switching:**
   - Each worktree provides isolated context
   - Claude Code sessions remain separate
   - Automation works across all branches

## Support

For issues or questions about the feature-x automation:

1. Check workflow logs in GitHub Actions
2. Review automation issues in the repository
3. Consult the analytics reports
4. Create an issue using the automation template

---

*This automation system was designed to streamline development workflows while maintaining high code quality and security standards.*