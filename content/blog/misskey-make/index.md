---
title: Trials and tribulations of creating a Misskey instance
date: "2023-07-02T22:12:00.284Z"
description: "Watch as I create a Misskey instance"
---

# The start
Today, I woke up and opened Twitter, and saw that Mr. Elon just decided to randomly destroy the platform and add a big ass ratelimit, limiting Non-Blue users to seeing only 600 tweets per day. So, I decided to embrace the Fediverse, and try to create my own Misskey instance.

# Part 1 - The Server
To create my Misskey instance, I needed a server to host it on. Since I wanted my server to be in an area near where I live, I wanted a hosting provider that had servers in Japan.

## The first Linode
The first hosting provider that came to mind was Linode. Since I had a good experience with them for hosting my ShareX server, and the fact that they had a Tokyo region, I created a Linode (2GB, 1vCPU) and started the setup process. However, the first time I did the Misskey setup, I made the mistake of forgetting to change the config files before I ran the build command (skill issue) and messed up my server.

## The Second Linode
After deleting and re-creating the Linode (cuz that was the easiest thing to do apparently) I retried the Misskey setup, and was able to fully create the server as intended.]

# Part 2 - The Storage
I had also decided that instead of hosting files on the server itself, I would use an S3-compatible object storage provider (cuz AWS expensive AF) and host the files on there. I was also going to use Linode Object Storage to host the files on. However, while Linode provided servers in Japan, the closest I could get my Object Storage hosted in was in was Singapore. Nevertheless, I decited to press on and create a Linode storage bucket.

## Reconsiderations
I had trouble with the S3 setup (mostly because of skill issue) and was not able to get S3 working properly. After reaching out to some friends of mine, (Notably Googlefan of [Neody](https://neody.land) and mc-fdc of [mc-fdc-dev](https://tuna2134.jp),) I was pointed to some reconsiderations of my hosting setup.

# Part 3 - New Setup

## ConoHa
It was pointed out to me that for a Misskey instance (something that will probly be hosted indefinetly) using a hosting provider that billed monthly instead of ny the hour would cost me less. So, after coercing with my friends for a bit, I decided to use a [GMO ConoHa](https://conoha.jp) server instead. So, for the third time in one day, I redid the Misskey setup.

## Cloudflare R2
Though I previously thought that there were no affordable S3 providers with storage zones in Japan, it was also pointed out to me that [Cloudflare R2](https://cloudflare.com/products/r2/) actually has storage in Japan in its Asia zone. I had not known of this, and since Cloudflare R2 is free for the first 10GB, i decided to jump on to that instead. I might move my ShareX server's files ofer from Linode at some point as well.

# Conclusion
I went through a lot while creating my Misskey instance, and was able to learn some new skills as well.

# So where's this Misskey instance?
Join me in https://sumerucity.com/ ! (This domain was left over as a late-night buy)