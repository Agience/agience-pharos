# Use cases — what the platform is for

Each page is one use case: who it is for, what they get, how the platform does it, what it uses that
exists today, what still has to be built, and how it is checked. People, organisations and data in
them are illustrative.

A use case is checked by a harness that plays it end to end on a deployed release, as the people and
agents in it would (`agience-cloud/e2e/usecases`, run by `platform_release staging check`). A step the
platform cannot do yet fails the harness, or is carried in it as a named expected failure, so a page's
"to build" list and its harness say the same thing.

| # | use case | checked |
|---|---|---|
| [01](01-email-that-surfaces-what-matters.md) | Email that surfaces what matters, with drafts for approval | not yet |
| [02](02-an-assistant-you-message.md) | An assistant you message from your phone | not yet |
| [03](03-meetings-into-notes-and-actions.md) | Meetings into notes, decisions and actions | not yet |
| [04](04-watching-the-business.md) | Watching the business: cash, pipeline and campaigns | not yet |
| [05](05-planning-an-event.md) | Planning an event with many people and dates | not yet |
| [07](07-a-clinical-practice-record.md) | A clinical practice's records, each reader seeing their part | not yet |
| [08](08-credit-for-contributions.md) | Credit for contributions in an open collaboration | not yet |
| [09](09-an-agent-on-a-key-you-can-revoke.md) | An agent works on a key you issue and can revoke | harness |
| [10](10-specs-that-emerge-from-the-work.md) | Specifications that emerge from the work | not yet |
| [11](11-reconciling-accounts-of-one-event.md) | Reconciling several accounts of one event | not yet |
| [12](12-a-shared-map-from-a-crowd.md) | A shared map from a crowd at a live event | not yet |
| [13](13-a-research-question-with-its-sources.md) | A research question answered with its sources | harness |
| [14](14-a-personal-tasting-notebook.md) | A personal tasting notebook that recommends | not yet |
| [15](15-sealed-logic-in-a-customers-install.md) | Proprietary logic delivered sealed into a customer's own install | not yet |
| [16](16-your-data-is-yours-alone.md) | Your data is reachable by you and those you grant, no one else | harness |

## Components several use cases need

Built once, used by many. Each is named on the pages that need it.

| component | what it is | needed by |
|---|---|---|
| **Timer** | **Built.** A Timer card (Lumen) runs an artifact's operation — a workflow, an operator — every N minutes, daily, or once, and records each run. Its owner arms it from the card, which issues the grant key it acts on; disarming revokes the key | 01, 02, 04, 05, 07, 09 |
| **Inbox** | **Built.** An Inbox item card (Lumen) holds what needs a person — an outlier, a failed routine, a draft to approve — with why and what it came from, moving `new → triaged → resolved`. An item may carry an action; approving runs it as the approver. A Timer raises a failed run into its owner's Inbox, and any workflow step can raise one | 01, 02, 04, 05 |
| **Inbound adapters** | Receiving mail, chat messages and form posts as artifacts. Iris sends mail; nothing receives it | 01, 02, 05, 08 |
| **Data connectors** | Read access to accounting, CRM, calendar and similar sources, on a key the person issues | 04, 05, 09 |
| **Context encryption** | Titles, names and other `context` fields encrypted under the content key (today they are plaintext, protected by grants: `design/architecture.md` §24) | 07, 15, 16 |
| **Transcription** | Speech to text for a stream, written into the stream's session | 03, 12 |
| **Reconciliation operator** | Compares several artifacts about one thing and writes what agrees, what conflicts and why | 08, 11, 12 |
| **History view** | **Built.** A card's History, in its window: what was added to and removed from what it holds (its commits), for anyone who can read it; and who reached it — a person, a key, or a persona acting for a person — what they did, and whether it was allowed, for those who administer it | 07, 08, 09, 10, 11 |
