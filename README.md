<!DOCTYPE html>

    <h1>osTicket Post‑Install Step‑by‑Step Guide</h1>
    <p class="lead">Use this opinionated checklist to finish setting up a fresh osTicket instance after you’ve completed the web installer and can log in as <em>Admin</em>. The steps are safe defaults and work for most small/medium helpdesks.</p>

    <div class="toc">
      <strong>Table of Contents</strong>
      <ol>
        <li><a href="#before-you-begin">Before you begin</a></li>
        <li><a href="#1-harden">1) Harden and tidy the install</a></li>
        <li><a href="#2-system-settings">2) System settings</a></li>
        <li><a href="#3-email-outbound">3) Outbound email (SMTP)</a></li>
        <li><a href="#4-email-inbound">4) Inbound email (IMAP/POP3 or Pipe)</a></li>
        <li><a href="#5-structure">5) Departments, teams, roles & agents</a></li>
        <li><a href="#6-topics-sla">6) Help topics & SLAs</a></li>
        <li><a href="#7-templates-notifs">7) Templates, auto‑replies & alerts</a></li>
        <li><a href="#8-ticket-config">8) Ticket configuration</a></li>
        <li><a href="#9-kb-canned">9) Knowledge base & canned responses</a></li>
        <li><a href="#10-branding">10) Branding & company profile</a></li>
        <li><a href="#11-plugins">11) Plugins & SSO</a></li>
        <li><a href="#12-cron">12) Scheduler / Cron</a></li>
        <li><a href="#13-backups">13) Backups & maintenance</a></li>
        <li><a href="#14-test">14) Test & go‑live checklist</a></li>
        <li><a href="#appendix">Appendix: common commands</a></li>
      </ol>
    </div>

    <section id="before-you-begin" class="card">
      <h2>Before you begin</h2>
      <ul>
        <li>You’ve finished the web installer and can sign into <kbd>Admin Panel</kbd>.</li>
        <li>You know your server path to osTicket (e.g., <code>/var/www/osticket</code> or <code>C:\\inetpub\\wwwroot\\osTicket</code>).</li>
        <li>You have SMTP/IMAP credentials for your helpdesk mailbox (e.g., <code>support@example.com</code>).</li>
      </ul>
    </section>

    <section id="1-harden">
      <h2>1) Harden and tidy the install</h2>
      <ol>
        <li><strong>Remove the installer</strong> (required)
          <pre><code># Linux
sudo rm -rf /var/www/osticket/setup

# Windows (PowerShell)
Remove-Item -Recurse -Force C:\inetpub\wwwroot\osTicket\setup</code></pre>
        </li>
        <li><strong>Lock the config file</strong> (read‑only)
          <pre><code># Linux
sudo chmod 0444 /var/www/osticket/include/ost-config.php
sudo chown -R www-data:www-data /var/www/osticket

# Windows (PowerShell, remove write for non‑admins)
icacls C:\inetpub\wwwroot\osTicket\include\ost-config.php /inheritance:r
icacls C:\inetpub\wwwroot\osTicket\include\ost-config.php /grant:r Administrators:R, IIS_IUSRS:R</code></pre>
        </li>
        <li>Ensure the site is served over <strong>HTTPS</strong> and that the <em>base URL</em> will be HTTPS.</li>
      </ol>
    </section>

    <section id="2-system-settings">
      <h2>2) System settings</h2>
      <p>In <kbd>Admin Panel → Settings → System</kbd>:</p>
      <ul>
        <li><strong>Helpdesk Name</strong> and <strong>Primary Language</strong></li>
        <li><strong>Default Department</strong>, <strong>Default Email</strong></li>
        <li><strong>Helpdesk URL</strong> (set to your public HTTPS URL)</li>
        <li><strong>Default Timezone</strong>, <strong>Date/Time formats</strong></li>
        <li><strong>Public/Staff visibility</strong> options as needed</li>
      </ul>
    </section>

    <section id="3-email-outbound">
      <h2>3) Outbound email (SMTP)</h2>
      <ol>
        <li>Create/verify the helpdesk mailbox (e.g., <code>support@example.com</code>).</li>
        <li>In <kbd>Admin Panel → Emails → Emails → Add New Email</kbd>, add the address.</li>
        <li>Open that email entry → <kbd>Outgoing</kbd> tab → set <strong>SMTP</strong> host, port, encryption and credentials.</li>
        <li>Click <em>Save Changes</em>, then use <em>Send Test Email</em> to verify.</li>
      </ol>
      <div class="card small">
        <strong>Tip:</strong> If your provider supports app‑passwords, use one and restrict SMTP to that app password.
      </div>
    </section>

    <section id="4-email-inbound">
      <h2>4) Inbound email (IMAP/POP3 or Pipe)</h2>
      <h3>A) IMAP/POP3 polling</h3>
      <ol>
        <li>In <kbd>Admin Panel → Emails → Emails</kbd>, open your helpdesk email → <kbd>Incoming</kbd> tab.</li>
        <li>Enable <strong>IMAP</strong> (recommended) or POP3 and enter host, port, encryption and credentials.</li>
        <li>Save and use <em>Fetch Test</em> if available.</li>
        <li>Set up the scheduler/cron in <a href="#12-cron">Step 12</a> to fetch mail automatically.</li>
      </ol>
      <h3>B) Email piping (via MTA)</h3>
      <p>Alternatively, pipe mail directly from your mail server/MTA:</p>
      <pre><code># Postfix (aliases)
support: "|/usr/bin/php -q /var/www/osticket/scp/pipe.php"
newaliases && sudo systemctl reload postfix

# Exim (system filter example)
# Route messages for support@ to pipe.php according to your Exim docs

# cPanel (Forwarders)
# Forward support@ to: |/usr/bin/php -q /home/USER/public_html/osTicket/scp/pipe.php</code></pre>
      <p class="small">Make sure <code>pipe.php</code> path matches your install and that the web user has execute permission.</p>
    </section>

    <section id="5-structure">
      <h2>5) Departments, teams, roles & agents</h2>
      <ol>
        <li><strong>Departments</strong>: <kbd>Admin Panel → Agents → Departments</kbd> → create units like <em>Support</em>, <em>Billing</em>, <em>IT</em>.</li>
        <li><strong>Teams</strong>: Cross‑department groups for escalations (optional).</li>
        <li><strong>Roles</strong>: Define permissions per role (e.g., <em>Agent</em>, <em>Supervisor</em>, <em>Admin</em>).</li>
        <li><strong>Agents</strong>: Invite staff via <kbd>Agents → Add New</kbd> and assign department, role, and team.</li>
      </ol>
    </section>

    <section id="6-topics-sla">
      <h2>6) Help topics & SLAs</h2>
      <ol>
        <li><strong>Help Topics</strong>: <kbd>Admin Panel → Manage → Help Topics</kbd> → create categories like <em>Password Reset</em>, <em>Bug Report</em>, <em>Sales</em>.</li>
        <li><strong>SLAs</strong>: <kbd>Admin Panel → Manage → SLA</kbd> → define response/resolve targets (e.g., <em>P1: 4h/1d</em>, <em>P3: 24h/3d</em>).</li>
        <li>Map topics → departments/SLAs where helpful.</li>
      </ol>
    </section>

    <section id="7-templates-notifs">
      <h2>7) Templates, auto‑replies & alerts</h2>
      <ol>
        <li><strong>Templates</strong>: <kbd>Admin Panel → Emails → Templates</kbd> → clone the default set and customize wording/branding.</li>
        <li><strong>Auto‑Responses</strong>: <kbd>Admin Panel → Settings → Tickets → Auto‑response</kbd> → enable for new tickets, new messages, and online forms.
        </li>
        <li><strong>Alerts & Notices</strong>: <kbd>Admin Panel → Settings → Alerts & Notices</kbd> → choose agent/department notifications for new/reopened/overdue tickets.</li>
      </ol>
    </section>

    <section id="8-ticket-config">
      <h2>8) Ticket configuration</h2>
      <ul>
        <li><strong>Fields & Forms</strong>: <kbd>Admin Panel → Manage → Forms</kbd> → add custom fields to the <em>Ticket Details</em> form.</li>
        <li><strong>Priorities</strong>: <kbd>Admin Panel → Manage → List</kbd> → set up Priority list values (P1, P2, etc.).</li>
        <li><strong>Auto‑close & Overdue</strong>: <kbd>Admin Panel → Settings → Tickets</kbd> → choose grace periods and overdue rules.</li>
        <li><strong>Attachment limits</strong>: <kbd>Admin Panel → Settings → Tickets → Attachments</kbd> → size/type limits.</li>
      </ul>
    </section>

    <section id="9-kb-canned">
      <h2>9) Knowledge base & canned responses</h2>
      <ol>
        <li><strong>KB</strong>: <kbd>Knowledgebase → Categories</kbd> → create categories and articles; publish publicly if desired.</li>
        <li><strong>Canned Responses</strong>: <kbd>Knowledgebase → Canned Responses</kbd> → snippets for common replies; map to departments.</li>
      </ol>
    </section>

    <section id="10-branding">
      <h2>10) Branding & company profile</h2>
      <ul>
        <li><kbd>Admin Panel → Settings → Company</kbd> → set company name, address, and upload logo.</li>
        <li>Customize the client portal welcome text at <kbd>Admin Panel → Pages</kbd>.</li>
      </ul>
    </section>

    <section id="11-plugins">
      <h2>11) Plugins & SSO</h2>
      <p>Install plugins (e.g., LDAP/AD, email fetchers, auth) by placing them in <code>/include/plugins</code>, then enable in <kbd>Admin Panel → Manage → Plugins</kbd>.</p>
      <ul>
        <li>LDAP/AD for domain logins</li>
        <li>reCAPTCHA for public forms</li>
        <li>Diagnostics/logging helpers</li>
      </ul>
    </section>

    <section id="12-cron">
      <h2>12) Scheduler / Cron</h2>
      <p>Automate background tasks (mail fetch, overdue checks). Pick one method:</p>
      <h3>A) System cron (recommended)</h3>
      <pre><code># Linux (run every 5 minutes)
*/5 * * * * /usr/bin/php -d detect_unicode=0 /var/www/osticket/api/cron.php >/dev/null 2>&1

# Alternative CLI path example
*/5 * * * * /usr/bin/php8.2 /var/www/osticket/api/cron.php >/dev/null 2>&1

# Windows Task Scheduler (Action)
Program/script: C:\\php\\php.exe
Add arguments: C:\\inetpub\\wwwroot\\osTicket\\api\\cron.php</code></pre>
      <h3>B) Auto‑cron (fallback)</h3>
      <p class="small">Enable <em>Auto‑Cron</em> in <kbd>Admin Panel → Settings → System</kbd>. Tasks run when staff use the site (not ideal for reliable mail fetch).</p>
    </section>

    <section id="13-backups">
      <h2>13) Backups & maintenance</h2>
      <div class="grid grid-2">
        <div class="card">
          <h3>Database (MySQL/MariaDB)</h3>
          <pre><code># Full dump (UTF‑8)
mysqldump -u osticket -p --single-transaction --routines --triggers --default-character-set=utf8mb4 osticket_db \
  | gzip > /backups/osticket-$(date +%F).sql.gz</code></pre>
        </div>
        <div class="card">
          <h3>Attachments & assets</h3>
          <pre><code># Files (adjust path)
rsync -a --delete /var/www/osticket/ /backups/osticket-files/</code></pre>
        </div>
      </div>
      <ul>
        <li>Test restores periodically (database + <code>/attachments</code> and custom files).</li>
        <li>Monitor <kbd>Admin Panel → Dashboard → System Logs</kbd> for warnings/errors.</li>
      </ul>
    </section>

    <section id="14-test">
      <h2>14) Test & go‑live checklist</h2>
      <ul class="checklist">
        <li>✅ Create a ticket via the web form and confirm agent notifications.</li>
        <li>✅ Email <code>support@example.com</code>; confirm a ticket is created and auto‑reply is sent.</li>
        <li>✅ Reply to the ticket; confirm the user receives the email.</li>
        <li>✅ Transfer and escalate between departments/teams; confirm SLA timers.</li>
        <li>✅ Close the ticket; verify satisfaction survey if enabled.</li>
        <li>✅ Check logs for mail fetch/SMTP success and any permission errors.</li>
      </ul>
    </section>

    <section id="appendix">
      <h2>Appendix: common commands & paths</h2>
      <div class="card">
        <h3>File permissions (Linux)</h3>
        <pre><code>sudo chown -R www-data:www-data /var/www/osticket
sudo find /var/www/osticket -type d -exec chmod 0755 {} \;
sudo find /var/www/osticket -type f -exec chmod 0644 {} \;
sudo chmod 0444 /var/www/osticket/include/ost-config.php</code></pre>
      </div>
      <div class="card">
        <h3>SELinux (if enabled)</h3>
        <pre><code># Allow web server network and mail
sudo setsebool -P httpd_can_network_connect 1
sudo setsebool -P httpd_can_sendmail 1

# Allow PHP to read attachment directories
sudo chcon -R -t httpd_sys_rw_content_t /var/www/osticket</code></pre>
      </div>
      <div class="card">
        <h3>PHP tuning</h3>
        <pre><code># php.ini suggestions
memory_limit = 256M
upload_max_filesize = 20M
post_max_size = 25M
max_execution_time = 60</code></pre>
      </div>
    </section>

    <hr/>
    <p class="small">© Your Company. This guide is provided as‑is. Replace example paths and domains with your own. osTicket is a trademark of its respective owners.</p>
  </div>
</body>
</html>
