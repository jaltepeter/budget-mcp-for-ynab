# User stories

These are the jobs this server helps with, written the way someone would ask Claude. They drive the tool design: each tool exists to serve one or more stories, not to mirror a YNAB API endpoint.

The user is a household that shares one YNAB plan. More than one person can edit the plan, so data can change between calls.

Stories are prioritized with MoSCoW:

- **Must:** in the first release, which is read-only.
- **Should:** next, starting with write operations.
- **Could:** worth doing once the core is solid.
- **Won't:** out of scope for now.

Endpoint paths are relative to `https://api.ynab.com/v1/plans/{plan_id}`. For endpoint details, see the [YNAB API reference](https://api.ynab.com/v1).

## Must

The following table lists the stories for the first release:

| ID | Story | Endpoints | Design notes |
| --- | --- | --- | --- |
| US-01 | "What transactions came in recently?" | `GET /transactions` with `since_date`, or `type=unapproved` for new ones waiting for approval | "Recently" needs a default window. Delta requests (`last_knowledge_of_server`) avoid refetching unchanged transactions. |
| US-02 | "How much do we have in checking?" | `GET /accounts` | People name accounts loosely, so names need matching. The answer differs for on-budget and tracking accounts. |
| US-03 | "How much fun money do we have left?" | `GET /months/current` | The user's words might not match a category name, or might mean a whole category group. The model can't match names it hasn't fetched. |
| US-04 | "Are we overspent anywhere right now?" | `GET /months/current` | Overspent means a negative available balance (`balance` in the API). Return only the overspent categories, not the whole list. |
| US-05 | "Where could we have done better last month?" | `GET /months/{month}` for last month and several before it, `GET /months/{month}/money_movements` | "Better" means staying within the assigned amount or target in categories that usually go over. Finding "usually" costs one request per month of history. Money moved in to cover overspending hides it in the month totals. |
| US-06 | "What did we spend on travel this year?" | `GET /categories/{category_id}/transactions` or `GET /payees/{payee_id}/transactions` with `since_date` | A year of transactions is large. Aggregate before returning, and don't send raw rows to the model. |
| US-07 | "What scheduled bills are still coming this month?" | `GET /scheduled_transactions` | Filter to the rest of the month. Scheduled transactions repeat, so the next occurrence is what matters. |

## Should

The following table lists the stories that come after the first release:

| ID | Story | Endpoints | Design notes |
| --- | --- | --- | --- |
| US-08 | "What are we getting better at?" | `GET /months/{month}` across six or more months | The most expensive read in request count. A good test of caching and rate-limit handling. |
| US-09 | "Categorize our uncategorized transactions." | `GET /transactions?type=uncategorized`, `PATCH /transactions` | A batch write that needs one clear confirmation. Payee names and memos are untrusted text that ends up in the model's context. |
| US-10 | "Log $42 at the hardware store under Home Maintenance." | `POST /transactions` | Retrying must not create a duplicate, so set an `import_id`. The payee and category must resolve to existing ones, or the server asks. Needs confirmation before writing. |
| US-11 | "Split this receipt across Groceries and Household." | `PUT /transactions/{transaction_id}` with `subtransactions`, or `POST /transactions` for a new one | The host reads the receipt image, so the server receives a structured split. Split amounts must add up to the total. The API rejects changes to the splits on an existing split transaction, and doesn't allow splits on tracking accounts or on transfers between on-budget accounts. |
| US-12 | "Cover the Dining Out overspend from Fun Money." | `PATCH /months/{month}/categories/{category_id}`, once per category | Moving money takes two separate writes, and the API can't make them one atomic step. A failure between them leaves the plan half-changed. |

## Could

The following table lists the stories to consider once the core is solid:

| ID | Story | Endpoints | Design notes |
| --- | --- | --- | --- |
| US-13 | "Based on past months, what else should we expect this month?" | `GET /transactions` over several months, `GET /scheduled_transactions` | Finds recurring spending and income that isn't scheduled. Look for patterns on the server, because months of raw transactions don't fit in the model's context. |
| US-14 | "Run our monthly budget review." | Combines US-04, US-05, and US-09 | A user-triggered, repeatable workflow. That's a better fit for an MCP prompt than a tool. |
| US-15 | "Can we afford a $300 purchase this month?" | `GET /months/current` | The server supplies Ready to Assign (`to_be_budgeted`) and category balances. The model makes the judgment. |

## Won't

The following items are out of scope for now:

- **Use from Claude on the web or mobile.** That needs a remote HTTP server and OAuth.
- **Manage accounts, categories, or payees.** Creating or renaming them isn't a job anyone asked for.
- **Start a bank import.** `POST /transactions/import` changes the plan, which is a side effect a question like US-01 must not trigger.
