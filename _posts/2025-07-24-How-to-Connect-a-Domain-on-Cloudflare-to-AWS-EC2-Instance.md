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
1. A registered domain name (e.g., **mydomain.com.np**)
2. A Cloudflare account
3. AWS EC2 instance

## Step 1: Add Your Domain to Cloudflare

1. Log in to https://dash.cloudflare.com

2. Click “**Add a Site**”

3. Enter your domain (e.g., mydomain.com.np)

4. Select the **Free plan**

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

## Step 3: Set Cloudflare as Primary & Secondary DNS in Your Domain Provider
Cloudflare gives you two nameservers, for example:
```
NameServer 1: clark.ns.cloudflare.com  
NameServer 2: emma.ns.cloudflare.com 
```
You must copy these and go to your domain registrar (like Namecheap, GoDaddy, etc.):

### Example: On register.com.np

1. Visit: https://register.com.np

2. Log in with your account

3. Go to the “Domain List”

4. Click your domain name (e.g. mydomain.com.np)

5. Scroll to the section called Nameservers

6. Replace existing nameservers with:

    ```
    Primary Nameserver: clark.ns.cloudflare.com
    Secondary Nameserver: emma.ns.cloudflare.com
    ```

7. Click Update Nameservers

> **Note:** 
> 1. It may take a few minutes to hours for DNS to propagate (**~1–24 hrs** ⏳)  
> 2. please ensure our domain is already approved and **active** in the .np system.

>💡 **Tip:** Check DNS Propagation Online: https://www.whatsmydns.net/


## Step 4: Enable Free SSL (HTTPS) on Cloudflare
Once DNS is active and orange cloud is on (proxy enabled), you can enable SSL:

1. Go to Cloudflare dashboard → **SSL/TLS tab**

2. Set SSL Mode to:

    1. **Flexible** → if your EC2 has no SSL certificate

    2. **Full or Full (Strict)** → if your EC2 has a valid certificate

> **Note:**
> 1. If your EC2 instance does not have an SSL certificate, set SSL/TLS mode to "Flexible" in Cloudflare.
> 2. If your EC2 instance has a valid SSL certificate installed, you should use "Full" or "Full (Strict)" mode for end-to-end encryption.

## Conclusion
By setting up Cloudflare as our **free DNS service**, we gain better control, **enhanced security (with SSL)**, and **improved performance** for our AWS-hosted website.

In this guide, we:
1. Pointed nameservers from our domain registrar to Cloudflare

2. Set up A records to our EC2 public IP

3. Enabled SSL to secure traffic using Cloudflare.

Once everything is in place, our website becomes globally accessible and benefits from Cloudflare’s powerful edge network and security features — all at no cost.
