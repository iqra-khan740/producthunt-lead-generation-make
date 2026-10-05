# ProductHunt Lead Generation Automation (Make)

An automated lead-generation pipeline built in **Make**. It watches Product Hunt for new launches, finds each company's contact email, uses AI to prepare the lead, and files it in **Airtable** while notifying the team on **Slack**. Companies with no contact email go to a separate **Cold Leads** list.

**Stack:** Make · Airtable · Slack · Make AI Toolkit · HTTP / RSS · Text parser

**🔗 Live scenario:** [ProductHunt Lead Generation on Make](https://us2.make.com/public/shared-scenario/XJ0lsI3PgAW/product-hunt-lead-generation)

![Make scenario](https://github.com/iqra-khan740/producthunt-lead-generation-make/blob/main/make-scenario.png)

## How it works

| Step | Module | What it does |
|------|--------|--------------|
| 1 | **RSS: Watch RSS feed items** | Runs **daily at 11:00** and picks up new Product Hunt launches |
| 2 | **HTTP: Make a request (GET)** | Fetches the launch page |
| 3 | **Text parser: Match pattern** | Extracts the needed data from the page with a pattern match |
| 4 | **Tools: Set variable** | Stores the extracted value for later steps |
| 5 | **HTTP: Make a request (GET)** | Visits the company's website to look for contact details |
| 6 | **Router** | Splits the flow: **Emails found** or fallback **Emails not found** |
| 7a | **Make AI Toolkit: Simple Text Prompt** | *(Emails found)* AI prepares the lead's overview and message |
| 8a | **Airtable: Create a record** | *(Emails found)* Saves the qualified lead in the **Leads** table |
| 9a | **Slack: Send a message** | *(Emails found)* Posts "New Qualified Lead from Product Hunt" with the company link |
| 7b | **Airtable: Create a record** | *(Emails not found)* Saves the company in the **Cold Leads** table |

## Airtable (base: *AI Client CRM*)

| Table | Purpose | Main fields |
|-------|---------|-------------|
| **Leads** | Qualified leads with a contact email | Company name, Overview, URL, Status, Message, Contact Email |
| **Cold Leads** | Companies where no email was found | Company name, Overview, URL |
| **Clients** | Converted clients (used by the [n8n client onboarding system](https://github.com/iqra-khan740/N8N_AI_Client_Acquisition_Onboarding_Challenge)) | n/a |

![Leads table](https://github.com/iqra-khan740/producthunt-lead-generation-make/blob/main/airtable-leads.JPG)

![Cold Leads table](https://github.com/iqra-khan740/producthunt-lead-generation-make/blob/main/airtable-cold-leads.jpeg)

## Slack notification

Each qualified lead is posted to a Slack channel, so the team can follow up straight away.

![Slack notifications](https://github.com/iqra-khan740/producthunt-lead-generation-make/blob/main/slack-notifications.jpeg)

## Setup

1. Open the [shared scenario](https://us2.make.com/public/shared-scenario/XJ0lsI3PgAW/product-hunt-lead-generation) and add it to your own Make account (or import a blueprint JSON through the Scenario menu → *Import Blueprint*).
2. Connect your **Airtable**, **Slack** and **Make AI Toolkit** accounts.
3. In the Airtable modules, choose your own base and the **Leads** / **Cold Leads** tables.
4. Set the RSS module's feed URL to the Product Hunt feed and choose the run schedule (the original runs daily at 11:00).
5. In the Slack module, pick the channel for notifications.
6. Run once, check the Airtable rows and the Slack message, then switch the scenario on.

## Notes

- Leads are routed by whether a contact email was found: it separates people you can reach now from companies to research later.
- The same Airtable base feeds the follow-up pipeline in the n8n project, so qualified leads can move into the client onboarding flow.
