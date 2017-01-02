---
layout: post
category: "read"
title:  "Github Jekyll 时区设置"
tags: [问题,博客]
---

# jekyll 无法及时更新post内容原因

由于timezone设置问题，导致我新建了2017-01-02的post之后，jekyll无法更新，jekyll build也不行，没有出错信息。  
google了一下，发现是timezone的问题，jekyll要求只能更新过去的blog。  
更改了blog的时间，果然可以了。  
参考网上建议，改成  
>timezone: Europe/Amsterdam  

先这样  
