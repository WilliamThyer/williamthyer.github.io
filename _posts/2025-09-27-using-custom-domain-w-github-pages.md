---
title: "Setup custom Cloudflare domain for your Jekyll Github page"
date: 2025-09-27
permalink: /posts/2025/9/custom-domain/
tags:
  - tutorial
---

<!-- <img src="" alt="drawing" width="800"/> -->

I setup a custom domain registered with Cloudflare for my Github page. So now my website domain is `williamthyer.com` instead of `williamthyer.github.io`. Here's a brief tutorial on how to do it. This tutorial assumes you already have a [Jekyll-based](https://github.com/jekyll/jekyll) Github page (I forked mine from [AcademicPages](https://github.com/academicpages/academicpages.github.io)). 

## Step 1. Register a domain with Cloudflare
I registered a domain with [Cloudflare Registrar](https://www.cloudflare.com/products/registrar/). I opted for 3 years all at once for just $31.


## Step 2. Update the DNS configuration in Cloudflare dashboard

Go to your Cloudflare dashboard. Under `DNS` > `Records`, go to `DNS Management` for your custom domain.

For the apex domain (`your_custom_domain.com`):  
```
Type: CNAME  
Name: @   
Target: your_current_github_page.github.io  
Proxy status: Proxied (orange cloud)  
```

For the www subdomain (optional, but recommended):  
```
Type: CNAME  
Name: www  
Target: your_current_github_page.github.io  
Proxy status: Proxied (orange cloud)  
```

<img src="https://williamthyer.github.io/images/custom_domain/dns_management.png" alt="drawing" width="800"/>

## Step 3. Create CNAME file in repo
Create a file called `CNAME`. All it contains is the name of your domain. In my case, it was "williamthyer.com".

<img src="https://williamthyer.github.io/images/custom_domain/dns_check_successful.png" alt="drawing" width="800"/>

## Step 4. Add the custom domain to your Github repo
Go to your website repo > `Settings`. Under `Code and automation` go to `Pages`. Under `Custom domain`, add your domain. Wait for the green checkmark that says "DNS check successful"

## That's it!
Make sure your changes are pushed and once your website builds, using the old github.io domain should automattically redirect to your custom domain! I was pleasantly surprised at how easy this was. And to be honest, it's really cool to have a "real" domain now.