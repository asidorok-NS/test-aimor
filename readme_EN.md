# How DNS Works: User Guide & Reference Sheets

**DNS (Domain Name System)** is a naming system that translates human-friendly domain names into machine-readable IP addresses. Without DNS, you would have to memorize long strings of numbers instead of simple website names to browse the internet.

## Why DNS Matters

Devices on the internet locate one another using unique IP addresses (such as `192.0.2.1` or `2001:db8::1`). Remembering dozens of these numerical addresses is impractical for people.

DNS acts as the **phonebook of the internet**: you type a contact's name (the domain), and the system automatically looks up the correct phone number (the IP address) to connect you to the server.

---

## How a Website Lookup Works: 4 Key Steps

When you type a web address into your browser and press **Enter**, DNS resolves the destination in a fraction of a second through a chain of servers.

1. **Local Cache Check.** Your browser and operating system first check if you have visited the website recently. If the record is stored locally on your device, the page loads immediately.
2. **Query to the DNS Resolver.** If no local record exists, your device sends a request to a DNS resolver—a specialized server operated by your Internet Service Provider (ISP) or a public service (such as `8.8.8.8`). The resolver handles the rest of the lookup process.
3. **Hierarchical Server Lookup.** The resolver queries three levels of servers in sequence:
   * **Root Server** — directs the resolver to the appropriate Top-Level Domain (TLD) server based on the domain extension (such as `.com`, `.org`, or `.net`).
   * **TLD Server** — points the resolver to the specific server responsible for the requested domain name.
   * **Authoritative Nameserver** — holds the final configuration for the domain and returns the exact IP address to the resolver.
4. **Page Loading.** The resolver sends the IP address back to your browser and caches it for future requests. Your browser then connects to the website's server at that IP address and loads the page.

---

## Deep Dive: DNS Reference Sheets

Understanding DNS record types and server roles is essential when configuring custom domains, setting up email services, or troubleshooting network issues.

### Common DNS Record Types

| Record Type | Full Name | Purpose | Example Value |
| :--- | :--- | :--- | :--- |
| **A** | Address | Maps a domain name to an IPv4 address. | `93.184.216.34` |
| **AAAA** | IPv6 Address | Maps a domain name to an IPv6 address. | `2606:2800:220:1:248:1893:25c8:1946` |
| **CNAME** | Canonical Name | Forwards one domain (usually a subdomain) to another domain name. | `://example.com` -> `example.com` |
| **MX** | Mail Exchange | Specifies the mail servers responsible for accepting emails for the domain. | `10 ://example.com` |
| **TXT** | Text | Stores text data for domain ownership verification and email security (SPF, DKIM). | `v=spf1 include:_://example.com ~all` |
| **NS** | Name Server | Identifies the authoritative DNS servers managing the domain's records. | `://hosting.com` |

### Key DNS Concepts & Parameters

| Term | Definition | Impact |
| :--- | :--- | :--- |
| **TTL (Time to Live)** | The lifespan of a DNS record in seconds. | Controls how long servers and browsers cache an IP address before requesting a fresh lookup. |
| **DNS Resolver** | A recursive intermediary server. | Receives queries from your device and traverses the DNS server hierarchy to find the answer. |
| **Authoritative Server** | The final source of truth for a domain. | Stores the actual DNS records configured by the domain owner or administrator. |
| **DNS Propagation** | The global update process for DNS changes. | Because of caching, updated records can take anywhere from a few minutes to 48 hours to take effect worldwide. |

## Common DNS Configuration Mistakes

Even a minor typo in your DNS control panel can take your website or email offline. Check these common issues when troubleshooting:

* **CNAME Conflicts with Other Records.** You cannot create a `CNAME` record for a root domain (such as `example.com`) if it already has `MX` or `NS` records. Use `CNAME` records strictly for subdomains (such as `www` or `blog`).
* **Pointing CNAME or MX Records to an IP Address.** These record types must point to a domain name (such as `://example.com`), never a numerical IP address. Always use `A` or `AAAA` records to map a domain to an IP address.
* **Missing Trailing Dot (FQDN).** Some DNS editors require a trailing dot at the end of a fully qualified domain name (such as `://example.com.`). Without it, the system may append your root domain automatically, resulting in a broken address like `://example.com.example.com`.
* **Missing or Misconfigured MX Priorities.** When configuring multiple mail servers, each `MX` record needs a priority number (such as `10` and `20`). Lower numbers indicate higher priority during email delivery.
* **High TTL Before Server Migration.** Leaving a high TTL value (such as `86400` seconds, or 24 hours) before moving a site to a new server forces users to hit the old IP address until the cache expires. Lower the TTL to `300` seconds (5 minutes) at least a day before migrating.
* **Editing Records at the Wrong DNS Provider.** If your domain uses external nameservers (such as a third-party host or CDN), changing `A` or `MX` records at your domain registrar will have no effect. Always manage DNS records at the provider listed in your active `NS` records.