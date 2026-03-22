---
title: Writeup&#58; Gralhix OSINT013
date:
  created: 2026-03-22
links:
  Link to challenge [external]: https://gralhix.com/list-of-osint-exercises/osint-exercise-013/
---

--8<-- "pages/solutions_working/gralhix_preamble.md"

## Introduction

I picked this challenge because it said that "Unfortunately it is no longer possible to solve this exercise due to changes on Google Maps." I was curious as to what made it impossible so I gave it a go. Also, telling me something is impossible is a bit like calling Marty McFly chicken... I just can't help myself...

![Nobody calls McFly chicken](chicken.jpeg)
/// caption
Nobody calls McFly chicken
///

Interesting parts of the solution:

- Getting the precise capture date of street view footage
- Using Wolfram Alpha for historical data
- Finding out that the reason why it's impossible is because Google have blurred out certain features in street view

## Locating

Given bio and the instruction to "locate".

What 3 words location. We use these at work to avoid the use of co-ordinates where we can.

![Twitter Location](locate.jpeg)

Let's convert back to co-ordinates so that we can

<https://gridreferencefinder.com/#ll=42.697324|23.343658|regard.diggers.edges|1>

![Grid reference finder](grf.jpeg)

Open in google maps. "Unfortunately it is no longer possible to solve this exercise due to changes on Google Maps."

```
!gm 42.697324, 23.343658
```

Nothing immediately obvious by scanning the map.

Zoomed into street view, again nothing immediately obvious. Date of the image is July 2014, so would have existed when challenge was posted.

![Street view date](gm_date.jpeg)

Only 1 date available, no other imagery. Quite old, had newer images been removed?

## Phase of the moon

I wondered if I could get the phase of the moon when the streetview was captured. Googled "get date of streetview". First result was <https://streetviewdate.com/>

Gave this result:

![Pano time of capture](pano_date.jpeg)

Loaded up wolfram alpha, which I normally use to find the weather at a specific point in time.

Gave this result:

![Phase of the moon](wa.jpeg)

"Waxing crescent moon"

## Table

| Blah | Blah  |
| ---- | ----- |
| Test | Test2 |
