---
pcx_content_type: faq
title: FAQ
weight: 7
structured_data: true
---

# FAQ

Below you will find answers to our most commonly asked questions. If you cannot find the answer you are looking for, refer to the [community page](https://community.cloudflare.com/) to explore more resources.

- [Domain transfers](#domain-transfers)
- [Domain registrations](#domain-registrations)
- [Billing](#billing)

## Domain transfers

{{<faq-item>}}
{{<faq-question level=3 text="Why did my transfer fail?" >}}

{{<faq-answer>}}

Domain transfers sometimes fail. Refer to [Registrar: troubleshoot stalled domain transfers](/registrar/troubleshooting/) for more information on what might have happened and how to solve the issue.

If you cannot solve the issue, open a support ticket or contact your account team.

{{</faq-answer>}}
{{</faq-item>}}

{{<faq-item>}}
{{<faq-question level=3 text="Why did my domain's expiration date change after transferring it to Cloudflare?" >}}

{{<faq-answer>}}

ICANN requires that any transfer also extends the expiration date of your domain by at least one year — that is one year from your current expiration date, not one year from the date of transfer. For example, if you transfer a domain on October 10, 2021, but it expires on March 10, 2022, your new expiration date will be March 10, 2023.

Whenever a domain is first registered, the registrant purchases control of that domain for some number of years — up to 10 years. For example, a domain registered on October 8, 2020 will have an expiration date of October 8th in some year between 2021 and 2030, depending on the amount of years originally purchased.

Transferring a domain adds time to the current expiration date, unless your domain already has [10 years on the term](#if-i-registered-my-domain-for-10-years-at-another-registrar-will-i-gain-another-year-if-i-transfer-it-to-cloudflare).

{{</faq-answer>}}
{{</faq-item>}}

{{<faq-item>}}
{{<faq-question level=3 text="What happens to my nameservers when I transfer my domain to Cloudflare?" >}}

{{<faq-answer>}}

Cloudflare Registrar only supports transfers of domains that are active on a Cloudflare [full setup](/dns/zone-setups/full-setup/). Domains on Cloudflare use [nameservers assigned by Cloudflare](/dns/zone-setups/reference/nameserver-assignment/) to the associated account and those nameservers must remain in place for the domain to be Active.

{{</faq-answer>}}
{{</faq-item>}}

{{<faq-item>}}
{{<faq-question level=3 text="How can I see the status of my domain transfer?" >}}

{{<faq-answer>}}

Once you initiate a domain transfer, your previous registrar has five days to release the domain. In most cases, they will send you an email to confirm you want to transfer. If you actively acknowledge that email (through a link or the registrar's dashboard), they can process it immediately.

To see the progress of your transfer, log in to the Cloudflare dashboard and select your account. Then, select **Domain Registration** > **Transfer Domains** to see a list of domain transfers that are in progress. To accelerate the process, be sure to check with your old registrar how you can approve the transfer out.

Once successful, you will receive an email from Cloudflare and be able to manage the domain in the dashboard under **Overview** of that site.

{{</faq-answer>}}
{{</faq-item>}}

{{<faq-item>}}
{{<faq-question level=3 text="Why am I not allowed to transfer my domain?" >}}

{{<faq-answer>}}

ICANN prohibits domain transfers within 60 days of a change to the WHOIS data or registrar of a domain. If you modified your contact information, transferred registrars, or registered your domain in the last 60 days, Cloudflare will be unable to process your transfer immediately.

You can leave the domain **In Progress** and Cloudflare will wait until after the 60-day window passes to attempt to process the transfer.

{{<Aside type="note" header="Note">}}This information does not apply to `.uk` domains.{{</Aside>}}

{{</faq-answer>}}
{{</faq-item>}}

{{<faq-item>}}
{{<faq-question level=3 text="Why am I not able to start a transfer?" >}}

{{<faq-answer>}}

If you have an [unverified email address](/fundamentals/setup/account/verify-email-address/), you might experience issues when initiating a domain transfer.

{{</faq-answer>}}
{{</faq-item>}}

{{<faq-item>}}
{{<faq-question level=3 text="What happens if I enter the wrong auth code?" >}}

{{<faq-answer>}}

If you enter an incorrect auth code (also referred to as authentication code or authorization code), return to the **Domain Registration** page or the **Overview** for your site. You can use the available input field to reenter your authentication code.

{{</faq-answer>}}
{{</faq-item>}}

{{<faq-item>}}
{{<faq-question level=3 text="If I registered my domain for 10 years at another registrar, will I gain another year if I transfer it to Cloudflare?" >}}

{{<faq-answer>}}

No. A domain cannot have more than 10 years on the term. If you registered your domain for 10 years, you will get 10 years upon transferring it to Cloudflare.

{{</faq-answer>}}
{{</faq-item>}}

---

## Domain registrations

{{<faq-item>}}
{{<faq-question level=3 text="My domain’s registration was not extended by one year after transferring to Cloudflare" >}}

{{<faq-answer>}}

When you transfer your domain to Cloudflare, the registry will extend your registration by one year. However, in one specific circumstance your transfer could result in you keeping your original expiration date.

When a domain expires, the registration enters the auto-renew grace period. During that time, you can renew the domain at your registrar to avoid losing it. If your domain expires at your current registrar, you renew it and then transfer to Cloudflare within 45 days, the registry can restrict the addition of an extra year.

Say you have `example.com` registered and it expires on December 10, 2021. You decide to renew it during the auto-renew grace period on December 20, 2021. That renewal extends the registration to December 20, 2022. You then transfer to Cloudflare on December 30, 2021. Since that transfer is within 45 days of the expiration, the registry may not add the year to your registration. When you transfer to Cloudflare or any registrar in this circumstance, your expiration can still remain December 20th, 2022.

If a year is not added to your registration, you have effectively paid twice for the same added year. Per ICANN rules, you are entitled to request a refund at your previous registrar.

{{</faq-answer>}}
{{</faq-item>}}

{{<faq-item>}}
{{<faq-question level=3 text="What Happens When a Domain Expires?" >}}

{{<faq-answer>}}

In summary, here is what will happen after a domain expires:

- **Day 0**: Expiration Date.
- **Day 1 - 30**: Grace Period (domain resolves normally).
- **Day 31 - 40**: Suspension Period (domains resolves to suspension page).
- **Day 41 - 70**: Redemption Period.
- **Day 71 - 75**: Pending Delete Period.

Cloudflare currently offers a 40-day grace period for most top-level domains (TLDs). 

During this period you may renew/extend the domain at any time from within the dashboard but no further auto-renew attempts will be made. For the first 30 days of the grace period, the domain will continue to resolve as normal. On the 30th day after the expiration date, the domain will be suspended and a parked suspension page will be displayed. You may still renew the domain at any time during this suspension period. On the 40th day, the domain will enter the Redemption Period and will no longer resolve to any web page. 

The redemption period lasts for 30 days. During this time, it may be possible to restore and renew the domain. A restore fee may apply in addition to the renewal fee. At the end of the 30 day redemption period, the domain will be placed in pending delete status for a period of five days, after which it will be released and made available for re-registration. The domain cannot be restored or renewed during this period.

If the domain is in a state where it can be restored, the Manage Domain page in the Registrar section of dash will display a message indicating the domain is restorable. You will then will be able to initiate the restore process directly from the dashboard.

{{</faq-answer>}}
{{</faq-item>}}

## Domain restoration

{{<faq-item>}}
{{<faq-question level=3 text="Which domains are eligible to be restored?" >}}

{{<faq-answer>}}

Domains that are in the Redemption Period and have an EPP status of redemptionPeriod may be restored. For most TLDs this will include domains that are between 40 and 70 days past expiration. 

Currently `.uk` domains cannot be restored using this process. We are working on an alternative process for `.uk` domains and will provide additional information at a later date.

{{</faq-answer>}}
{{</faq-item>}}

{{<faq-item>}}
{{<faq-question level=3 text="Is there a fee to restore a domain?" >}}

{{<faq-answer>}}

Yes, in most cases there is a restore fee. 

The amount varies depending on the TLD. The restore fee is separate from the renewal fee. You will be presented with both the restore and renewal fees before confirming they wish to proceed.

{{</faq-answer>}}
{{</faq-item>}}

{{<faq-item>}}
{{<faq-question level=3 text="Will the domain be renewed after the restore has completed?" >}}

{{<faq-answer>}}

Yes. We will attempt to renew the domain after the restore has been completed. While not common, it is possible for the renewal transaction to fail. 

In the event of a failure, we will make several retry attempts. If we are unable to process the renewal after several retries, you will be presented with a message that you should contact support for assistance.

{{</faq-answer>}}
{{</faq-item>}}

{{<faq-item>}}
{{<faq-question level=3 text="How long does the restore process take?" >}}

{{<faq-answer>}}

The entire process can take a few minutes to complete. 

There are multiple steps to the restore process, and each step must be completed in a specific sequence. These steps are performed automatically by the system. The UI will continue to poll for an updated status and will provide feedback as each step completes.

{{</faq-answer>}}
{{</faq-item>}}

{{<faq-item>}}
{{<faq-question level=3 text="What happens if the domain renewal fails?" >}}

{{<faq-answer>}}

The restore and the renewal are two distinct processes that happen sequentially. 

In rare cases the domain may be successfully restored but the renewal fails. We will make several attempts to renew the domain. However, should all the renewals fail the customer may attempt to manually renew the domain or contact support so we may investigate the cause of the failure.

{{</faq-answer>}}
{{</faq-item>}}

{{<faq-item>}}
{{<faq-question level=3 text="Can a restore be reversed or refunded?" >}}

{{<faq-answer>}}

No. Once a restore has been completed it can not be reversed. It may be possible to delete the domain again but there are no refunds.

{{<Aside type="note" header="Note">}}

Domain names should be released after a period of 75 days, although the exact deletion timeline is ultimately determined by the domain's registry. You should monitor the domain status to ascertain when it will become available for registration once again.

{{</Aside>}}

{{</faq-answer>}}
{{</faq-item>}}

---

## Domain deletions

{{<faq-item>}}
{{<faq-question level=3 text="Why am I unable to delete my Registrar domain?" >}}

{{<faq-answer>}}

A domain can only be deleted if all the following conditions are met:

* The user initiating the action is a Super Admin or Read/Write Administrator.
* The domain is not delete locked at the registry with either `clientDeleteProhibited` or `serverDeleteProhibited`.
* The domain is not already in `pendingDelete`, `redemptionPeriod`, or in `pendingTransfer`.
* The domain has not been administratively locked by Cloudflare.  This typically occurs for legal reasons such as a UDRP filing or court order, but may also be the result of an abuse or payment investigation.
* The domain is NOT a .UK domain. .UK domains currently cannot be deleted at the registry.

If any of the above conditions are not met, the domain cannot be deleted.

{{</faq-answer>}}
{{</faq-item>}}

{{<faq-item>}}
{{<faq-question level=3 text="Who has permission to delete a domain registration?" >}}
{{<faq-answer>}}

Only Super Admins and Administrators with Read/Write access can initiate the deletion of a domain.  Note that only Super Admins will receive the email with the delete token.

{{</faq-answer>}}
{{</faq-item>}}

{{<faq-item>}}
{{<faq-question level=3 text="Will I receive a refund for my deleted domain registration?" >}}

{{<faq-answer>}}
No. Refunds will not be issued for costs incurred by a domain registration.
{{</faq-answer>}}
{{</faq-item>}}

{{<faq-item>}}
{{<faq-question level=3 text="How do I get the domain deletion token?" >}}

{{<faq-answer>}}

The delete token is only sent to the Super Admins of the account. If the user requesting the deletion is not a Super Admin they will need to obtain the delete token from one of the Super Admins of the account.

{{</faq-answer>}}
{{</faq-item>}}


{{<faq-item>}}
{{<faq-question level=3 text="How long is the domain deletion token valid for?" >}}

{{<faq-answer>}}

The delete token is valid for 30 minutes. After the 30 minutes the code will expire and the user must restart the process.

{{</faq-answer>}}
{{</faq-item>}}


{{<faq-item>}}
{{<faq-question level=3 text="Will the domain be deleted immediately from my account?" >}}

{{<faq-answer>}}

If the domain is within 5 days of the initial registration, the domain will be immediately released by the registry and made available for re-registration. In this scenario the domain will be immediately removed from the registrar section of the account. You may need to refresh the page to force an update of the data.

If the domain is more than 5 days old, it will enter the redemption period and will remain in account until the redemption period expires and the registry releases the domain.

{{</faq-answer>}}
{{</faq-item>}}

---

## Billing

{{<faq-item>}}
{{<faq-question level=3 text="How much does Cloudflare Registrar cost?" >}}

{{<faq-answer>}}

Refer to [What is Cloudflare Registrar](https://www.cloudflare.com/learning/dns/what-is-cloudflare-registrar/) for more information on pricing.

{{</faq-answer>}}
{{</faq-item>}}

{{<faq-item>}}
{{<faq-question level=3 text="When will I be billed?" >}}

{{<faq-answer>}}

You will be billed when you input your authorization code and initiate the transfer of your domain to Cloudflare. Currently, Cloudflare Registrar only uses the primary payment method for any associated transaction. Make sure to copy and paste the code to avoid mistakes. The transfer will not initiate if the code is incorrect.

{{</faq-answer>}}
{{</faq-item>}}

{{<faq-item>}}
{{<faq-question level=3 text="Is there a fee to transfer a .UK domain?" >}}

{{<faq-answer>}}

No, there is no fee to transfer a `.uk` domain. Also, an additional year is NOT added during the transfer process. However, if the domain is nearing the expiration date and is set to auto-renew, it may be automatically renewed shortly after the completion of the transfer.

{{</faq-answer>}}
{{</faq-item>}}