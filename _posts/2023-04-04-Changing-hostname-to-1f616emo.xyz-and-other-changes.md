---
title: Changing hostname to 1f616emo.xyz, and other changes
date: 2023-04-04 09:54:10+08:00
categories: ["Server Maintainence","Service Changes"]
tags: []     # TAG names should always be lowercase
author: emoji
---
Serveral changes have done and will be done on Toyshost Services.

## Domain name migration
I have created the domain name [1f616emo.xyz](https://1f616emo.xyz) on Feb 2, 2023. Services on TH3 with slowly migrated from thost3.de to 1f616emo.xyz. Redirects will be created from the old to the new domains once the migration was done upon a particular service. The use of the subdomains of thost3.de, if a subdomain of 1f616emo.xyz pointing to the same site exists, is deprecated.

> Though will still work within a year, the domain name thost3.de should not be treated as stable. If you have any bookmarks that are pointing to the old domain, please change them as soon as possible.
{: .prompt-info }

## Moving services to different machines
All static website services will be moved to Cloudflare Pages, and some other services will be moved from TH3 to TH2. This help balancing the workload of two servers, and increase the access speed of static websites.

The following services have been migrated:
1. Main website (TH3 -> CF Pages, <https://1f616emo.xyz/>)
2. (Partly) Blog (Th3 -> CF Pages, <https://blog.1f616emo.xyz/>)

The following services will be migrated:
1. Static file downloads (TH3 -> load balance between TH3 and TH2)
2. Small script download (TH3 -> CF Pages)
3. dNotes (TH3 <https://dnotes.thost3.de/> -> TH2)

The following services will never be moved and will be kept as-is:
1. [Chat Relay EMOLWB2](https://t.me/emoji_lwb2_bot) (TH3, managed by [sunafterrain](https://zh.wikipedia.org/wiki/User:Sunafterrain))

## Drop of some seldom-used services
The following services will be dropped because of their low demand of use or their failed operation:
1. SimpleUpload (TH3, <https://simpleupload.thost3.de/>)
2. AnonQ (TH3, <https://anonq.thost3.de/>)
3. Pastebin (TH3, <https://pb.thost3.de/>)
4. Django Blog (TH3, <https://dblog.thost3.de/>)
5. Wiki (TH3, <https://wiki.thost3.de/>)
6. Wordpress Blog (TH3, <https://blog.thost3.de/>, contents will be moved to CF Pages)
7. About Emoji (TH3, <https://about.thost3.de/>)
8. Minetest C&C Server Website (TH3, <https://mtccs.thost3.de/>)
9. Telegram Bot Plug (TH3, TG[@emoji_tgplug_bot](https://t.me/emoji_tgplug_bot))
10. E-Handbook (TH3, Details kept private)
