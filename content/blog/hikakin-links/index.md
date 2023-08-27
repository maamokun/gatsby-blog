---
title: Exploring the YouTube Data API and Web Scraping - Magic Hikakin Links
date: "2023-08-27T23:38:00.284Z"
description: "A short project which took me down a small rabbit hole..."
---

# Introduction
While browsing domains for an unrelated project with some friends, one of them discovered that the domain "hikakintvevery.day" was up for grabs. Now this is interesting, because the tagline "Hikakin TV Everyday!" is the intro of highly famous Japanese YouTuber [HikakinTV](https://youtube.com/hikakintv). So, as an act of respect, We decided to register the domain, and create a system that redirects you to his latest video.

# Take One - The YouTube Data API
The simplest way I thought of accomplishing this task was to first make a request to the YouTube Data API to find the latest video, and redirect the user to the URL. Since this is a very simple task, I decided to use Cloudflare Workers to handle the requests. The YouTube Data API turned out to be a very easy to use API, as the API key and request parameters can all be passed through Query Parameters, and no special headers need to be passed to use it. With the help of ChatGPT, I was able to quickly create a Workers script that could accomplish this task. And with that, my magic URLs were working!

# Problems arouse.
After testing out my magic URLs for a while, they suddenly stopped working. Puzzled, I looked at the logs for the Workers. It turns out that my multiple requests to the YouTube Data API had seemingly exceeded the daily limit on API requests. I could have made a request to lift the limit of daily requests, but I thought that would be too much work, and decided to try a different approach.

# Take Two - Web Scraping
Since the latest video of a creator is always going to be on the homepage of their channel, I decided to rty out Web Scraping as a way of getting the latest video URL without running into limits. I first tried to create the scraping script inside the worker itself, but this proved challenging because of the restrictive nature of Cloudflare Workers built in HTML parsing and browsing capabilities. So, I decided to do it the old-fashioned way and create a simple web API using ExpressJS.

```
const puppeteer = require('puppeteer');
const express = require('express');

const app = express();
const PORT = process.env.PORT || 3000;

const getURL = async (channelURL) => {
  const browser = await puppeteer.launch();
  const page = await browser.newPage();

  await page.goto(channelURL);

  const latestVideoLink = await page.$eval('.ytd-grid-video-renderer a', link => link.href);

  await browser.close();
  return latestVideoLink;
}

app.get('/', async (req, res) => {
  try {
    const channelURL = req.query.channelURL;
    const videoURL = await getURL(channelURL);
    res.json({ videoURL });
  } catch (error) {
    console.error(error);
    res.status(500).json({ error: 'An error occurred' });
  }
});

app.listen(PORT, () => {
  console.log(`Example app listening on port ${PORT}!`);
});
```
This was the code formy very simple API. I used puppeteer to get the URL of the latest fideo of the channel, the URL provided by the `channelURL` query parameter. However, it was recommended to me to cache the URLs instead of scraping for them every time, to avoid getting ratelimited.

```const express = require('express');
const puppeteer = require('puppeteer');
const NodeCache = require('node-cache');

const app = express();
const PORT = process.env.PORT || 3000;

// Create a cache instance with a TTL (time to live) of 1 hour
const cache = new NodeCache({ stdTTL: 3600 });

const getURL = async (channelURL) => {
  // Check if the response is already in the cache
  const cachedResponse = cache.get(channelURL);
  if (cachedResponse) {
    return cachedResponse;
  }

  const browser = await puppeteer.launch();
  const page = await browser.newPage();

  await page.goto(channelURL);

  const latestVideoLink = await page.$eval('.ytd-grid-video-renderer a', link => link.href);

  await browser.close();

  // Cache the response for future requests
  cache.set(channelURL, latestVideoLink);

  return latestVideoLink;
}

app.get('/', async (req, res) => {
  try {
    const channelURL = req.query.channelURL;
    const videoURL = await getURL(channelURL);
    res.json({ videoURL });
  } catch (error) {
    console.error(error);
    res.status(500).json({ error: 'An error occurred' });
  }
});

app.listen(PORT, () => {
  console.log(`Example app listening on port ${PORT}!`);
});
```

This revised version of the code uses the `node-cache` module to cache responses with a TTL of 1 hour instead of scraping for them every time. This will help avoid my server getting ratelimited by YouTube. With that, I modified my Workers script to make a request to a hosted version of this API instead of YouTube's own, and with that, my links were back in action!

# Conclusion
What seemed to be a very simple project turned out to be a little more difficilt than expected, and taught me a thing or two about how to do basic web scraping. I think that these skills can be useful to me in my other projects as well!

# The URLs
Try these out for yourself :)

https://hikakintvevery.day/ - Latest HikakinTV video
https://games.hikakintvevery.day/ - Latest HikakinGames video
https://seikin.hikakintvevery.day/ -Latest SeikinTV video
https://blog.hikakintvevery.day/ - Latest HikakinBlog video
https://seikingames.hikakintvevery.day/ - Latest SeikinGames video
https://masuo.hikakintvevery.day/ - Latest MasuoTV video