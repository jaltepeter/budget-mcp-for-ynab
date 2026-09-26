# CLAUDE.md

Guidance for Claude Code (and other AI assistants) working in this repository.

## Project

`budget-mcp-for-ynab` is an MCP server that lets AI assistants work with a user's YNAB budget. It's in
early development: the language and tooling haven't been chosen yet, so don't scaffold a project, add
dependencies, or assume a stack without being asked.

## Hard rules

- **Never commit secrets or real financial data.** No access tokens, and no real transactions, payees,
  balances, or account names in code, fixtures, tests, logs, docs, or screenshots. Use synthetic data only.
- **Branding:** don't use "YNAB" in any product, package, or domain name except right after the word
  "for" (e.g. "... for YNAB"). Never suggest the project is affiliated with or endorsed by YNAB. This
  follows the YNAB API Terms of Service: https://api.ynab.com/#terms
- **Cross-platform:** code, scripts, and docs must work on native Windows and macOS, not just Linux.
  Use path APIs instead of hardcoded separators, and don't write POSIX-only scripts.
- **Changes go through pull requests** into `main`. Direct pushes are blocked.

## Documentation standard

Documentation is part of the definition of done. A change that doesn't meet this standard isn't finished.

1. **File headers:** every source file starts with a comment describing what the module is responsible
   for and where it sits in the architecture.
2. **Doc comments on every export:** purpose, parameters, return value, errors thrown, and an example
   when usage isn't obvious.
3. **Comments explain *why*, not *what*:** constraints, API quirks, and tradeoffs. Don't narrate the code.
4. **Cite sources:** link the relevant MCP spec or YNAB API doc section when behavior depends on it.
5. **Tool and schema descriptions count as documentation:** every MCP tool, parameter, and output field
   has a clear description written for the model that will read it.
6. **Docs change with behavior:** update the README, `docs/`, and doc comments in the same PR as the
   behavior change.
