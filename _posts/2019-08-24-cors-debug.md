---
title:  "My Thought Process while Debugging a CORS error"
date:   2019-08-24
---

I've been thinking ([and writing](/blog/2019/cors-1)) about CORS recently because there's a CORS error on the Bowdoin Orient's site. The stylesheets for loading the Orient's fonts are coming up with a CORS error in the Firefox console, while the Chrome console shows CORS errors for both the stylesheet and the font files.

I thought the fix would be fairly easy but a quick diagnostic showed that it's a little more complicated. I'm not sure what the fix is, but I'm going to try to outline my thought process while fixing it to give a sense of how I think about debugging.

- Is this really even a problem? The site looks fine to me—the fonts are showing up fine.
- My computer has the Orient fonts downloaded locally, so maybe the browser is finding the fonts there. I should check on a different computer.
- I checked on my girlfriend's computer. The CORS errors are still showing up in her browser's console, but the fonts look pretty much fine, except for one: Chronicle, font weight 700, not italic.
  - Interestingly enough, 700/italic, 600/regular, and 900/regular are all available. It just seems to be that one weight/style combo.
  - Also interestingly, *every* font coming from the CDN is showing CORS errors in the console, but every font except that one combo looks... totally fine.
  - I wish I could deprioritize the bug given that it's not breaking all the fonts, but the Chronicle 700 font is used as the article headline on the [single.php page](https://developer.wordpress.org/themes/template-files-section/post-template-files/#single-php) which is arguably the most viewed part of the website.
- The browsers are complaining that there isn't an `Access-Control-Allow-Origin` header on the response, so let's figure out why not.
- The response headers in the network tag don't have an `Access-Control-Allow-Origin` header
- When I copy the Curl request, I don't see the headers either:

  ```bash
  $ curl 'https://font-cdn.bowdoinorient.co/files/orient-fonts.css' \
  -H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.14; rv:68.0) Gecko/20100101 Firefox/68.0' \
  -H 'Accept: text/css,*/*;q=0.1' -H 'Accept-Language: en-US,en;q=0.5' --compressed \
  -H 'DNT: 1' -H 'Connection: keep-alive' -H 'Referer: https://bowdoinorient.com/' \
  -H 'Pragma: no-cache' -H 'Cache-Control: no-cache' -I

  HTTP/2 200
  content-type: text/css
  content-length: 17214
  last-modified: Thu, 28 Feb 2019 00:37:37 GMT
  accept-ranges: bytes
  server: AmazonS3
  date: Sun, 25 Aug 2019 00:09:55 GMT
  etag: {idk maybe this should be censored}
  age: 21541
  x-cache: Hit from cloudfront
  via: 1.1 275c261effb3ee5f39bd3dd96f438f26.cloudfront.net (CloudFront)
  x-amz-cf-pop: SFO5-C3
  x-amz-cf-id: {maybe this should be censored too??}
  ```

- I checked AWS and the bucket *should* have CORS set up on it. The [CORS troubleshooting documentation](https://docs.aws.amazon.com/AmazonS3/latest/dev/cors-troubleshooting.html)[^1] says there need to be certain criteria met before the CORS-related headers are included in the response.
- I wonder what happens when only use an `Origin` header.

  ```bash
  $ curl 'https://font-cdn.bowdoinorient.co/files/orient-fonts.css' \
  -H 'Origin: https://bowdoinorient.com' -I
    
  HTTP/2 200
  content-type: text/css
  content-length: 17214
  date: Sat, 24 Aug 2019 03:54:06 GMT
  access-control-allow-origin: https://bowdoinorient.com
  access-control-allow-methods: GET
  access-control-max-age: 3000
  access-control-allow-credentials: true
  last-modified: Thu, 28 Feb 2019 00:37:37 GMT
  etag: {censored}
  accept-ranges: bytes
  server: AmazonS3
  x-cache: RefreshHit from cloudfront
  via: 1.1 100e7eca600d702a8613a94cb0899fe9.cloudfront.net (CloudFront)
  x-amz-cf-pop: SFO5-C3
  x-amz-cf-id: {censored}
  ```

- Nice. So CORS is set up properly.
- When I use a `Referer` header instead of an `Origin` header in the request above, it still works. So something in the full request is causing the CORS headers to drop.
- I sequentially added each header in a new CURL request. It wasn't any of the headers, it was the `--compressed` flag.
- I thought my S3 configuration was wrong, but maybe it's my Cloudfront configuration; maybe Cloudfront, in compressing the files, is removing the CORS headers that S3 is providing.
- I found a [documentation page about Cloudfront and CORS](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/header-caching.html#header-caching-web-cors). I whitelisted four headers in my Cloudfront Default Cache Behavior Settings:
  - `Access-Control-Request-Headers`
  - `Access-Control-Request-Methods`
  - `Origin`
  - `Referer`
- Now none of my CURL requests are giving me any CORS headers in the response; even the ones that used to work
- But the CORS error is gone from Firefox now.
- And from Chrome.
- After clearing the browser cache, the page looks fine on my girlfriend's computer. I think I fixed this.
- I wonder if I need to clear the Cloudfront cache or add a cachebusting query onto the `<link>` tag.
- The end.


[^1]: My goodness this page was a slog to find. And it's not even that helpful.