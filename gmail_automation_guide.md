# Gmail Automation Quick Start

This guide shows a beginner-friendly automation using Google Apps Script.

## What you'll build
- Label incoming emails from a sender (example: invoices@company.com)
- Mark matching emails as important
- Send yourself a summary every day

## Steps

1. Open [script.google.com](https://script.google.com) and create a new project.
2. Replace the default code with the script below.
3. Update `TARGET_SENDER` and `SUMMARY_RECIPIENT`.
4. Click **Run** once to authorize.
5. Create a trigger:
   - In Apps Script, open **Triggers** (clock icon)
   - Add trigger for `processInbox`
   - Event source: **Time-driven**
   - Frequency: every 1 hour (or as you prefer)

## Script

```javascript
const TARGET_SENDER = 'invoices@company.com';
const LABEL_NAME = 'Auto/Invoices';
const SUMMARY_RECIPIENT = 'your-email@gmail.com';

function processInbox() {
  const label = GmailApp.getUserLabelByName(LABEL_NAME) || GmailApp.createLabel(LABEL_NAME);
  const query = `from:${TARGET_SENDER} is:inbox -label:${LABEL_NAME}`;
  const threads = GmailApp.search(query, 0, 50);

  if (!threads.length) return;

  threads.forEach(thread => {
    thread.addLabel(label);
    thread.markImportant();
  });
}

function dailySummary() {
  const label = GmailApp.getUserLabelByName(LABEL_NAME);
  if (!label) return;

  const threads = label.getThreads(0, 20);
  const lines = threads.map(t => {
    const msg = t.getMessages()[0];
    return `- ${msg.getDate()} | ${msg.getFrom()} | ${msg.getSubject()}`;
  });

  const body = lines.length
    ? `Recent labeled emails:\n\n${lines.join('\n')}`
    : 'No labeled emails found today.';

  GmailApp.sendEmail(SUMMARY_RECIPIENT, 'Daily Gmail Automation Summary', body);
}
```

## Useful next automations
- Auto-reply to common questions with templates.
- Save attachments to Google Drive.
- Send Slack/Discord notifications for urgent emails.
- Route receipts/invoices to Google Sheets.

## Safety notes
- Avoid giving scripts broad access unless needed.
- Use clear labels (e.g., `Auto/...`) so you can review actions.
- Start with read-only/reporting automations before destructive actions.
