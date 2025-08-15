<img src="https://i.imgur.com/qOlfX1V.png">
OsTicket Post‑Install Guide (Plain‑English Walkthrough)

This guide explains, in regular words and simple checklists, how to finish setting up osTicket after the web installer is done and you can sign in as an **Admin**. It avoids command lines and focuses on what to click, what to fill in, and what to double‑check.

---

Before you begin
- You can log in to the **Admin Panel** of osTicket.
- You know where osTicket is installed on your server (you may need this later, but you don’t need to run any commands now).
- You have the username and password for the mailbox you want osTicket to use (for example, support@yourcompany.com), including details like the mail server name and whether it uses security (SSL/TLS).

---

1) Installation
1. **Remove the installer folder:** In your hosting file manager (or ask your host), delete the folder named **setup** inside your osTicket directory. This prevents anyone from re‑running the installer.
2. **Make the configuration file read‑only:** Ask your host or use your control panel’s file permissions tool to set the file named **ost‑config.php** (inside the **include** folder) so it can’t be edited by the web server. This stops accidental changes.
3. **Use HTTPS:** Make sure your site address starts with **https://** so staff and customers connect securely. If you have a control panel (like cPanel, Plesk, or a managed cloud), turn on the SSL certificate there.

---

2) Set your basic system details
Go to **Admin Panel → Settings → System** and work through the page from top to bottom:
- **Helpdesk Name:** The name customers and staff will see.
- **Primary Language:** Choose the language your team will work in.
- **Default Department and Default Email:** Pick where new tickets go by default and which email osTicket uses as the sender.
- **Helpdesk URL:** Paste your full public site link (use https). This helps with links in emails.
- **Timezone and Date/Time Formats:** Match your organization’s location so due dates and reports make sense.
- Review the visibility options to control what customers and staff can see.

---

3) Set up sending email (outbound / SMTP)
1. Go to **Admin Panel → Emails → Emails**.
2. Add your support address if it isn’t listed yet.
3. Open that email entry and go to the **Outgoing** or **SMTP** section.
4. Enter your mail provider’s server name, the port, the security type (SSL/TLS), and the mailbox username and password.
5. Save, then use the built‑in **Send Test Email** to confirm messages can be sent.

**Tip:** If your provider offers an “app password” or “SMTP password,” use that instead of your normal mailbox password for better security.

---

4) Set up receiving email (inbound)
Choose one of these methods. Most teams start with IMAP.

Option A: IMAP/POP3 (recommended for most)
1. In **Admin Panel → Emails → Emails**, open your support email.
2. Go to the **Incoming** section and turn on **IMAP** (or POP3 if IMAP isn’t available).
3. Enter the mail server name, port, security type (SSL/TLS), and the mailbox username and password.
4. Save. If there’s a **Fetch Test**, run it to confirm.
5. Later, in the **Scheduler/Cron** step, enable the background task so osTicket checks for new mail automatically.

Option B: Email piping (advanced/host‑managed)
- If your mail server can forward messages directly into osTicket, set up a forwarder (often called a *pipe*) to the osTicket **pipe.php** script. This is typically done in your hosting control panel. If you’re unsure, stick with IMAP.

---

5) Build your team structure
Go to **Admin Panel → Agents** and set up how work should be routed.
- **Departments:** Create the main buckets for work (for example, Support, Billing, IT).
- **Teams:** Optional groups that can span departments, useful for escalations or special projects.
- **Roles:** Define what agents are allowed to do (for example, standard Agent, Supervisor, Admin). Roles control permissions.
- **Agents (staff):** Invite teammates, assign them to the correct department, role, and any teams they should belong to.

---

6) Define Help Topics and SLAs
- **Help Topics:** Go to **Admin Panel → Manage → Help Topics** and create clear categories customers can choose from (for example, Password Reset, Bug Report, Sales Question). This helps with routing and reporting.
- **SLA Plans:** Go to **Admin Panel → Manage → SLA** and create your response/resolve targets (for example, urgent issues due in 4 business hours; standard issues due in 24 business hours). Assign SLAs to the topics or departments that need them.

---

7) Tidy up email templates, auto‑replies, and alerts
1. **Templates:** In **Admin Panel → Emails → Templates**, duplicate the default template set so you have your own copy. Edit the wording to match your tone and include your branding.
2. **Auto‑Responses:** In **Admin Panel → Settings → Tickets → Auto‑response**, choose whether customers should get an automatic reply when they open a ticket or send a new message.
3. **Alerts & Notices:** In **Admin Panel → Settings → Alerts & Notices**, decide which staff receive notifications for new tickets, overdue tickets, reopened tickets, and so on. Make sure the right people get alerted without flooding inboxes.

---

8) Configure ticket behavior
Go to **Admin Panel → Manage** and **Settings → Tickets** as needed.
- **Fields & Forms:** Add or remove fields on the ticket form to capture the right information (for example, site location, device type, order number).
- **Priorities:** Define the priority levels your team understands (for example, P1, P2, P3) so reports are consistent.
- **Auto‑close and Overdue Rules:** Decide when inactive tickets should close and when tickets should be marked overdue.
- **Attachments:** Choose the maximum file size and file types customers can upload.

---

9) Create Knowledge Base articles and canned responses
- **Knowledge Base:** In the **Knowledgebase** section, create helpful articles. Organize them by category and choose whether each one is public (visible on the customer portal) or internal (for agents only).
- **Canned Responses:** Still in **Knowledgebase**, create short, reusable replies for frequently asked questions. Agents can insert these to answer faster and more consistently.

---

10) Add branding and your company profile
- Go to **Admin Panel → Settings → Company** to set your company name, address, and upload a logo.
- In **Admin Panel → Pages**, update the welcome text on the customer portal so it uses your voice and links to important resources (for example, status page, policy page).

---

11) Enable plugins and sign‑in options (optional)
- Put any plugins your host supports into the osTicket plugins area (your host can help with this if you don’t have direct file access).
- In **Admin Panel → Manage → Plugins**, turn on what you need, such as:
  - **LDAP/Active Directory** for staff to sign in with their work accounts.
  - **reCAPTCHA** to reduce spam on public forms.
  - Diagnostics or logging plugins if recommended by your host.

---

12) Turn on the background scheduler (Cron)
- osTicket needs a background task to fetch email and handle things like overdue checks. If your host provides a “Scheduled Tasks” or “Cron Jobs” screen, create a task that runs the osTicket scheduler frequently (every few minutes is common). If you don’t see this option, ask your host to enable it for your site.
- As a fallback, you can enable **Auto‑Cron** in **Admin Panel → Settings → System**. This runs tasks when staff use the site, but it’s less reliable for regular email fetching.

---

13) Plan for backups and routine maintenance
- **Database:** Ask your host to include your osTicket database in nightly backups, and confirm how to restore it if needed.
- **Attachments/Uploads:** Ensure the folder that stores file uploads is also backed up on a schedule.
- **Test a restore:** Periodically perform a small test restore so you know your backups actually work.
- **Health checks:** In **Admin Panel → Dashboard → System Logs**, review warnings or errors and address them promptly.

---

14) Test everything before you go live
Use this quick checklist to make sure the basics work end‑to‑end:
- Open a ticket from the customer portal and confirm the correct department is notified.
- Send an email to your support address and confirm a ticket is created and the customer receives an acknowledgment.
- Reply from the agent side and confirm the customer receives the reply by email.
- Transfer a ticket between departments or teams and make sure the right people are notified and the SLA still makes sense.
- Close a ticket and, if you use them, confirm any satisfaction survey or closure message is sent.
- Look at the **System Logs** to ensure there are no errors related to email sending, email fetching, or permissions.

---

Appendix: helpful notes (no command line required)
- **File permissions:** If you’re on shared hosting or a managed platform, your provider usually sets this up for you. If you see errors about not being able to write files, contact support and mention the osTicket folder and the **ost‑config.php** file.
- **Security and firewalls:** If email sending or fetching fails, your host may need to allow connections to your email provider. Share the mail server name and port with them so they can verify.
- **PHP limits:** If your users upload larger files, ask your host to raise the maximum upload size for your site (for example, allow attachments up to 20 MB).

---

You’re done!
At this point, osTicket should be ready for daily use. Keep your templates and knowledge base fresh, review reports regularly, and adjust SLAs and alerts as you learn how your team works best.

