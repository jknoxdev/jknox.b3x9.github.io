---
title: Rabbit R1 ground plane issue
description: Draft
date: 2024-09-17
scheduled: 2024-09-17
layout: layouts/post.njk
tags:
 - rabbit-r1
 - r1
---

### rabbit r1 technical issue

##### notice!! support has been extremely helpful, will post more later! 
so, I've had the rabbit r1 6th generation for about 5 months now and I have to say 
this thing is awesome. 

contrary to popular opinion, I really think this is an amazing product and am really excited to see something new in the tech space that isn't from the giant techbros. 

I've recently ran into an issue where it is in a bootloop, as I mention on the video, I think this is an error in the charging circuit, but I'll put more details into this as I go further in to the analysis. 

But in the video, you can see that as it is power cycling, the camera gets stuck
in 'creepy open mode'. I believe what is happening here is that if you leave it in the charging dock for too long, the battery develops memory and the charging circuit is unable to shunt the excess electicity to ground since the ground plane on this device is really a plastic case.. I think what we could do better would be to add a steel frame around the unit, which should dissapate the excess electricity. I'd have to hook this up to a oscope to be sure. 

<div style="position: relative; padding-top: 56.25%;">
  <iframe
    src="https://customer-gumhm6zr3upeseg1.cloudflarestream.com/c44be07a440a4b1c391ae3dbeb33d855/iframe?preload=true&autoplay=true&poster=https%3A%2F%2Fcustomer-gumhm6zr3upeseg1.cloudflarestream.com%2Fc44be07a440a4b1c391ae3dbeb33d855%2Fthumbnails%2Fthumbnail.jpg%3Ftime%3D%26height%3D600"
    loading="lazy"
    style="border: none; position: absolute; top: 0; left: 0; height: 100%; width: 100%;"
    allow="accelerometer; gyroscope; autoplay; encrypted-media; picture-in-picture;"
    allowfullscreen="true"
  ></iframe>
</div>