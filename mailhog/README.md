# MailHog Docker Compose Setup

A Docker Compose configuration for running MailHog - an email testing tool for developers that captures outgoing emails and displays them in a web interface.

## What is MailHog?

MailHog is a email testing tool that acts as a fake SMTP server. It captures any emails sent to it and displays them in a web UI, making it perfect for:
- Testing email functionality during development
- Previewing email templates
- Debugging email issues without sending real emails
- Avoiding accidental emails to real users during testing

## Service Details

### MailHog
- **Image**: mailhog/mailhog
- **Ports**:
  - `1025`: SMTP server (for sending emails)
  - `80`: Web UI (for viewing captured emails)
- **Access**: http://localhost

## Prerequisites

- Docker installed
- Docker Compose installed
- Port availability: 80, 1025

## Getting Started

1. **Start MailHog**:
   ```bash
   docker-compose up -d
   ```

2. **Check service status**:
   ```bash
   docker-compose ps
   ```

3. **Access the Web UI**:
   Open your browser and navigate to http://localhost

4. **View logs**:
   ```bash
   docker-compose logs -f mailhog
   ```

## How to Use MailHog

### Configuring Your Application

Point your application's email configuration to use MailHog's SMTP server:

**SMTP Settings**:
- **Host**: `localhost` (or `mailhog` if connecting from another Docker container)
- **Port**: `1025`
- **Authentication**: None required
- **Encryption**: None (plain text)

### Example Configurations

**Python (using smtplib)**:
```python
import smtplib
from email.mime.text import MIMEText

msg = MIMEText("This is a test email")
msg['Subject'] = 'Test Email'
msg['From'] = 'sender@example.com'
msg['To'] = 'recipient@example.com'

with smtplib.SMTP('localhost', 1025) as server:
    server.send_message(msg)
```

**Node.js (using nodemailer)**:
```javascript
const nodemailer = require('nodemailer');

const transporter = nodemailer.createTransport({
  host: 'localhost',
  port: 1025,
  secure: false,
  ignoreTLS: true
});

transporter.sendMail({
  from: 'sender@example.com',
  to: 'recipient@example.com',
  subject: 'Test Email',
  text: 'This is a test email'
});
```

**PHP**:
```php
ini_set('SMTP', 'localhost');
ini_set('smtp_port', 1025);

mail('recipient@example.com', 'Test Email', 'This is a test email');
```

**Environment Variables** (common pattern):
```
MAIL_HOST=localhost
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
```

### Viewing Captured Emails

1. Send an email from your application using the SMTP settings above
2. Open http://localhost in your browser
3. You'll see all captured emails in the web interface
4. Click on any email to view its contents, headers, and source

### Web UI Features

- **View emails**: See all captured emails in a list
- **Email details**: Click to view full content, HTML preview, and headers
- **Search**: Find specific emails using the search function
- **Delete**: Clear individual emails or all emails at once
- **Download**: Download emails in .eml format
- **Source view**: Inspect raw email source

## Useful Commands

**Stop MailHog**:
```bash
docker-compose down
```

**Restart MailHog**:
```bash
docker-compose restart
```

**View real-time logs**:
```bash
docker-compose logs -f mailhog
```

## Using with Docker Networks

If your application runs in another Docker container, add it to the same network as MailHog:

```yaml
services:
  your-app:
    # ... your app config
    depends_on:
      - mailhog
    environment:
      MAIL_HOST: mailhog
      MAIL_PORT: 1025
  
  mailhog:
    # ... mailhog config from above
```

Then use `mailhog:1025` as your SMTP server instead of `localhost:1025`.

## Troubleshooting

- **Cannot access web UI**: Ensure port 80 is not already in use. If it is, change the port mapping to something like `"8025:8025"` and update the web UI access to http://localhost:8025
- **Emails not appearing**: Verify your application is configured to use `localhost:1025` (or `mailhog:1025` from Docker)
- **Connection refused**: Check that MailHog is running with `docker-compose ps`
- **Port 80 conflict**: Many services use port 80. Consider changing to `"8025:8025"` in the ports section

## Notes

- **No persistence**: Captured emails are stored in memory and will be lost when the container is stopped
- **No authentication**: MailHog accepts all emails without authentication - this is intentional for development
- **Not for production**: MailHog is a development tool only. Never use it in production environments
- **Performance**: Can handle thousands of emails, but not designed for high-volume testing