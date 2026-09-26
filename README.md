# budget-mcp-for-ynab

An [MCP](https://modelcontextprotocol.io) server that lets AI assistants like Claude help with your
[YNAB](https://www.ynab.com) budget: answer spending questions, review categories, and find where the money
went.

> [!WARNING]
> **Early development.** Nothing is usable yet. Watch or star the repo to follow along.

## Status

The design is in progress. Planned highlights:

- **Local first.** Runs on your machine with your own YNAB Personal Access Token.
- **Read-only by default.** Anything that changes your budget will require explicit opt-in and
  confirmation.
- **Cross-platform.** Windows and macOS are supported first-class.

Installation and configuration docs will be added when there's something to install.

## Security

This project handles personal financial data. Never commit or share your YNAB access token. Treat it like
your account password.

## License

[MIT](LICENSE)

---

*We are not affiliated, associated, or in any way officially connected with YNAB or any of its subsidiaries
or affiliates. The official YNAB website can be found at <https://www.ynab.com>. The names YNAB and You Need
A Budget, as well as related names, marks, and images, are registered trademarks of YNAB.*
