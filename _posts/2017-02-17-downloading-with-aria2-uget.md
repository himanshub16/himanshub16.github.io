---
layout: post
title: Improving download speed on Linux
medium_link: none
---

This blog is about improving download speeds on Linux using aria and Uget download manager.
This is similar to how IDM works on Windows.

<iframe width="100%" height="400"
  src="https://www.youtube.com/embed/HCOlmT5IfUY">
</iframe>


Here's the step by step guide:

* Install ```aria2``` and `uget`.
```
sudo apt install aria2 uget
```
* Open Uget and go to
```
Edit -> Settings -> Plug-in
```
* Change plugin matching order to `aria2+curl`.
![uget window]({{ site.url }}/assets/aria-uget/uget-settings-required.png)

* Set max connections and proxy settings (if any) from ```Category -> Properties -> Default for new download```.
![create profile]({{ site.url }}/assets/aria-uget/create-profile.png)

### You are ready to go. Downloading something!
