---
title: "The one with a guide for configuring a proxy-email service"
seoDescription: "Learn how to configure a proxy-email service focusing on security and privacy with ProxiedMail for your custom domain"
datePublished: 2026-01-27T22:22:02.431Z
cuid: cmkx5vsu7000002l5bd2mdgzs
slug: the-one-with-a-guide-for-configuring-a-proxy-email-service
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1770663122646/050d08f7-f412-482a-8ed0-3abd2def15c2.png
tags: dns, email, privacy, custom-domain, email-forwarding, proxiedmail, proxy-email

---

> Disclaimer: This is not a sponsored post.

I recently registered the domain [davidmontesdeoca.dev](https://davidmontesdeoca.dev) through [sav.com](https://sav.com/). At the time of this writing, they offer a highly competitive price of $4.99 for the first year, with a renewal rate of $12.68 per year.

After configuring the blog DNS on Hashnode, I was surprised to find no option for setting up an email account. Since the feature was missing from the control panel, I contacted customer support via their web chat. They confirmed that they do not provide email services, though they did recommend several third-party email providers.

Until now, every time I registered a domain, I had access to a webmail service and had the possibility of creating an email account and multiple aliases for it. Usually, I would configure Gmail to receive and send emails for and from that email address too.

Ultimately, I chose [ProxiedMail](https://proxiedmail.com/) as my proxy-email provider, a service that was not among the recommendations from the sav.com team. I was impressed by their [security and privacy-focused approach](https://proxiedmail.com/en/blog/using-email-directly-not-safe-anymore), the intuitive dashboard for configuring proxy-emails, and the availability of the following features within their free tier:

![Free plan's features](https://github.com/user-attachments/assets/e1904a50-7ce7-4ef2-bbc9-b0889ff41844 align="center")

The following steps outline how I configured a proxy-email specifically for this post: `blog-post[@]davidmontesdeoca.dev`.

After registration, the dashboard allows you to create a new proxy-email:

![ProxiedMail's dashboard](https://github.com/user-attachments/assets/3397dd3b-a69a-4b4d-a9a1-41ea0c2ce78e align="center")

Since I am using a custom domain, I must first add it to the system:

![Form to add a custom domain](https://github.com/user-attachments/assets/67c961bc-4f57-4de6-8e70-25dde3d74afb align="center")

The verification process involves several steps:

![Step 1 of the domain configuration process: Domain ownership](https://github.com/user-attachments/assets/62c7a42e-ec43-46f4-bdfc-823ec40dd2b2 align="center")

To begin the verification, navigate to sav.com: `My Domains` -> `davidmontesdeoca.dev` -> `Manage DNS` -> `Custom DNS`.

The process for each record is identical: create a record in the domain provider dashboard using the data provided by the proxy-email service, then verify the record.

First, you must verify the domain ownership:

```plaintext
Type: TXT
Name: davidmontesdeoca.dev
Value: proxiedmail-verification=<verification code>
```

> Using @ as the name works exactly the same way.

In the Proxy settings, you must select *DNS Only*. Choosing *Enabled* will trigger the following error:

```plaintext
Code 9004: This record type cannot be proxied.
```

Once ownership is verified, proceed to the MX record configuration:

![Step 2 of the domain configuration process: MX record](https://github.com/user-attachments/assets/99bfbfc7-5574-432f-b26c-7c7e73297c67 align="center")

Add the following record at your domain provider:

```plaintext
Type: MX
Name: davidmontesdeoca.dev
Value: mx.proxiedmail.com
Priority: 10
Proxy: DNS Only
```

Once the MX record is verified, proceed to the SPF record configuration:

![Step 3 of the domain configuration process: SPF record](https://github.com/user-attachments/assets/67d87c8a-bbf4-4233-b7ec-3cbbcf65d5b1 align="center")

The process for adding this record is the same:

```plaintext
Type: TXT
Name: davidmontesdeoca.dev
Value: v=spf1 include:spf.proxiedmail.com ~all
Proxy: DNS Only
```

Once the SPF record is verified, optionally proceed to the DKIM record configuration, which helps ensure your forwarded emails are not marked as spam by receiving servers:

![Step 4 of the domain configuration process: DKIM record](https://github.com/user-attachments/assets/70c47fa5-f771-47c4-8002-4aa55b5d81e3 align="center")

To enable DKIM, add the following record:

```plaintext
Type: CNAME
Name: dkim._domainkey.davidmontesdeoca.dev
Value: dkim._domainkey.pxdmail.com
Proxy: Enabled
```

While other verifications are near-instant, this one may take longer to propagate. Once applied, you will see the message: *You are all set*.

You are now ready to create your first proxy-email using your verified custom domain:

![Form to create a proxy-email](https://github.com/user-attachments/assets/4f3aaa9a-5648-49d1-99b3-ad91a4fb8301 align="center")

A confirmation message will appear:

![Modal with message regarding the first proxy-email created](https://github.com/user-attachments/assets/03b94771-a55c-419b-acd0-6f52382b9cf7 align="center")

With forwarding enabled by default, emails sent to the proxied email address will be received at your specified real email account:

![Test of the proxy-email sent](https://github.com/user-attachments/assets/314107c0-bcca-4542-bc8e-92321551e3af align="center")

![Details of the test of the proxy-email sent](https://github.com/user-attachments/assets/a4f34959-a12f-45e6-9a12-fb3e0c90f297 align="center")

The email arrived in less than a minute:

![Test of the proxy-email received](https://github.com/user-attachments/assets/6aee4af7-0f22-4034-8773-b42f17c690ae align="center")

![Details of the test of the proxy-email received](https://github.com/user-attachments/assets/f0d95a67-8892-43b5-912e-0280149e3aa2 align="center")

> This only works if the recipient is in the *To* field. During testing, emails where the recipient was placed in the *BCC* field were not delivered.

In the dashboard you can see the total number of emails forwarded to the new address:

![The proxy-email just generated in the dashboard](https://github.com/user-attachments/assets/d2b5a18f-e720-497e-a4f1-d5348d78eeea align="center")

ProxiedMail also supports wildcard proxy email addresses:

![Form to create a wildcard proxy-email](https://github.com/user-attachments/assets/fd4b23fe-99a2-4bba-a50e-ea6f5ddea207 align="center")

A modal explains the functionality:

![Modal explaining what are the wildcard proxy-emails](https://github.com/user-attachments/assets/2434e725-6137-4755-8b3c-dba4a8d44310 align="center")

As with the other proxy-email, forwarding is active immediately:

![Test of the wildcard proxy-email sent](https://github.com/user-attachments/assets/0deb2b4c-d509-4fc0-abc1-63788d1ebce1 align="center")

![Details of the test of the wildcard proxy-email sent](https://github.com/user-attachments/assets/11d60c0b-933a-4bc8-a7c6-e8c79b14f57e align="center")

The email sent to this email address was also delivered in less than a minute:

![Test of the wildcard proxy-email received](https://github.com/user-attachments/assets/35b25286-2a66-464c-a137-8732281d2b7f align="center")

![Details of the test of the wildcard proxy-email received](https://github.com/user-attachments/assets/f4ca4c53-3102-4889-8b2a-5a29f6905596 align="center")

In the dashboard you can see the total number of emails forwarded to this new address too:

![The wildcard proxy-email just generated in the dashboard](https://github.com/user-attachments/assets/930a7e2d-e795-4490-8c9a-99a506d497e2 align="center")

To conclude, I will highlight several other features available on the platform, both free and paid:

*   Deleting proxy-emails is a paid feature. On the free plan, you can only disable unused email addresses:
    
    ![Modal asking the user to upgrade their plan to delete a proxy-email](https://github.com/user-attachments/assets/80ff31a3-7b0e-41d5-8c89-547821bec104 align="center")
    
*   Hiding the forwarded email banner is also a paid feature:
    
    ![Modal with message telling the user that removing the forwarded email banner from the emails is a paid feature](https://github.com/user-attachments/assets/861ffb90-3d87-4980-bc28-69fd9dc5fab6 align="center")
    
*   Adding an extra layer of securiy to your account with Two-Factor Authentication (2FA) is only available in a paid tier.
    
*   Storing a password to send emails via an alias did not work with Gmail during my testing; so most likely it requires a paid plan:
    
    ![Form to store a password for a proxy-email](https://github.com/user-attachments/assets/30fbbe33-abfc-4329-be23-0ccfbfa9cb3e align="center")
    
    ![Form to generate a password for a proxy-email](https://github.com/user-attachments/assets/0f839ce8-e5f4-4d3c-af06-917fa2b971ac align="center")
    
    However, they offer a compelling feature for managing contacts:
    
    ![Form to create a new contact](https://github.com/user-attachments/assets/e79cde22-7822-404c-95be-05ea3e5a1197 align="center")
    
    ![Explanation of the reverse alias process used for contacts](https://github.com/user-attachments/assets/68447ace-498f-45c7-8570-8197bf708ec5 align="center")
    
    This uses a reverse alias process, which worked perfectly in my tests (though the initial email was flagged as spam):
    
    ![Test using reverse alias for a contact](https://github.com/user-attachments/assets/124f1636-3db3-4524-baf6-8cc5a3532170 align="center")
    
    ![Details of the test using reverse alias for a contact](https://github.com/user-attachments/assets/5b43e11d-195b-4856-9da4-99cc30bf4ddb align="center")
    
*   Using unique email addresses for every site where you register to track potential data leaks:
    
    ![Form to add websites where a given proxy-email has been used](https://github.com/user-attachments/assets/a3056df9-7ed1-4813-9344-c30f5623a5de align="center")
    
*   Adding context to remember the purpose of each proxy-email:
    
    ![Form to add a description to know where a given proxy-email has been used](https://github.com/user-attachments/assets/bb3eacf0-cda2-4037-9e72-8891072a6eb9 align="center")
    
*   Identifying which proxy received a specific message with a reverse lookup:
    
    ![Modal with the form for the proxy email reverse lookup](https://github.com/user-attachments/assets/e62e40d8-5f37-4498-b5ce-6cf3b5f8a79c align="center")
    
    ![Modal with the result of the proxy email reverse lookup](https://github.com/user-attachments/assets/4fe21f25-bd24-4f78-bd6c-8678d4beb6bb align="center")
    

It is also worth noting that if you use the free tier, ProxiedMail requires you to verify your account via email every quarter. This is a simple process to confirm your account is still active and helps them manage inactive accounts:

```plaintext
Thank you for using ProxiedMail.

You are receiving this as part of the quarterly verification of your ProxiedMail account. This verification allows us to track the active accounts and prevent spam.

All accounts that didn't pass the verification during the year could be frozen. That means you will stop receiving your messages when the account is frozen. Although, your account will be unfrozen on any activity in your account.

Additionally, verifying your account will give you some benefits as faster messages delivery and priority support.
```

The service offers other functionalities beyond those listed here. I encourage you to see for yourself.

Although I have not tested every feature yet, what I have discovered while writing this post is impressive and definitely offers a significantly better user experience than relying on a provider's native webmail interface.

I really like ProxiedMail's approach, being a compelling alternative to traditional email setups. It is also significantly easier to configure.

Thank you for reading and see you in the next one!