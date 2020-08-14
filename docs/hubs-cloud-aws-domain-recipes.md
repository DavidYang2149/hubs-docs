---
id: hubs-cloud-aws-domain-recipes
title: Domain Recipes
sidebar_label: Domain Recipes
---

This guide provides a few recipes for registering and purchasing necessary domains before creating your hub Cloud stack.

Site domains: **Site Domain Name**, **Internal Domain**, and **Short Link Domain**

Email domains: **Outgoing Email Domain** and **Outgoing Email Subdomain Prefix**

To simplify setup, it's highly recommended you transfer any relevant domains to Route 53, since Hubs Cloud will then be able to manage DNS and SSL certificate renewals for you.

## Is my domain "set up on Route 53"?

**Your domain is set up on Route 53, if it meets one of two criteria:**

- It was purchased on AWS. It's nameservers point to AWS, it shows up on Route 53 Hosted Zones and Registered Domains.
- Your domains are set up to use AWS Route 53 DNS Hosting via Hosted Zones and the nameservers have been changed to point to AWS Route 53. These domains were purchased in a domain registrar like Namecheap or GoDaddy.

**Not set up on Route 53 and want to be?** Follow instructions in [Route 53 Hosted Zones](https://console.aws.amazon.com/route53/home#hosted-zones:) to "Create Hosted Zone" and update the domain nameservers to point to the provided AWS ones in your domain registrar website.

**Why my domain "_CAN NOT_" be set up on Route 53?** Your site, and its nameservers, are already being used elsewhere for other sites or purposes. For example, for us to use mozilla.com (and not break the pre-existing site), we had to follow [Recipe 3](./hubs-cloud-aws-domain-recipes.md#recipe-3-domain-can-not-be-on-route-53) and _NOT_ change nameservers to point to AWS for hubs.mozilla.com to work as a subdomain.

## Recipe 1: Dedicated domain on Route 53

- `myhub.com` **OR subdomain** `hub.myhub.com` connects to your hub - _Warning! Do not create a new Hosted Zone for `hub.myhub.com` on Route 53! The Cloudformation template will manage the connections on your root domain, `myhub.com`, hosted zone._
- `myhub.com` is **_NOT_** used for any other purpose or sites
- `mysite.com` set up on on Route 53 [(?)](./hubs-cloud-aws-domain-recipes.md#is-my-domain-set-up-on-route-53)
- `anothersubdomain.myhub.com` **_could be_** used for any other purposes or sites

### Instructions:

**[Set up or purchase 2 domains on Route 53](./hubs-cloud-aws-domain-recipes.md#is-my-domain-set-up-on-route-53)**

1. `myhub.com` - Houses Hub site domain name + internal server domain
2. `myhub.link` - Short link domain name

**Next, specify the following when creating the stack:**

| STACK OPTIONS                       | RECIPE 1                           |
| ----------------------------------- | ---------------------------------- |
| **Site Domain Name**                | `myhub.com` **OR** `hub.myhub.com` |
| **Site is Set Up On Route 53**      | `Yes`                              |
| **Internal Domain**                 | `myhub.com`                        |
| **Short Link Domain**               | `myhub.link`                       |
| **Outgoing Email Domain**           | `myhub.com`                        |
| **Outgoing Email Subdomain Prefix** | `mail`                             |

## Recipe 2: Domain is in-use, configure subdomain for hub on Route 53

- `hub.mysite.com` connects to your hub. - _Warning! Do not create a new Hosted Zone for `hub.mysite.com` on Route 53! The Cloudformation template will manage the connections on your root domain, `mysite.com`, hosted zone._
- `mysite.com` **_IS_** used for other sites or purposes
- `mysite.com` set up on on Route 53 [(?)](./hubs-cloud-aws-domain-recipes.md#is-my-domain-set-up-on-route-53)
- `anothersubdomain.myhub.com` **_could be_** used for any other purposes or sites

### Instructions:

**[Set up or purchase 3 domains on Route 53](./hubs-cloud-aws-domain-recipes.md#is-my-domain-set-up-on-route-53)**

1. `mysite.com` - Houses subdomain as Hub site domain name + the other sites or purposes at the root 
2. `myhub.link` - Short link domain name
3. `mysite-internal.com` - Internal server domain. This can be any name you want, and will not be seen by users.

**Warning!** Do not create a _new_ Hosted Zone for the subdomain `hub.mysite.com` on Route 53. The Cloudformation template will create CNAME records on the Hosted Zone for your root domain, `mysite.com`, to manage the subdomain connections for you.

**Next, specify the following when creating the stack:**

| STACK OPTIONS                       | RECIPE 2              |
| ----------------------------------- | --------------------- |
| **Site Domain Name**                | `hub.mysite.com`      |
| **Site is Set Up On Route 53**      | `Yes`                 |
| **Internal Domain**                 | `mysite-internal.com` |
| **Short Link Domain**               | `myhub.link`          |
| **Outgoing Email Domain**           | `mysite.com`          |
| **Outgoing Email Subdomain Prefix** | `mail`                |

## Recipe 3: Domain CAN NOT be on Route 53

- `mysite.com` **_CAN NOT_** be set up on Route 53 [(?)](./hubs-cloud-aws-domain-recipes.md#is-my-domain-set-up-on-route-53)
- `mysite.com` connects to your hub **OR** `hub.mysite.com` connects to your hub

### Instructions:

**[Set up or purchase 2 domains on Route 53 (optional 3 domains)](./hubs-cloud-aws-domain-recipes.md#is-my-domain-set-up-on-route-53)**

1. `myhub.link` - Short link domain name
2. `mysite-internal.com` - Internal server domain + email domain. This can be any name you want, and will not be seen by users.
3. _(optional)_ `mysite-mail.com` - Email domain, if using `mysite-internal.com` for emails is not what you want.

To use an existing email provider, read through our [Using an Existing Email Provider Guide](./hubs-cloud-aws-existing-email-provider.md)

**First, follow [Using an Existing Domain: SSL Certificates instructions](./hubs-cloud-aws-existing-domain.md)**

**Next, specify the following when creating the stack:**

| STACK OPTIONS                       | RECIPE 3                                                                                       |
| ----------------------------------- | ---------------------------------------------------------------------------------------------- |
| **Site Domain Name**                | `mysite.com` **OR** `hub.mysite.com` + [SSL certificates](./hubs-cloud-aws-existing-domain.md) |
| **Site is Set Up On Route 53**      | `No`                                                                                           |
| **Internal Domain**                 | `mysite-internal.com`                                                                          |
| **Short Link Domain**               | `myhub.link`                                                                                   |
| **Outgoing Email Domain**           | `mysite-internal.com` **OR** `mysite-mail.com`                                                 |
| **Outgoing Email Subdomain Prefix** | `mail`                                                                                         |

**Lastly, after the stack is successfully deployed, follow [Using an Existing Domain: DNS Setup instructions](./hubs-cloud-aws-existing-domain.md)**

### If you run into any issues:

1. Check **Site Domain Name** is typed correctly with no typos.
2. Check AWS Console > Route 53 > [Hosted Zones](https://console.aws.amazon.com/route53/home#hosted-zones:) and all of the domains you listed above are [registered](https://console.aws.amazon.com/route53/home#DomainListing:) on Route 53 or you've updated the nameservers for your domains to point to AWS Route 53 as the DNS host
3. Find the rollback error in the stack output for your region [AWS Troubleshooting: see first stack error event](./hubs-cloud-aws-troubleshooting.md#my-aws-stack-says-rollback-complete-after-deploying-what-went-wrong)
4. Troubleshoot any common errors via [AWS Troubleshooting documentation](./hubs-cloud-aws-troubleshooting.md)
5. Email hubs-support@mozilla.com for additional assistance
