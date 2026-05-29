DNS (Domain Name System)
Why DNS Exists

Let's start with a simple question.

Imagine I ask you to open YouTube.

You type:

youtube.com

into your browser.

Now think about this:

How does your laptop know where YouTube is located?

Your laptop doesn't understand:

youtube.com

Computers communicate using IP addresses.

Example:

142.250.80.46

For a computer, an IP address is like a home address.

If the computer doesn't know the destination address, it cannot send the request.

So before opening YouTube, the browser must answer:

What is the IP address of youtube.com?

DNS exists to solve exactly this problem.

The Problem DNS Solves

Imagine the internet without DNS.

Instead of:

youtube.com
google.com
amazon.com

you would have to remember:

142.250.80.46
172.217.160.110
205.251.242.103

This would be impossible at internet scale.

DNS provides a human-friendly naming layer.

Human Language
        ↓
youtube.com

DNS Translation
        ↓

Machine Language
        ↓

142.250.80.46

Think of DNS as the internet's phonebook.

You know the person's name.

You don't know their phone number.

DNS gives you the number.

Why Not Use IP Addresses Directly?

Many beginners ask:

If computers need IP addresses, why don't we simply use IP addresses?

Excellent question.

There are several reasons.

Reason 1: Human Readability

Which is easier?

youtube.com

or

142.250.80.46

Obviously the domain name.

Humans remember names.

Machines remember numbers.

Reason 2: IP Addresses Change

Suppose YouTube moves its servers.

Today:

youtube.com
        ↓
142.250.80.46

Tomorrow:

youtube.com
        ↓
34.110.250.10

Users continue using:

youtube.com

No changes required.

DNS absorbs infrastructure changes.

Reason 3: One Domain Can Have Many IPs

Most people think:

youtube.com
        ↓
1 IP

Wrong.

In reality:

youtube.com
        ↓
IP-1

youtube.com
        ↓
IP-2

youtube.com
        ↓
IP-3

DNS can return different IPs.

This helps distribute traffic.

Reason 4: Geographic Routing

A user in India should not always go to a server in the USA.

That would increase latency.

Instead:

India User
       ↓
Mumbai Server

US User
       ↓
Iowa Server

Europe User
       ↓
Belgium Server

DNS can return different answers depending on the user's location.

This concept is called:

GeoDNS

We'll revisit it later.

DNS Architecture

Most people think DNS is a single server.

It's not.

DNS is a massive distributed system.

A lookup travels through multiple layers.

Browser
    ↓
OS Cache
    ↓
Recursive Resolver
    ↓
Root Server
    ↓
TLD Server
    ↓
Authoritative Server

Every layer exists for a reason.

Let's understand each one.

Step 1: Browser Cache

Imagine you open:

youtube.com

The browser asks:

Do I already know the IP address?

Modern browsers maintain an internal DNS cache.

Example:

youtube.com
    ↓
142.250.80.46

If found:

Cache Hit

No network request needed.

If not found:

Cache Miss

Move to next step.

Why Browser Cache Exists

Network calls are expensive.

Typical timings:

Memory Lookup
≈ Microseconds

DNS Network Lookup
≈ 20–100 ms

Cache is thousands of times faster.

This is the first lesson in system design:

Always check cache before going to the network.

You'll see this pattern repeatedly.

Step 2: OS Cache

Suppose browser cache misses.

Browser asks the Operating System.

Example:

Windows:

DNS Client Service

Linux:

systemd-resolved

macOS:

mDNSResponder

The OS also maintains a DNS cache.

Why?

Because multiple applications may need the same DNS record.

Example:

Chrome
Slack
VS Code
Teams

All might access:

api.company.com

The OS cache prevents duplicate lookups.

Step 3: Recursive Resolver

Suppose OS cache also misses.

Now the request leaves your machine.

It reaches a:

Recursive Resolver

Usually owned by:

ISP
Google DNS (8.8.8.8)
Cloudflare DNS (1.1.1.1)

The resolver's job is:

Find the answer on behalf of the client.

The browser says:

Find youtube.com

The resolver says:

Leave it to me.
I'll do all the work.

This is why it is called:

Recursive Resolution
Why Not Let Browser Contact Root Servers Directly?

Imagine billions of devices contacting root servers.

Root servers would collapse.

Instead:

Millions of Clients
          ↓
Few Recursive Resolvers
          ↓
DNS Infrastructure

This dramatically reduces load.

Step 4: Root Name Servers

Now the resolver begins searching.

First stop:

Root Server

Many people misunderstand root servers.

Root servers do NOT know YouTube's IP.

They only know:

Where .com lives
Where .org lives
Where .net lives
Where .in lives

Think of root servers as a receptionist.

Example:

Question:
Where is youtube.com?

Root:
I don't know.
But .com servers know.
Ask them.
Why DNS Is Hierarchical

Imagine storing:

All domains
All IPs
Entire Internet

inside one server.

Impossible.

DNS uses delegation.

Root
 ↓
.com
 ↓
google.com
 ↓
youtube.com

Each layer knows only its responsibility.

This allows DNS to scale globally.

Step 5: TLD Server

TLD means:

Top Level Domain

Examples:

.com
.org
.net
.in
.io

The resolver asks:

Where is youtube.com?

TLD replies:

I don't know the IP.

But Google's DNS servers know.

Ask them.
Step 6: Authoritative DNS Server

Now the resolver reaches the final source of truth.

Google Authoritative DNS

This server owns the record.

Example:

youtube.com
       ↓
142.250.80.46

Unlike caches:

Browser Cache
OS Cache
Resolver Cache

the authoritative server contains the actual DNS record.

This is where the answer finally comes from.

Complete DNS Resolution Flow
DNS Record Types
A Record

Maps domain to IPv4.

Example:

youtube.com
      ↓
142.250.80.46
AAAA Record

Maps domain to IPv6.

Example:

youtube.com
      ↓
2607:f8b0:4004:800::200e
CNAME Record

Alias.

Example:

www.youtube.com
      ↓
youtube.com
MX Record

Mail server.

Example:

gmail.com
      ↓
Mail Infrastructure
NS Record

Defines authoritative DNS servers.

Example:

google.com
      ↓
ns1.google.com
DNS Caching

After the resolver gets the answer:

142.250.80.46

it stores it.

Why?

Because another user might ask the same question.

Without caching:

Every lookup
→ Root
→ TLD
→ Authoritative

This would overload DNS infrastructure.

Caching dramatically reduces traffic.

TTL (Time To Live)

Every DNS record has:

TTL

Example:

300 seconds

Meaning:

Cache answer for 5 minutes

After expiration:

Lookup again
TTL Tradeoff
Low TTL
60 seconds

Pros:

Faster failover
Faster updates

Cons:

More DNS traffic
High TTL
3600 seconds

Pros:

Less DNS traffic

Cons:

Slower failover
DNS in System Design

Now we move beyond networking.

This is where architects care.

GeoDNS

Users should reach the nearest data center.

India User
      ↓
Mumbai

US User
      ↓
Iowa

Europe User
      ↓
Belgium

Same domain.

Different answers.

Failover

Primary:

10.0.1.100

Backup:

10.0.2.100

Normal:

youtube.com
      ↓
Primary

Failure:

youtube.com
      ↓
Backup

DNS becomes the first layer of disaster recovery.

CDN Integration

This is how YouTube and Netflix scale.

Instead of:

User
 ↓
Origin Server

they do:

User
 ↓
CDN Edge
 ↓
Origin Server

DNS routes users to the nearest CDN edge.

Real World Example: YouTube

When a user in Hyderabad opens YouTube:

youtube.com
       ↓
DNS Lookup
       ↓
Nearest Edge Location
       ↓
CDN
       ↓
Video Starts

Without DNS-based routing:

India User
      ↓
US Server

Latency would be much higher.

Common Misconception

Many engineers think:

DNS Change
=
Instant Update

Wrong.

Because of caching:

DNS Change
≠
Instant Propagation

TTL controls propagation speed.

Interview Questions
Why can't DNS replace a Load Balancer?

DNS routing:

Once per lookup

Load Balancer:

Every request

Different responsibilities.

Why is DNS hierarchical?

To scale to billions of domains.

Why is caching critical in DNS?

To reduce latency and infrastructure load.

Architect Perspective

Most engineers think:

DNS = Name Resolution

Architects think:

DNS =

Routing Layer
Failover Layer
Geo Distribution Layer
CDN Entry Point

Before a request reaches your infrastructure, DNS has already influenced:

Where traffic goes
Which region serves the request
Which CDN node responds
How failover occurs

That is why DNS is far more important than most engineers realize.

Revision Notes

Remember these four statements:

DNS converts names into IP addresses.

DNS is hierarchical.

Caching makes DNS scalable.

DNS is the first layer of traffic routing.

For Principal Engineer interviews, always connect DNS to:

Global Routing
GeoDNS
Failover
CDN Integration
Latency Optimization

because that is where DNS becomes a system design topic rather than just a networking topic.
