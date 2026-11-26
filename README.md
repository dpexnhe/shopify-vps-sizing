# Shopify VPS: Do You Need One at All, How Much RAM Webhooks and Headless Frontends Need, and Which SixtyNet Plan Fits (Full Price Table)

Your Shopify storefront will never run on a VPS. Shopify hosts the store, the checkout, and the admin on its own cloud, and that hosting is part of the subscription you already pay for. What people actually rent a server for is the work around the store: custom apps, API sync jobs, webhook endpoints, price scrapers, and headless frontends built in Next.js or Hydrogen.

That split decides your budget. A webhook receiver and a headless storefront differ by roughly four times the RAM, and on SixtyNet's plan list it decides whether you can buy the cheap line at all, because part of that catalogue cannot accept an incoming HTTPS request.

**Quick pick.** One webhook endpoint plus a nightly sync job lands on LAX-MICRO: 4 cores, 4 GB RAM, 80 GB NVMe, 10 TB of traffic, **$400 per year**. 👉 [Check the LAX-MICRO plan at SixtyNet](https://sixtynet.net/aff.php?aff=39)

## First work out which "Shopify VPS" problem you have

Three different people search this phrase, and they need three different machines.

**"Can I move my store onto a VPS?"** No, and you don't want to. Shopify's own documentation is blunt about self-hosting: you can buy servers and run a site yourself, but it is complicated and expensive, and you take on 24/7 availability, security, updates, and troubleshooting. When you subscribe to Shopify, hosting, maintenance, security, and scaling are all handled inside its managed cloud. Moving the storefront off Shopify means leaving Shopify, not adding a server to it.

**"I need somewhere to run my Shopify app."** This is the common case. The storefront stays in Shopify's cloud and the VPS runs the services around it: a custom app, an order or inventory sync, a webhook handler, a feed generator, a repricing bot. You get root access, your own daemons and cron, and a fixed public IP, and you install Node.js, Python, or PHP yourself.

**"I'm building a headless storefront."** Shopify stays the backend for products, checkout, payments, and orders, and the VPS serves the frontend. This is the only scenario where the server is customer-facing and needs real headroom.

Sizing rules that follow from that:

- One webhook handler and a couple of sync jobs: 4 vCPU and 4 GB is the usual fit.
- Several integrations, a queue, ERP or warehouse sync, image-heavy exports: start at 6 GB and watch disk, because logs and temporary image files eat a 40 GB volume faster than people expect.
- Production headless frontend: 6 to 8 vCPU with 12 to 24 GB. Advanced headless features and higher API limits generally point to Shopify Plus, while a basic headless build can run on a standard plan, so confirm your API limits before you size the box.

## SixtyNet's plans, and the one line that works for Shopify

SixtyNet runs a single location, Los Angeles, and quotes annual prices. Four product lines sit on the VPS page, and only one of them is usable for anything Shopify sends traffic to.

| Plan | vCPU / RAM | NVMe | Traffic | Annual price (list) | Order |
| --- | --- | --- | --- | --- | --- |
| LAX-STARTER | 2 / 2 GB | 40 GB | 5 TB | **$200/yr** ($240) | [Order LAX-STARTER](https://sixtynet.net/aff.php?aff=39) |
| LAX-MICRO | 4 / 4 GB | 80 GB | 10 TB | **$400/yr** ($480) | [Order LAX-MICRO](https://sixtynet.net/aff.php?aff=39) |
| LAX-MEDIUM | 8 / 8 GB | 160 GB | 20 TB | **$800/yr** ($960) | [Order LAX-MEDIUM](https://sixtynet.net/aff.php?aff=39) |
| LAX-LARGE | 8 / 16 GB | 320 GB | 40 TB | **$1,600/yr** ($1,920) | [Order LAX-LARGE](https://sixtynet.net/aff.php?aff=39) |

These are the DDoS-protected plans with normal public networking, which is the requirement for a webhook endpoint or a headless frontend. Everything else on the page is aimed at a different buyer.

**The residential and mobile lines close ports 80 and 443.** SixtyNet says this explicitly on the product page: residential IP products do not open those ports for IP risk-control reasons. That kills them for webhook receiving and for serving a storefront, because both need standard HTTPS on a public port. It does not kill them for outbound work. If the reason you want a Shopify-adjacent server is scraping competitor prices or ad feeds, a residential IP is the whole point, since datacenter ranges get blocked quickly, and an outbound-only box is fine for it. The residential range runs from 2 cores/2 GB to 16 cores/16 GB with 5 to 40 TB of traffic, and the official page lists Residential-MEDIUM (8 cores, 8 GB, 160 GB NVMe) at $860/yr against a $1,020 list price. The mobile line is smaller still, 2 to 4 cores with 50 GB of mobile data at $50 and $100 per year, and has nothing to do with Shopify work.

One more clause worth reading before you pick a residential plan: on the hardened residential variant, refunds are issued as website balance rather than money back. 👉 [See the full SixtyNet VPS list and current annual pricing](https://sixtynet.net/aff.php?aff=39)

## What running Shopify services on one of these actually involves

Four steps, no control panel hand-holding:

1. Pick a plan and a Linux image. Ubuntu and Debian are the safe defaults for a Node or Python stack.
2. Deploy the OS yourself through the panel onto the KVM instance. The configuration stays under your control, which also means nobody configures it for you.
3. SSH in, install your runtime (Node.js, Python, or PHP), connect to the Shopify API, and start your app or worker under a process manager so it survives a reboot.
4. Point your DNS at the server, put HTTPS in front of it, and record the public IP so you can register a stable webhook endpoint and allowlist the machine wherever your Shopify app configuration expects it.

Budget half a day if you have done it before, and a full day if you have not.

## The money part: annual billing, three-day refunds, and what $400 really buys

SixtyNet quotes yearly figures. LAX-MICRO at $400/yr is $33.33 a month, and LAX-STARTER at $200/yr is $16.67. Compare that honestly against the market: a 4 vCPU/4 GB/40 GB NVMe KVM box in Europe starts at €5.49/mo, and a 3 vCPU/8 GB/75 GB headless-oriented plan runs $21.17/mo on a 12-month term. For a single small webhook handler, SixtyNet is not the cheap option.

What you pay the premium for is the location and the pipe: Los Angeles latency for a US customer base, 10 TB of monthly traffic on the $400 plan, and DDoS filtering included rather than sold as an add-on. If your app talks to US carriers, marketplaces, or West Coast APIs all day, that is a real difference. If your only traffic is Shopify posting a few thousand webhooks a day, it is not.

Watch the billing structure. The higher figure in each row is the list price and the lower one is what annual billing costs, a 17% discount for paying up front. No renewal rate is published, so plan your second year at the list price instead of the discounted one. Shopify's own hosting-cost guidance makes the same point about total cost of ownership: a low initial price means little if the fee rises sharply after the first term, and it is worth checking both the upfront and renewal figures before committing. Hosting resellers do this openly, with plans advertised at $19.99/mo for the first term renewing at $39.99.

Two things to confirm at checkout, because they are easy to miss. Third-party reviews from this year report that card payments were suspended for fraud screening at the time of writing, so check which payment methods the checkout offers before you commit to a plan. And the money-back window is **three days**, not the thirty that most hosts advertise, which is enough to boot the server, install your runtime, and confirm the network behaves, but not enough to run a month-long trial.

## Who should not buy any of this

If you sell products and do not write code, stop here. Your Shopify subscription already includes hosting, security, scaling, and backups, and a VPS adds a monthly bill plus a machine you have to patch. There is no performance argument for moving a standard Shopify store onto a server you administer.

If you run WooCommerce, you are in a different conversation. There, the VPS is the store, PHP and MySQL are yours to tune, and the plugin stack is your responsibility, which is why WooCommerce hosts start their guidance at 4 GB rather than 1 GB.

## Shopify hosting vs a VPS for surrounding services vs a VPS for a headless frontend

|  | Shopify's own hosting | Self-managed VPS for apps and integrations | VPS for a headless frontend |
| --- | --- | --- | --- |
| Control | No server access, apps only | Root, own runtimes, cron, queues | Root plus full frontend stack |
| Cost | Included in the subscription | Roughly $70 to $800 per year at the low end of the market | $250 to $1,600+ per year |
| Maintenance | Shopify | You, including OS patches | You, plus frontend deploys and caching |
| Fits | Stores that just sell | Developers running apps, syncs, webhooks | Teams replacing the storefront layer |

## Choosing, in one line each

Store that just sells: nothing. One webhook endpoint or a single sync script: LAX-MICRO at $400/yr, and drop to LAX-STARTER only if you know the workload is outbound-only and tiny, since 2 GB gets tight once Node and a database share the box. Three or more apps with queues and image exports: LAX-MEDIUM for the 160 GB of disk as much as the 8 GB of RAM. Hydrogen or Next.js in production: LAX-LARGE, the only plan in the lineup that reaches the 12 to 24 GB band headless builds want. Outbound scraping where the IP reputation matters more than the ports: the residential line, with the port restriction and the balance-only refund in mind.

Before you order, confirm two things: that the annual list price is what you can afford in year two, and that Los Angeles is close enough to your users and your APIs. Everything SixtyNet sells sits in that one city, so if your customers are in Frankfurt, latency is a solved problem for someone else.

👉 [Start with the LAX-MICRO plan at SixtyNet](https://sixtynet.net/aff.php?aff=39)
