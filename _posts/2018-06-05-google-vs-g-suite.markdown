---
title: Personal Google vs G Suite - Differences when using Google Services
date: '2018-06-05 20:16:00'
tags:
- google
- gsuite
---

## G Suite Migration

So a little while back, I switched all my email over to G Suite instead of hosting my own solution (I was paying for a VPS to do this as well, which was more costly). Eventually, you do get to a point where you realise that someone else can do something better than you and your time is more valuably spent doing other things. I was also routing a large amount of my email via Gmail for public usage anyway as it was easier to explain over the phone than my domain email address (possibly just a bad choice on my part for this one xD).

G Suite has a few advantages that replicating myself would be quite costly and/or time consuming:

- **More Highly available:** Getting a multi-master setup to be reliable with Dovecot over the internet was a bit of a time investment and I was never convinced it worked properly. I would also have to replicate the various frontends.
- **Tagging & Search:** This was quite a big one. It's so much easier to tag and search in Gmail than set up the infrastructure required to do it myself and make it highly available and performant.
- **Unmanaged:** I would no longer have to debug and look after the infrastructure myself. Email can be fussy at times just trying to avoid spam filters.
= **Calendar & Contacts Support:** Whilst I had this working with CalDAV and CardDAV via Davical, integrating this every time with various plugins was a bit tedious and didn't work greatly when it came to tying it into an interface. The HA side of things comes into play here as I never set it up to be HA.

Some of the other nice features that made it easy to use were the following:

- **Unlimited Domains:** This was quite nice as I could add as many domains as I wanted and redirect emails to one user with many aliases, much as I had been doing with the Open Source stack I was using. I retained delimiter support in emails, so migrating across was pretty much seamless.
- **Storage:** With G Suite Business (see my previous article for more details), I have pretty much unlimited storage on Drive now.

To be honest, it's just pretty easy to use, which also makes it quite appealing.

In the end, the main reason I chose it would be the **custom domain support** and ability to retain **+** delimiter support in emails.
