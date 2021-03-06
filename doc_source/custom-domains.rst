.. _custom-domains:

#########################
Setting Up Custom Domains
#########################

You can connect a custom domain purchased through domain registrars (for example, Amazon Route 53, GoDaddy, and Google Domains) to your app. When you deploy your web app with the Amplify Console, it is hosted at:

.. code-block:: none

	https://branch-name.d1m7bkiki6tdw1.amplifyapp.com

When you use a custom domain, users see that your app is hosted from a URL, such as the following:

.. code-block:: none

	https://www.awesomedomain.com

The Amplify Console issues an SSL certificate for all domains connected to your app so that all traffic is secured through HTTPS/2. The SSL certificate generated by Amazon Certificate Manager is valid for 30 days and renews automatically as long as your app is hosted with Amplify.

.. contents::
   :local:
   :depth: 1


.. _custom-domain-route53:

Adding a Custom Domain Managed in Amazon Route 53
=================================================

After you deploy your app, you can add a custom domain that you manage using Amazon Route 53. 

1. On the left navigation pane, choose **App Settings**, **Domain management**, and then choose **Add domain**.

   .. image:: images/amplify-customdomain-1.png

2. In **Enter your root domain**, enter your root domain (`https://awesomedomain.com`). As you start typing, root domains that you manage in Amazon Route 53 appear in the list (`https://awesomedomain.com`). Select the domain you want to use and then choose **Configure Domain**. 

   .. image:: images/amplify-customdomain-2.png

3. By default, the Amplify Console automatically adds two entries for `https://www.myroute53domain.com` and `https://myroute53domain.com` with a redirect set up from the `www` subdomain to the root domain (you can change this by choosing **Rewrites and redirects** from the left menu). You can modify the default configuration if you want to add subdomains only, see: :ref:`Connecting subdomains <custom-domain-subdomains>`. Choose **Save** after configuring your domain. 
 
   .. image:: images/amplify-customdomain-configure.png  

**Note:** It can take up to 24 hours for the DNS to propagate and to issue the SSL certificate. For more information about the status messages, see :ref:`Associating a Domain <custom-domain-status>`.

.. _custom-domain-third-party:

Adding a Custom Domain Managed by a Third-Party DNS Provider
============================================================

After you deploy your app, you can add a custom domain that you manage using a third-party DNS provider if you don't use Amazon Route 53. 

1. On the left navigation pane, choose **App Settings**, **Domain management**, and then choose **Add domain**.

2. In **Enter your root domain**, enter your root domain (`https://awesomedomain.com`) and then choose **Configure domain**. If the domain is registered, a green alert notifies you to proceed as long you are the owner of the domain. If the domain is available, purchase a domain at `Amazon Route 53 <https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/domain-register.html>`__.

   .. image:: images/amplify-customdomain-3.png

3. By default, the Amplify Console adds two entries for `https://www.awesomedomain.com` and `https://awesomedomain.com` with a redirect set up from the `www` subdomain to the root domain. You can modify the default configuration if you want to add subdomains only, see: :ref:`Connecting subdomains <custom-domain-subdomains>`. Choose **Save** after configuring your domain. 

4. Because the domain is managed externally you must manually configure your DNS provider (for example, GoDaddy or Google domains). Choose **View DNS records** in the alert banner. 

   .. image:: images/amplify-customdomain-4.png

5. Update your DNS providers' CNAME and ALIAS records as follows. For GoDaddy and Google Domains, see the step-by-step instructions in :ref:`Connecting to Third-Party Custom Domains <howto-third-party-domains>`.

   1. To generate a SSL certificate for your domain, Amplify Console needs to verify ownership. Configure a CNAME to point to the validation server. Once Amplify Console validates ownership of your domain, all traffic will be served using HTTPS/2.

   2. Configure CNAME record to point your subdomains (`https://*.awesomedomain.com`) to the amplifyapp domain. **NOTE**: If you have production traffic it is recommended you update your CNAME record after your domain status shows AVAILABLE.

   3. Configure ANAME/ALIAS record to point the root domain (`https://awesomedomain.com`) to your amplifyapp domain. ANAME records enable you to point the root of your domain to a hostname. For DNS providers that do not have ANAME/ALIAS support, we strongly recommend `migrating your DNS to Amazon Route 53 <https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-configuring.html>`__. **NOTE: If you have production traffic it is recommended you update your ANAME record after your domain status shows AVAILABLE.*

      .. image:: images/amplify-customdomain-5.png

**Important:** Verification of domain ownership and DNS propagation for third-party domains can take up to 48 hours. For more information about status messages, see :ref:`Associating a Domain <custom-domain-status>`.

.. _custom-domain-subdomains:

Adding Subdomains
===================

A subdomain is the part of your URL that appears before your domain name (for example, `www`.amazon.com or `aws`.amazon.com). 

1. **Add a subdomain only**: If you already have a production website, you might only want to connect a subdomain (eg `https://alpha.mydomain.com`). You can do this by choosing **Exclude root** and modifying the subdomain to `alpha` as shown below. 

   .. image:: images/amplify-customdomain-configure-2.png 

2. **Add a multi-level subdomain**: You might want to connect a multi-level subdomain (eg `https://beta.alpha.mydomain.com`). You can do this by entering `alpha.mydomain.com` in the domain search bar, choosing **Exclude root**, and modifying the subdomain to `beta` as shown below. 

   .. image:: images/amplify-customdomain-configure-3.png

3. **Manage subdomains** After adding your domain, you might want to add more subdomains. Choose **Manage subdomains** from the Domain management screen and edit your subdomains.

   .. image:: images/amplify-customdomain-6.png

.. _custom-domain-troubleshoot-guide:

Troubleshooting Guide
=====================

This guide will help you troubleshoot issues regarding the setup of a custom domain in the AWS Amplify Console.

.. contents::
   :local:
   :depth: 1

.. _standard:

Technical Terminology 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1. **CNAME** - A CNAME (Canonical Record Name) is a type of DNS record which allows you to mask the domain for a set of webpages and make them appear as though they are located elsewhere. CNAMES point a subdomain to a Fully Qualified Domain name (FQDN). For example, we can create a new CNAME record to map the subdomain **www**.myawesomesite.com to the FQDN domain **branch-name.d1m7bkiki6tdw1.amplifyapp.com** assigned to our App.

2. **ANAME** - An ANAME record is like a CNAME record, but at the root level. An ANAME will point the root of your domain to a FQDN. That FQDN will actually point to an IP address.

3. **Nameserver** - A Nameserver is a server on the internet specialized in handling queries regarding the location of a domain name's various services. If you have your domain setup in AWS Route53, you will have a list of nameservers assigned to your domain.

   .. image:: images/1555951526863-979.png

**Custom Domain Setup in AWS Amplify Console**

When you create a new domain on the Amplify Console, there are a number of steps which need to happen before you can view your app via your custom domain.

   .. image:: images/1555951758569-803.png

1. **SSL Create** - AWS Amplify Console is issuing an SSL certificate for setting up a secure custom domain.

2. **SSL Configuration/Verification** - Before issuing an SSL certificate, Amplify Console must verify that you are the owner of the domain. For domains managed by Route53, we will automatic update the DNS verification record. For domains managed outside of Route53, you will need to manually add the displayed DNS verification record into your domain’s DNS provider.

3. **Domain activation** - The domain is successfully verified. For domains managed outside of Route53, you will need to manually add the CNAME records provided in the console into your domain's DNS provider.

**Understanding DNS Verification**

DNS stands for Domain Name System, and is commonly referred to being a phone book, translating human-readable names (domains) to computer-friendly addresses (IP Addresses).

When you type **https://google.com** in the browser, a lookup is done in the DNS provider to find the IP Address of server which hosts the website.

DNS providers contain records of domains and their corresponding IP Addresses. Here are the  most commonly used DNS records.

1. **A record** - points the root domain or subdomain to an IP Address

2. **CNAME record** - Points a subdomain to a FQDN (Fully Qualified Domain Name)

3. **NS record** - Points to servers which will lookup your domain details

There are a number of free services on the internet you can use to verify your DNS records. For example, `whatsmydns.net <https://www.whatsmydns.net/>`__.

The Amplify Console uses a CNAME record to verify that you own your custom domain. If you host your domain with AWS Route53, verification is done on your behalf. However, if you host your domain with a Third party, you'll have to manually go into your DNS settings and add a new CNAME record.


How do I verify that my CNAME resolves?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

After your DNS update, you can use a tool like `dig <https://en.wikipedia.org/wiki/Dig_(command)>`__ or a free website like `whatsmydns.net <https://www.whatsmydns.net/>`__ to verify that your CNAME records are resolving.

   .. image:: images/1555952586288-584.png

When you click search, you should that the results show that your CNAME is resolving correctly.

   .. image:: images/1555952626363-494.png

   You can similarly check the other DNS records.

My domain hosted with a third party is stuck in Pending Verification state
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The first thing you'll want to do is to verify if your CNAME records are resolving. See previous step for instructions. If you CNAME records are not resolving, then you should confirm that the CNAME entry exists in the your DNS Provider.

   .. code-block:: none

      Note: If you added or updated your CNAME records a few hours after you
      created your app, this will most likely cause your app to get stuck in
      the pending verification state. It is important that you update your
      CNAME records as soon as you create your custom domain.

   Once your app is created in the Amplify Console, your CNAME records are checked every few minutes to determine if it resolves. If it doesn't resolve after an hour, the check is made every few hours which can lead to a delay in your domain being ready to use.

   Lastly, if you have confirmed that the CNAME records exists, then there might be an issue with your DNS provider. You can either contact the DNS provider to diagnose why the DNS verification CNAME is not resolving or `migrate your DNS to Route53  <https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/MigratingDNS.html>`__.

My domain hosted with AWS Route53 is stuck in Pending Verification state
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you transferred your domain to AWS Route53 then it's possible that your domain has different nameservers then those issued by the Amplify Console when your app was created. Login to the `Route53 console <https://console.aws.amazon.com/route53/home>`__, choose **Hosted Zones** from the left navigation, and pick the domain you are connecting. Record the nameserver values.

   .. image:: images/1555952748759-111.png

Next, choose **Registered domains** from the left navigation. Ensure the nameservers on the registered domain screen match what you copied from the Hosted Zone.

   .. image:: images/1555952748759-607.png

If this did not resolve the issue, please email aws-amplify-customer@amazon.com.

CNAMEAlreadyExistsException Error
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This means that one of the hostnames you tried to connect (could be a subdomain, or the apex domain) is already deployed to another AWS CloudFront distribution. Here's how you can diagnose the issue:

1. Check the `AWS CloudFront Console <https://console.aws.amazon.com/cloudfront/home?#>`__ to see if you have this domain deployed to any other distribution. At a time, you can only have a single CNAME record attached to a one CloudFront distribution.

2. Is this domain connected to a different Amplify App that you own? If so, make sure you are not trying to reuse one of the hostnames. If you are using `www.domain.com` on the other app, you cannot use `www.domain.com` with this appYou can use other subdomains such as `blog.domain.com`.

3. If you had this domain successfully connected to another app and then recently (within the last hour) deleted it, please wait and try again after some time. If you still see this exception after 6 hours, please contact us at aws-amplify-customer@amazon.com.




