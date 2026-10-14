# API and MCP surface

The receptionist has two separate surfaces:

1. **The public widget API**, an HTTP API called by the chat widget on customers' websites. Origins are checked against each site's allowed domains, and requests go through a bot check and rate limits.
2. **The owner and agent tools**, which are `rx_*` tools exposed to the owner's AI assistant through the KissMySkills MCP connector. The connector authenticates the user and calls the receptionist over a Cloudflare service binding (RPC). None of these operations is reachable over public HTTP.

## Public widget API

| Method | Path | Purpose |
|---|---|---|
| GET | `/widget.js` | The embeddable chat widget (one script tag) |
| GET | `/api/config` | Widget settings and appearance for a site |
| POST | `/api/chat` | One visitor turn. Returns the reply and an optional action (lead form, booking card) |
| POST | `/api/lead` | Leave a name, contact and question for the team |
| GET | `/api/slots` | Free booking slots for a service |
| POST | `/api/book` | Book a slot |
| GET | `/api/messages` | Fetch replies written by the team (live replies) |
| POST | `/api/contact` | Leave an e-mail so a team reply can reach the visitor after they leave |
| GET | `/turnstile-frame` | Bot-check frame served from the receptionist's own host |
| GET | `/preview/:site` | Test page showing the widget as visitors will see it |
| GET | `/preview-frame/:site` | Live appearance preview, which only the KissMySkills app can embed |
| GET | `/avatar/:site/:hash` | Uploaded widget avatar |
| GET | `/api/health` | Health check |

## MCP tools (`rx_*`)

| Tool | Purpose |
|---|---|
| `rx_create_site` | Create a receptionist for a business website |
| `rx_update_site` | Change the name, allowed domains, widget settings and appearance |
| `rx_list_sites` | List sites with usage, answer count and new leads |
| `rx_import_site` | Fetch public website pages as text so the agent can write answers |
| `rx_autosetup` | One-click setup: read the website and draft Q/A pairs for approval |
| `rx_set_answers` | Add or update ready answers in bulk |
| `rx_list_answers` | List ready answers, filtered by text, tag or status |
| `rx_approve_answers` | Publish approved draft answers |
| `rx_list_requests` | The owner's to-do list: unanswered questions and requests for a person |
| `rx_update_request` | Mark a request as done or reopen it |
| `rx_test_chat` | Talk to the receptionist as a visitor, including draft answers |
| `rx_set_hours_and_slots` | Set opening hours, bookable services and slot rules |
| `rx_list_conversations` | Recent widget conversations with their status |
| `rx_get_conversation` | Full transcript, showing how each reply was produced |
| `rx_list_leads` | Leads and bookings waiting for the owner |
| `rx_reply_lead` | Record the owner's reply to a lead and update its status |
| `rx_unanswered_questions` | Grouped unanswered questions with the closest existing answer |
| `rx_get_embed_code` | The embed tag plus install guides for each platform |
| `rx_check_install` | Check that the widget is installed on a live page |
| `rx_find_by_contact` | GDPR lookup: everything held about one visitor's contact |
| `rx_delete_conversation` | Permanently delete one conversation (erasure request) |
| `rx_delete_lead` | Permanently delete one lead and its booking |
| `rx_delete_site` | Permanently delete a site and all its data |

## Internal RPC (service bindings)

| Direction | Purpose |
|---|---|
| MCP connector to receptionist | Tool discovery and tool calls for the `rx_*` family, plus relaying the owner's live replies to KissMySkills' own support chat |
| Receptionist to MCP connector | Owner plan lookup, owner e-mail alerts and activity events |
| Receptionist to AI CRM | Create a deal for each lead or booking, and a support card for requests that need a human |
