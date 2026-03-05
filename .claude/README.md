# Claude Skills for Polymarket CLI

This directory contains Claude skills for the Polymarket CLI, enabling AI-assisted interaction with Polymarket prediction markets.

## What is a Claude Skill?

A Claude skill is a structured knowledge base that helps Claude AI understand and use specific tools, APIs, or command-line interfaces. Skills provide detailed information about commands, parameters, expected outputs, and usage patterns.

## Available Skills

### Polymarket CLI Skill

**File**: `commands/polymarket.md`

**Description**: Comprehensive documentation of all 80+ commands in the Polymarket CLI, including:
- Complete command reference with all parameters
- Expected outputs and return values
- Authentication requirements
- Common workflows and examples
- Error handling guidance
- Scripting patterns

**Coverage**:
- 15 command groups (Markets, Events, Tags, Series, Comments, Profiles, Sports, Approve, CLOB, CTF, Data, Bridge, Wallet, Setup, Shell)
- 80+ subcommands fully documented
- All parameters, flags, and options explained
- Both table and JSON output formats
- Read-only and authenticated operations

## How to Use the Skill

### With Claude Code

When using Claude Code with this repository, Claude will automatically have access to the skill documentation and can:

1. **Answer questions** about any Polymarket CLI command
2. **Generate commands** with correct parameters
3. **Create scripts** that use the CLI effectively
4. **Explain outputs** and help interpret results
5. **Suggest workflows** for common tasks
6. **Debug errors** and provide solutions

### Example Interactions

**Example 1: Find markets**
```
You: "How do I find markets about Bitcoin?"
Claude: "You can use: polymarket markets search 'bitcoin' --limit 20"
```

**Example 2: Place an order**
```
You: "I want to buy 100 shares at $0.55 on token ID 123456789"
Claude: "Use: polymarket clob create-order --token 123456789 --side buy --price 0.55 --size 100"
```

**Example 3: Complex workflow**
```
You: "Create a script to monitor my positions and alert if any change"
Claude: [Creates a bash script using the appropriate commands with JSON output]
```

**Example 4: Portfolio analysis**
```
You: "Check my portfolio value and list all open positions"
Claude: "Run these commands:
  1. polymarket data value $(polymarket wallet address)
  2. polymarket data positions $(polymarket wallet address) --limit 50"
```

## Skill Structure

The skill is organized by command groups, with each section containing:

1. **Command syntax** with all parameters
2. **Parameter descriptions** including types, defaults, and constraints
3. **Authentication requirements** (read-only vs. wallet required)
4. **Output format** description
5. **Real examples** with actual use cases
6. **Common patterns** and best practices

## Updating the Skill

When the Polymarket CLI is updated with new features:

1. Update `commands/polymarket.md` with new commands
2. Add examples and parameter descriptions
3. Update version information
4. Document any breaking changes
5. Add new workflow examples if relevant

## Integration with MCP

This skill can be used with Model Context Protocol (MCP) servers:

1. The skill format follows MCP conventions
2. Can be exposed as an MCP resource
3. Enables programmatic access to CLI knowledge
4. Supports tool-calling patterns

## Best Practices

### For Users
- **Ask specific questions** about commands you need
- **Request examples** when unsure about syntax
- **Use JSON output** (`-o json`) for scripting
- **Check authentication** requirements before running wallet commands

### For Developers
- **Keep skill updated** with CLI changes
- **Add examples** for new features
- **Document edge cases** and limitations
- **Include error scenarios** and solutions

## Technical Details

### Skill Format
- **Format**: Markdown with structured sections
- **Syntax**: GitHub-flavored markdown
- **Code blocks**: Bash/shell syntax highlighting
- **Structure**: Hierarchical with clear headers

### Coverage Completeness
- ✅ All 15 command groups documented
- ✅ All 80+ subcommands included
- ✅ Every parameter explained
- ✅ Both output formats (table/JSON) covered
- ✅ Authentication requirements specified
- ✅ Examples for common operations
- ✅ Error handling guidance
- ✅ Workflow patterns included

### Maintenance
- Skill should be updated when CLI version changes
- Check against `polymarket --help` output
- Verify parameter accuracy against source code
- Test examples to ensure they work

## Contributing

To improve the skill:

1. **Add missing examples**: If you find useful patterns, add them
2. **Clarify documentation**: Improve unclear descriptions
3. **Add troubleshooting**: Document common issues and solutions
4. **Update for new versions**: Keep in sync with CLI updates
5. **Add automation examples**: Share scripts and workflows

## Resources

- **CLI Repository**: https://github.com/Polymarket/polymarket-cli
- **Polymarket Docs**: https://docs.polymarket.com
- **Claude Code**: https://claude.com/claude-code
- **MCP Protocol**: https://modelcontextprotocol.io

## License

This skill documentation follows the same license as the Polymarket CLI (MIT).

---

**Created**: 2026-03-05
**CLI Version**: 0.1.4+
**Maintained by**: Claude AI with human oversight
