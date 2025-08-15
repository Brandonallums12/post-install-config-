<img src="https://i.imgur.com/qOlfX1V.png">
# osTicket Post‑Install Step‑by‑Step Guide

Use this opinionated checklist to finish setting up a fresh osTicket instance after you’ve completed the web installer and can log in as **Admin**. The steps are safe defaults and work for most small/medium helpdesks.

## Table of Contents
1. Before you begin
2. Harden and tidy the install
3. System settings
4. Outbound email (SMTP)
5. Inbound email (IMAP/POP3 or Pipe)
6. Departments, teams, roles & agents
7. Help topics & SLAs
8. Templates, auto‑replies & alerts
9. Ticket configuration
10. Knowledge base & canned responses
11. Branding & company profile
12. Plugins & SSO
13. Scheduler / Cron
14. Backups & maintenance
15. Test & go‑live checklist
16. Appendix: common commands

---

## Before you begin
- Web installer finished and you can sign into **Admin Panel**.
- Know your osTicket path (e.g., `/var/www/osticket` or `C:\inetpub\wwwroot\osTicket`).
- Have SMTP/IMAP credentials for your helpdesk mailbox.

## 1) Harden and tidy the install
**Remove the installer:**
```bash
# Linux
sudo rm -rf /var/www/osticket/setup

# Windows (PowerShell)
Remove-Item -Recurse -Force C:\inetpub\wwwroot\osTicket\setup
```
**Lock the config file:**
```bash
# Linux
sudo chmod 0444 /var/www/osticket/include/ost-config.php
sudo chown -R www-data:www-data /var/www/osticket

# Windows
icacls C:\inetpub\wwwroot\osTicket\include\ost-config.php /inheritance:r
icacls C:\inetpub\wwwroot\osTicket\include\ost-config.php /grant:r Administrators:R, IIS_IUSRS:R
```
Enable **HTTPS**.

## 2) System settings
Admin Panel → **Settings → System**
- Helpdesk Name, Primary Language
- Default Department, Default Email
- Helpdesk URL (public HTTPS)
- Timezone, Date/Time formats
- Public/Staff visibility options

## 3) Outbound email (SMTP)
- Create/verify helpdesk mailbox (e.g., `support@example.com`).
- Add new email in **Admin Panel → Emails**.
- Configure SMTP in the **Outgoing** tab.
- Send Test Email.

## 4) Inbound email
**IMAP/POP3 polling:** Configure **Incoming** tab with host, port, encryption, and credentials. Enable cron in Step 12.

**Email piping:**
```bash
# Postfix
support: "|/usr/bin/php -q /var/www/osticket/scp/pipe.php"
```

## 5) Departments, teams, roles & agents
- Create departments like Support, Billing, IT.
- Create teams for cross-department tasks.
- Define roles (Agent, Supervisor, Admin).
- Invite agents and assign them.

## 6) Help topics & SLAs
- Create Help Topics.
- Define SLA plans.
- Map topics to departments/SLAs.

## 7) Templates, auto‑replies & alerts
- Clone and edit templates.
- Enable auto-responses.
- Configure alerts & notices.

## 8) Ticket configuration
- Add custom fields/forms.
- Set priorities.
- Configure auto-close & overdue.
- Set attachment limits.

## 9) Knowledge base & canned responses
- Create KB categories/articles.
- Add canned responses.

## 10) Branding & company profile
- Set company info and logo.
- Customize client portal welcome text.

## 11) Plugins & SSO
- Install plugins in `/include/plugins`.
- Enable via **Admin Panel → Manage → Plugins**.

## 12) Scheduler / Cron
**Linux:**
```bash
*/5 * * * * /usr/bin/php /var/www/osticket/api/cron.php >/dev/null 2>&1
```
**Windows Task Scheduler:**
```
Program/script: C:\php\php.exe
Arguments: C:\inetpub\wwwroot\osTicket\api\cron.php
```

## 13) Backups & maintenance
**Database:**
```bash
mysqldump -u osticket -p osticket_db | gzip > /backups/osticket-$(date +%F).sql.gz
```
**Files:**
```bash
rsync -a --delete /var/www/osticket/ /backups/osticket-files/
```

## 14) Test & go‑live checklist
- Create ticket via web form.
- Email test.
- Reply test.
- Escalation test.
- Close ticket test.
- Check logs.

## Appendix: common commands
**Linux permissions:**
```bash
sudo chown -R www-data:www-data /var/www/osticket
sudo find /var/www/osticket -type d -exec chmod 0755 {} \;
sudo find /var/www/osticket -type f -exec chmod 0644 {} \;
sudo chmod 0444 /var/www/osticket/include/ost-config.php
```
**PHP tuning:**
```
memory_limit = 256M
upload_max_filesize = 20M
post_max_size = 25M
max_execution_time = 60
```
