---
title: "How to Connect a Domain on Cloudflare to AWS EC2 Instance"
categories: "Cloudfare"
tags:
  - "Cloudflare"
  - "AWS"
  - "EC2"
  - "DNS"
  - "nameservers"
  - "domain-management"
  - "web-hosting"
  - "ssl"
  - "https"

header:
  teaser: "/assets/images/2025-06-20-Snowflake-Data-Sharing/teaser.png"
---
![Cover Page](/assets/images/2025-06-20-Snowflake-Data-Sharing/cover.png)

## ✅ Requirements
1. A registered domain name
2. A Cloudflare account
3. AWS EC2 instance

## Step 1: Add Your Domain to Cloudflare

1. Log in to https://dash.cloudflare.com

2. Click “Add a Site”

3. Enter your domain (e.g., example.com)

4. Select the Free plan

5. Let Cloudflare scan existing DNS records

6. Proceed to the next step (you'll add your EC2 IP next)

## Step 2: Point DNS to Your EC2 IP
1. Go to the DNS tab inside your Cloudflare dashboard

2. Add the following A records:

    ```
    Type: A
    Name: @
    IPv4 Address: <your EC2 public IP>
    Proxy: ON (orange cloud) or OFF if needed
    ```
    
    Optional (for www):
    
    ```
    Type: A
    Name: www
    IPv4 Address: <same EC2 IP>
    Proxy: ON
    ```
3. Click Save

## 🛰️ Step 3: Set Cloudflare as Primary & Secondary DNS in Your Domain Provider
Cloudflare gives you two nameservers, for example:
```
NameServer 1: clark.ns.cloudflare.com  
NameServer 2: emma.ns.cloudflare.com 
```

You must copy these and go to your domain registrar (like Namecheap, GoDaddy, etc.):

### 📌 Example: On register.com.np

1. Visit: https://register.com.np

2. Log in with your account

3. Go to the “Domain List”

4. Click your domain name (e.g. yourname.com.np)

5. Scroll to the section called Nameservers

6. Replace existing nameservers with:

    ```
    Primary Nameserver: clark.ns.cloudflare.com
    Secondary Nameserver: emma.ns.cloudflare.com
    ```

7. Click Update Nameservers

> **Note:** It may take a few minutes to hours for DNS to propagate (~1–24 hrs ⏳)and please ensure your domain is already approved and active in the .np system



## Step4: Enable Free SSL (HTTPS) on Cloudflare
Once DNS is active and orange cloud is on (proxy enabled), you can enable SSL:

 🔐 Steps:
1. Go to Cloudflare dashboard → SSL/TLS tab

2. Set SSL Mode to:

    - Flexible → if your EC2 has no SSL certificate

    - Full or Full (Strict) → if your EC2 has a valid certificate

Cloudflare now handles HTTPS for your domain — even if your EC2 isn’t configured for SSL.
