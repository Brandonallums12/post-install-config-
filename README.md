<img src="https://i.imgur.com/qOlfX1V.png">
OsTicket Post‑Install Guide

This guide explains, in regular words and simple checklists, how to finish setting up osTicket after the web installer is done and you can sign in as an **Admin**. It avoids command lines and focuses on what to click, what to fill in, and what to double‑check.

---
<img src="https://i.imgur.com/GHWi9au.png">
**Installation**

1. **Remove the installer folder:** In your hosting file manager (or ask your host), delete the folder named **setup** inside your osTicket directory. This prevents anyone from rerunning the installer.
2. **Make the configuration file read‑only:** Ask your host or use your control panel’s file permissions tool to set the file named **ost‑config.php** (inside the **include** folder) so it can’t be edited by the web server. This stops accidental changes.
3. **Use HTTPS:** Make sure your site address starts with **https://** so staff and customers connect securely. If you have a control panel (like cPanel, Plesk, or a managed cloud), turn on the SSL certificate there.

---
<img src="https://i.imgur.com/1PoeSXm.png">

**Set your basic system details**
   
Go to **Admin Panel → Settings → System** and work through the page from top to bottom:
- **Helpdesk Name:** The name customers and staff will see.
- **Primary Language:** Choose the language your team will work in.
- **Default Department and Default Email:** Pick where new tickets go by default and which email osTicket uses as the sender.
- **Helpdesk URL:** Paste your full public site link (use https). This helps with links in emails.
- **Timezone and Date/Time Formats:** Match your organization’s location so due dates and reports make sense.
- Review the visibility options to control what customers and staff can see.

---
<img src="https://i.imgur.com/Ibykmmf.png">

**Set up sending email (outbound / SMTP)**
1. Go to **Admin Panel → Emails → Emails**.
2. Add your support address if it isn’t listed yet.
3. Open that email entry and go to the **Outgoing** or **SMTP** section.
4. Enter your mail provider’s server name, the port, the security type (SSL/TLS), and the mailbox username and password.
5. Save, then use the built‑in **Send Test Email** to confirm messages can be sent.

**Tip:** If your provider offers an “app password” or “SMTP password,” use that instead of your normal mailbox password for better security.

---
<img src="https://i.imgur.com/qOlfX1V.png">

**Set up receiving email**

1. In **Admin Panel → Emails → Emails**, open your support email.
2. Go to the **Incoming** section and turn on **IMAP**.
3. Enter the mail server name, port, security type (SSL/TLS), and the mailbox username and password.
4. Save.
5. Later, in the **Scheduler** step, enable the background task so osTicket checks for new mail automatically.

---
<img src="https://i.imgur.com/qOlfX1V.png">

**Build your team structure**
Go to **Admin Panel → Agents** and set up how work should be routed.
- **Departments:** Create the main buckets for work (for example, Support, Billing, IT).
- **Teams:** Optional groups that can span departments, useful for escalations or special projects.
- **Roles:** Define what agents are allowed to do (for example, standard Agent, Supervisor, Admin). Roles control permissions.
- **Agents (staff):** Invite teammates, assign them to the correct department, role, and any teams they should belong to.

---
<img src="https://i.imgur.com/qOlfX1V.png">

**Define Help Topics and SLAs**
- **Help Topics:** Go to **Admin Panel → Manage → Help Topics** and create clear categories customers can choose from (for example, Password Reset, Bug Report, Sales Question). This helps with routing and reporting.
- **SLA Plans:** Go to **Admin Panel → Manage → SLA** and create your response/resolve targets (for example, urgent issues due in 4 business hours; standard issues due in 24 business hours). Assign SLAs to the topics or departments that need them.

---
<img src="https://i.imgur.com/qOlfX1V.png">

**Tidy up email templates, auto‑replies, and alerts**
1. **Templates:** In **Admin Panel → Emails → Templates**, duplicate the default template set so you have your own copy. Edit the wording to match your tone and include your branding.
2. **Auto‑Responses:** In **Admin Panel → Settings → Tickets → Auto‑response**, choose whether customers should get an automatic reply when they open a ticket or send a new message.
3. **Alerts & Notices:** In **Admin Panel → Settings → Alerts & Notices**, decide which staff receive notifications for new tickets, overdue tickets, reopened tickets, and so on. Make sure the right people get alerted without flooding inboxes.

---
<img src="https://i.imgur.com/qOlfX1V.png">

**Turn on the background scheduler**
- osTicket needs a background task to fetch email and handle things like overdue checks. If your host provides a “Scheduled Tasks” or “Cron Jobs” screen, create a task that runs the osTicket scheduler frequently (every few minutes is common). If you don’t see this option, ask your host to enable it for your site.
- As a fallback, you can enable **Auto‑Cron** in **Admin Panel → Settings → System**. This runs tasks when staff use the site, but it’s less reliable for regular email fetching.

---

You’re done!
At this point, osTicket should be ready for daily use. Keep your templates and knowledge base fresh, review reports regularly, and adjust SLAs and alerts as you learn how your team works best.

