---
layout: post
title: How does a URL shortener work?
medium_link: none
categories: [concept, programming, url shortener, algorithm]
---

Have you ever wondered links like [https://goo.gl](https://goo.gl), [https://bit.ly](https://bit.ly), [https://goo.gl](https://goo.gl)  ?

They are some of the renowned URL shortening services on the Internet.

This aim of this post is to explain how does URL shortening works, so one can make on his own.

> This post deals with the concepts invovled. If you are looking for code, click the lord's face and get it.

[![Talk is cheap, show me the code](/assets/url-shortener/talk-is-cheap.jpg)](https://github.com/himanshub16/outbound-go)


#### Pre-requisites
- Interest in topic.
- [Base conversion](https://www.tutorialspoint.com/computer_logical_organization/number_system_conversion.htm). If you know to convert deciman to hexadecimal and back, that's more than enough.

## What is a shortened URL?
Consider the following examples:

| Shortened version          | Original URL                                                                               |
|----------------------------|--------------------------------------------------------------------------------------------|
| <https://goo.gl/KIuxZj>    | <https://github.com/himanshub16/resume/raw/master/himanshub16.pdf>                         |
| <https://bit.ly/tweelyser> | <https://tweelyser.herokuapp.com>                                                          |
| <https://t.co/VzsLNXdD4Y>  | <https://www.nasa.gov/image-feature/goddard/2018/hubble-exquisite-view-of-a-stellar-nursery> |

The shortened versions appear more precise to the original ones.


## Why to shorten links?
There are two major use-cases where shortening URLs is helpful.
- **Save space**
  Targets like SMS, tweets have strict character-count limit where long URLs can take up lot of space.
  
- **Counting clicks**
  This is the real and most important use of URL shortening.
  Consider the third link in above table.
  Every time one clicks on the t.co link, Twitter gets informed and redirects you to the original link.

## Finding a short representation
Well, that's simple. Index it.

Just associate a serial number to any content, and that gets indexed. Isn't this how we are allotted roll numbers in schools?

Here's how it might look like:

| Serial # | URL                                  |
|----------|--------------------------------------|
|        1 | https://github.com                   |
|        2 | https://google.com/search?q=computer |
|        3 | https://twitter.com/status/1         |
|      ... | ...                                  |

**But that'd eventually lead to a problem.**

There will come a time soon when the numbers used will be of more than 8 or 10 digits, which is fine but can be made even better.

## A higher base would help
Decimal number system use base 10, i.e. they use 10 symbols to represent a number. Hexadecimal system uses 16 (0-9 A-F). Thus, a large number like `65432` is represented as `ff98`.

Take even higher base, say 26 (**0-9 a-p**), 36 (**0-9 a-z**), 62 (**0-9 a-z A-Z**), and apply the same concept.

Here's the conversion that'd happen:

|      Base 10 | Base 16    | Base 26   | Base 36  | Base 62 |
|--------------|------------|-----------|----------|---------|
|           27 | 1b         | 11        | r        | r       |
|       938641 | e5291      | 21adf     | k49d     | 3Wbn    |
|    999999999 | 3b9ac9ff   | 3647job   | gjdgxr   | 2wDOpb  |
| 987948554334 | e60652985e | 4j030eice | cluv27a6 | NOolhwe |


For an exercise,
```
3Wbn =>  (3 * 62^3)  + (W * 62^2)  + (b * 62^1) + (n * 62^0)
      = (3 * 238328) + (58 * 3844) + (11 * 62)  +    23
      =   714984     +   222952    +   682      +    23
      = 938641
```

Clearly, bases 36 and 62 seem to be safest choices and require less space compared to smaller bases.

## So how does this work in software?

[Find code implementation here](https://github.com/himanshub16/outbound-go/blob/master/baseconv.go)

- Assign a serial number to a URL. This can be the primary key if it is a SQL database.
- Convert that serial number to base 62 (you can use any base).
- `yourdomain.com/base_62`_value is the required URL.

- Whenever one hits the above URL, you get the string for `base_62_value`.
- Convert that `base_62_value` to `base_10` integer as above exercise does.
- That is the serial number you are looking for.


#### Note
- Make sure the serial number is something unique and never repeats.
- If you are using MongoDB, use a serial number stored in some other collection.

:tada: Here you go! Make one like [this](https://outbound-go.herokuapp.com) yourself.
