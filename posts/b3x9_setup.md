---
title: b3x9 setup notes
description: Here's my notes from the steps needed to setup this blog.
date: 2024-09-15
scheduled: 2024-09-14
tags:
  - another-tag
layout: layouts/post.njk
---

### local notes

- tech used: 

[@11ty/eleventy ](https://www.11ty.dev/)

```bash
cd ~/code/jknoxdev/www.b3x9.github.io
npx @11ty/eleventy --serve
firefox localhost:8080
```

```bash
npm install @11ty/eleventy-plugin-rss
```

### web hosting setup 

#### links
[cloudflare](https://www.cloudflare.com/)
[vercel](https://www.vercel.com)


- clone the template from the google page, and name the new repo: 

redirected.registeredomain.github.io

where redirected will be the project's new subdomain with the squarespace cname redirector: 

- next in the 'Custom domain' section, we will enter the subdir.tld.com and also switch 'build and deployment' over to deploy from branch. in this case the main branch will work!

clone the repo on your workstation

```bash
git clone subdir.tld.github.io
```

- cd into the directory, and setup npm

```bash
cd subdir.tld.github.io
npm i
```

- if it works, congratsNPM is setup! now lets run the preview and start updating the template! 

```bash
npm run watch
```

- point the browser to: 

[`http://localhost:8080`](http://localhost:8080)

- now, lets migrate to vercel for the hosting portion because the google template app won't work github pages

### google analytics

https://www.raymondcamden.com/2020/05/21/integrating-google-analytics-with-eleventy

I also used the following sites, warning, this is indeed a rabit hole: 

https://github.com/GoogleChrome/web-vitals#send-the-results-to-google-analytics

https://web.dev/articles/vitals

https://analytics.google.com/analytics/web/provision/?authuser=1#/provision/create


First, we need to get the javascript that we will use for our template page:
- head over to the [analytics](https://analytics.google.com)
- click through the setup screens entering your hosting info where needed, 

```html
jknox.b3x9.com
```
next, grab the google tag that we will place into our base file at the blog:
```html
<!-- Google tag (gtag.js) -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-X7YQ09YPCG"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());

  gtag('config', 'G-X7YQ09YPCG');
</script>
```

then paste in to the base.njk file (_includes/layouts/base.njk)
```html
<!-- paste in the end of the header file-->
</head>
```

save, publish, and head back over to the [google analytics(https://analytics.google.com)] page
![screenshot of tracking button](https://imagedelivery.net/h6duaPVMwqSx6OPYA68aOw/0c916d3b-3409-40dd-d657-a28ea13c6700/public)

