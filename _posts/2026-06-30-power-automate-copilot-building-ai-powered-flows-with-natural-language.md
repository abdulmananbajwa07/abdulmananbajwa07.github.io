---
layout: post
title: "Power Automate + Copilot: Building AI-Powered Flows with Natural Language"
date: 2026-06-30 09:00:00 +1000
category: Power Automate
icon: "🔄"
description: "I'll be honest — when I first started building Power Automate flows years ago, I spent a lot of time in the designer dragging connectors around, hunting for the"
---

---

I'll be honest — when I first started building Power Automate flows years ago, I spent a lot of time in the designer dragging connectors around, hunting for the right action, and second-guessing trigger conditions. It worked, but it wasn't exactly *fast*. Then Copilot came to Power Automate, and everything changed. These days, I can describe what I want in plain English and have a working flow scaffold in under two minutes. If you haven't tried this yet, this post is for you.

## What Is Copilot in Power Automate, Really?

Copilot in Power Automate is an AI assistant built directly into the flow designer. You describe your automation goal in natural language — "Send me a Teams notification when a new item is added to a SharePoint list" — and Copilot generates the flow structure, pre-fills connector actions, and even suggests improvements. It's powered by Azure OpenAI under the hood, and since we're staying inside the Microsoft 365 ecosystem, your data doesn't leave your tenant boundary.

But it goes deeper than just flow creation. Copilot can also:

- **Edit existing flows** — describe the change you want and it updates the actions
- **Explain flows** — ask "what does this flow do?" and get a plain-English summary
- **Troubleshoot errors** — paste an error message and Copilot suggests fixes
- **Generate expressions** — describe the logic you need and it writes the formula

For anyone who has ever stared at a cryptic `formatDateTime` expression at 4pm on a Friday, that last point alone is worth the price of admission.

## Getting Started: Your First AI-Built Flow

Let me walk you through creating a real flow using Copilot. We'll build something practical: a flow that monitors a SharePoint document library for new files, extracts key information using AI Builder, and posts a summary to a Teams channel.

**Step 1 — Open the Copilot pane**

In Power Automate (make.powerautomate.com), click **Create** → **Describe it to make it**. You'll see a text prompt.

**Step 2 — Describe your automation**

Type something like:

> *"When a new file is uploaded to a SharePoint library called 'Contracts', use AI Builder to extract the document summary, then post the summary to a Teams channel called 'Legal Updates'."*

Copilot generates a flow with four actions: a SharePoint trigger, an AI Builder "Extract information from documents" action, and a Teams "Post a message" action. It even maps the dynamic content fields automatically.

**Step 3 — Review and refine**

This is important: always review what Copilot built. It gets the structure right about 90% of the time, but you'll often need to update connection references, tweak field mappings, or adjust the AI Builder model selection. I treat Copilot's output as a great first draft, not a finished product.

**Step 4 — Test it**

Upload a test file to your SharePoint library and watch the flow run. Power Automate's run history gives you detailed input/output at each step — invaluable for debugging.

## A Real-World Example From My Work

A few months ago, I helped a professional services firm automate their client onboarding process. Every time a new client folder was created in SharePoint, someone had to manually:

1. Create a Planner task for the account manager
2. Send a welcome email from a template
3. Log the onboarding date in a Teams channel

Three manual steps, every single time, often forgotten or delayed. Using Copilot in Power Automate, I described the process and had a working flow in about 15 minutes. I prompted Copilot with:

> *"When a new folder is created in SharePoint under 'Clients', create a Planner task assigned to the folder creator, send an email using an Outlook template, and post a notification to the 'Onboarding' Teams channel."*

Copilot got the trigger and the three actions right. I spent the remaining time refining the Planner task title (using dynamic content from the folder name) and testing the Outlook template logic. The firm now saves roughly 20 minutes per new client — which adds up fast when you're onboarding 30+ clients a month.

## Tips for Getting the Best Results from Copilot

After building dozens of flows with Copilot assistance, here's what I've learned:

**Be specific about your connectors.** Instead of "send an email," say "send an Outlook email." Instead of "save to storage," say "save to SharePoint." Copilot uses your connector names to wire things up correctly.

**Describe outputs, not steps.** Tell Copilot what you want to *happen*, not how to implement it. "Notify the team when a high-priority ticket is created" works better than "add an HTTP request action that calls the Teams webhook."

**Use it to write expressions.** Next time you need a complex formula, try: "Write a Power Automate expression to get the last day of the current month." Copilot handles this far better than most people expect.

**Iterate in conversation.** The Copilot pane keeps context. If the first flow isn't quite right, say "Add an approval step before the Teams notification" and it amends the existing flow — you don't start over.

## What Copilot Can't Do (Yet)

Let's be balanced. Copilot in Power Automate still has limitations:

- It can't build highly complex branching logic reliably in one shot — break those into smaller flows
- It sometimes picks the wrong connector action (e.g., "Send an email" vs "Send an email from a shared mailbox")
- It doesn't yet have deep awareness of your existing environment — it doesn't know your SharePoint site names or Planner board IDs, so you'll need to configure those manually

None of these are dealbreakers, just things to keep in mind as you collaborate with AI rather than hand off to it blindly.

---

## Key Takeaways

- **Copilot in Power Automate** lets you generate, edit, explain, and troubleshoot flows using plain English — no more blank-canvas paralysis.
- **The best prompt strategy** is to describe the *outcome* you want (with specific connector names), then refine the generated flow rather than accepting it wholesale.
- **AI Builder + Power Automate + Copilot** is a powerful trio for document processing, content extraction, and intelligent notifications.
- **Real productivity gains** come from automating repetitive multi-step manual processes — even simple 3-step flows save meaningful time at scale.
- **Copilot is a collaborator**, not a replacement for understanding your flows. Always review, test, and own what you ship.

---

If you're already using Power Automate, I'd encourage you to open the Copilot pane today and describe your most tedious manual process. You might be surprised how close it gets on the first try.

And if you're new to Power Automate entirely, this is genuinely one of the most accessible entry points into the Microsoft AI ecosystem — no coding required, and the iteration loop is fast and forgiving.

Have questions or want to discuss this further? Connect with me on LinkedIn or leave a comment below.

---
