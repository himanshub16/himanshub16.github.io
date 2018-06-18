---
layout: post
title: How does a URL shortener work?
medium_link: none
categories: [concept, programming]
---

Have you ever visited links like https://goo.gl, https://bit.ly, https://git.io ?
They are some of the renowned URL shortening services on the Internet.

This aim of this post is to explain how does URL shortening works, so one can make on his own.
This post deals with the concepts invovled. For hands-on programming guide, visit this link.

#### Pre-requisites
- Interest in topic.
- Base conversion. If you know to convert deciman to binary and back, that's more than enough.

## What is a shortened URL?
Consider the following examples:

| Shortened version        | Original URL                                                                               |
|--------------------------|--------------------------------------------------------------------------------------------|
| https://goo.gl/KIuxZj    | https://github.com/himanshub16/resume/raw/master/himanshub16.pdf                           |
| https://bit.ly/tweelyser | https://tweelyser.herokuapp.com                                                            |
| https://t.co/VzsLNXdD4Y  | https://www.nasa.gov/image-feature/goddard/2018/hubble-exquisite-view-of-a-stellar-nursery |

The shortened versions appear more precise to the original ones.


## Why to shorten links?
There are two major use-cases where shortening URLs is helpful.
- **Save space**
  Targets like SMS, tweets have strict character-count limit where long URLs can take up lot of space.
  
- **Counting clicks**
  This is the real and most important use of URL shortening.
  Consider the third link in above table.
  Every time one clicks on the t.co link, Twitter gets informed and redirects you to the original link.
