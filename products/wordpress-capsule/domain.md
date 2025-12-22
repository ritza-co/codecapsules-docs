---
description: >-
  Configure custom domains for your WordPress capsule to replace the default
  Code Capsules URL.
---

# Domains


When you deploy a WordPress Capsule, Code Capsules assigns it a default URL, like `wordpress-slug.codecapsules.co.za`. You can access your site immediately using this URL.

![Default WordPress URL](../.gitbook/assets/wordpress-capsule/domain/wordpress-default-url.png)

## Adding a Custom Domain

To add a custom domain, navigate to the **Domains** tab in your WordPress Capsule.

On the left panel, you will see DNS configuration instructions. You'll need to create a CNAME or ALIAS record with your DNS provider or point a Type A record to the provided IP address in your domain provider's management console.

Once you have created your DNS records, click the **+** button to add the domain entry.

![Add Custom Domain Button](../.gitbook/assets/wordpress-capsule/domain/add-custom-domain-button.png)

Enter your custom domain in the form.

![Custom Domain Entry Form](../.gitbook/assets/wordpress-capsule/domain/custom-domain-entry.png)

DNS propagation typically takes 15-60 minutes. Once complete, your WordPress site will be accessible at your custom domain.

