# Mail-Alert-for-scripting
Step-by-step guide to setting up and enabling Gmail SMTP authentication for Postfix on Ubuntu. Includes installation, configuration, and email testing instructions.

# Setup and Enable Gmail SMTP Authentication for Postfix

## Step 1: Generate an App Password for Gmail
Since Gmail no longer allows direct password authentication for SMTP, you must generate an "App Password".

1. Go to [Google App Passwords](https://myaccount.google.com/apppasswords) (Log in to your Google account).
2. Select **Mail** as the app and **Other (Custom name)** for the device (e.g., "Ubuntu Postfix").
3. Click **Generate** → Copy the 16-character password (you will need it in Step 2).

## Step 2: Install Required Packages
Ensure that `mailutils` and `postfix` are installed on your system.

```bash
sudo apt update
sudo apt install mailutils postfix -y
```
During the installation, select **Internet Site** when prompted and enter your system's domain name.

## Step 3: Configure Postfix to Use Gmail SMTP

### 1️⃣ Edit Postfix SMTP Credentials
Run:

```bash
sudo nano /etc/postfix/sasl_passwd
```
Add the following line (replace `<your-email>` and `<app-password>`):

```ini
[smtp.gmail.com]:587 <your-email>@gmail.com:<app-password>
```

### 2️⃣ Secure the Credentials File
```bash
sudo postmap /etc/postfix/sasl_passwd
sudo chmod 600 /etc/postfix/sasl_passwd /etc/postfix/sasl_passwd.db
```

### 3️⃣ Configure Postfix for Gmail SMTP
Open the main Postfix configuration file:

```bash
sudo nano /etc/postfix/main.cf
```

Add or modify the following lines:

```ini
relayhost = [smtp.gmail.com]:587
smtp_use_tls = yes
smtp_sasl_auth_enable = yes
smtp_sasl_security_options = noanonymous
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt
smtp_tls_session_cache_database = btree:/var/lib/postfix/smtp_tls_session_cache
```

Save and exit (**CTRL + X, then Y, then Enter**).

## Step 4: Restart Postfix
```bash
sudo systemctl restart postfix
sudo systemctl status postfix
```

## Step 5: Test Email Sending
Now, try sending a test email:

```bash
echo "Test email from Ubuntu" | mail -s "Test Subject" hrparab99@gmail.com
