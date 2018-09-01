---
title: Cloud Mirrors & Backup Storage
date: '2018-06-03 12:06:00'
tags:
- linux
- backups
- google
- gsuite
- onedrive
- microsoft
---

**This is going to end up more of a personal reference page for myself than anything else most likely as I sometimes find it hard to remember why I did what I did lol**

## Background

The gist of this is that over time, I've used various backup/mirroring tools to take copies of my data and store them in the Cloud. Over time, I've used a few storage providers such as:

- Google Drive
- OVH Dedicated Servers
- VPS solutions
- OneDrive
- Amazon Cloud Drive

As I'm an avid photographer, this data is actually growing at a pretty decent rate due to shooting in RAW. When an image can be 50MB+, the storage tends to add up pretty quickly!

### Amazon Cloud Drive and the rclone fiasco

For a while, I decided that making use of the unlimited photo storage that Amazon Cloud Drive offers as part of its' Amazon Prime membership seemed like a good idea and I started to make use of this with the `rclone` tool which I've been using with good success for a while now. All *was* well and good.

At some point in 2017 (I can't remember when), Amazon decided to revoke the keys that `rclone` was using for access. This became a major problem for me as I back up from a NAS box running Gentoo Linux (with the data stored in RAID1). A workaround was soon developed using an OAUTH proxy run on Google's App Engine and things worked briefly. Eventually I ran into rate-limiting issues which I haven't been able to resolve and thus, ended up stuck.

I even tried running a backup tool (SyncBack SE Pro) from my desktop, to copy the files on the NAS to ACD. It decided however that on every single run, files had *changed*. I didn't really fancy syncing 700GB~ of photo data every single time I wanted to take a copy and this started to seem like more and more effort for something that should really be a painless process.

## Finding something better

At this point, I'd pretty much had enough and decided that if I wanted something painless and that would tick along, I'd have to find another solution to the problem and this might involve some form of monthly spending fee. I won't go into huge amounts of detail but here are the thoughts on solutions I came across.

### Microsoft Onedrive (as part of an Office 365 subscription)

**Disclaimer:** I already use this for a lot of things such as: music storage, encrypted backups of smaller data, document storage and sharing etc.

I'm actually a big fan and it works pretty well for me. I've even got a weird setup where it syncs my Lightroom Catalog files so that I can edit the previews on the move (it's got a few quirks but works pretty much seamlessly!).

#### Pros

- £46.50 for the year for an Office 365 sub from Amazon (£3.90~ per month).
- 1TB storage per user.
- Desktop synchronization client.
- API support (works with `rclone`, `duplicity` etc).
- Office Desktop & mobile license is included and use of all the web applications as well.
- £68 per year (£5.70~ per month) for the 5 user subscription from Amazon. Apparently, each user can have 1TB of OneDrive storage.

#### Cons

- You can't really have more than 1TB of storage per user as a consumer, which rules it out for using as photo storage as well at this point.
- Additional setup required to use multiple accounts. I also don't know how easy it is to move from the 1 to 5 user subscription and and pre-requisites that might have.

### Google Drive (Consumer Edition)

So, I've used this before and it was pretty good but as listed below, cost was a bit much. A second Office 365 subscription for backups would be half the cost (at the expense of being a bit of a pain to scale).

#### Pros

- API Support (same as OneDrive)
- Transfer speeds are good
- Variable storage limits depending on how much you need

#### Cons

- The desktop applications are undergoing a bit of a transition which I haven't quite got to grips with yet. They removed the original sync app and replaced it with two others. As I haven't used that in a while, it might be fine, but something to be aware of.
- £7.99 a month per TB (over twice the cost of an Office 365 subscription. The limit is going up to 2TB soon though, so it will be more competitive if you're just looking for storage.)

### Dropbox Plus

I won't go into too much detail on this one but basically the 1TB is the same price as Google Drive. This pretty much has the same pros/cons as Google Drive (Consumer Edition) apart from the fact that when I have used it in the past, the Desktop Application was pretty slick compared to other providers I've used. If you are swayed by the previous but want better desktop support, Dropbox is definitely a contender!

### AWS S3/Glacier

Given that this is run by an entirely different Amazon division and is Production grade storage, it's worth including to see how this pans out. That said, retrieval cost pretty much rules out this option for consumer usage.

#### Pros

- Production grade, various policies for availability and durability, all of them being 99% or higher.
- Glacier costs for primarily storage are reasonable per GB ($0.004-$0.0045 per GB).
- S3 API support is an industry standard for a lot of tools, so lots of support.

#### Cons

- Costs vary per region per GB stored.
- API calls and bandwidth are calculated as separate costs.
- Retrieval from Glacier (which is the only price-competitive option), can be very costly.


### Backblaze B2

I very much almost went for this, as it was quite an appealing option. Note, this is the professional, not the consumer option which just backs up everything on your PC.

#### Pros

- Good API support, with a multitude of clients available (including `rclone`).
- Price per GB ($0.005) is good. Not as good as Glacier but you don't have a myriad of costs. You pay a small amount for putting data in and then just pay for the data at rest. Deletes are mostly free unless you do a huge amount of transactions (and even the transaction cost is reasonable).
- Dedicated subreddit with responses from the people who run Backblaze
- If you need to restore a lot, you can pay to get a drive with all your data shipped to you, rather than trying to download it all.

#### Cons

- Limited amount of data centers (I think 2 at time of writing?)
- Daytime outage when attempting to provision support for more clients. With the amount of data they hold, I was expecting somewhat better management when I read about the recent incident, with regards to scaling.
- There's a restore cost for getting data out, varying by method.

### Google Drive for Business (as part of G Suite)

So, this is an interesting consideration, depending on number of users (< 5 or 5+), you get slightly different packages. Also, there's a difference between G Suite Basic (£3.30 pcm/user) vs G Suite Business (£6.60 pcm/user).

Here, we'll evaluate the Business edition, as the Basic doesn't give you more than 30GB of storage.

#### Pros

- 1TB per user (<5 users) or **unlimited** (5+ users). The big pro here is that, according to reddit users, the 1TB limit **isn't enforced actively**. This means you effectively can have unlimited storage with less than 5 users on the account.
- Plenty of API support for Google Drive (see above)

#### Cons

- If you can consider this a con, there's a 750GB upload limit daily and there's a multi terabyte download limit daily.
- At any point, if you're using with less than 5 users, in theory the 1TB limit of storage could be enforced.
- Same issue as Google Drive for consumers with desktop applications

## Overall Solution

**Winner:** Google Drive for Business (G Suite Business)

To start with, I already use OneDrive as part of the Office 365 subscription due to the price and storage space and the fact I get Office licenses and web apps all together. As much as Google pushes its' own productivity suite, the Microsoft suite is a major preference for me.

I was also a G Suite Basic subscriber, therefore I have all my email etc hosted with Google already and paying £3.30 per month for the privilege of high availability and all the other integration features. This made my choice a no-brainer really.

- For the £3.30 per month upgrade to G Suite Business, I could use the Google Drive storage. It gives me 1TB (although in practice, it's unlimited, so it could scale forever).
- The speeds are good.
- I don't need to create yet *another* user account to manage.

If in future I hit the storage limit and it becomes enforced, I might look back into the Office 365 5 User Family subscription and start looking at spreading my data across multiple accounts for cost efficiency. Until then though, this was a fairly easy solution to put in place (a few button clicks to upgrade my subscription and an OAUTH signin with `rclone` and I was good to go and start syncing!).
