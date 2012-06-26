---
title: Piles of Data
layout: post
---

Reading [Dumped on by Data][1] has brought, first and foremost, a huge problem
facing the Internet-connected world of today: now that we have all the data we
could ask for, how do make sure we can store it and make sense of it all?

In any university research I've done, collecting digital data *and* in getting it into
 the "correct" format for future work
is a huge percentage of the work that needs to be done in research in all fields.
With the advent of the Internet, we have the potential to form
connections between disparate data points like never before, but often times the
time cost of munging this in the correct format, bandwidth, and political / IP
concerns, and data trust issues prevent this data from being distributed to a wider audience. Some of
the worst offenders are those that receive federal aid for their research, and
then go on to not provide any publicly-accessible raw data from their findings.
[Reproducibility][2] is a major component of any valid scientific experiment, but
this is impossible unless any and all third-parties have access to original sources to
 validate experiments.

Almost inexcusable in this day and age is being limited by physical hard
disk space. In some cases, this results in a willful deletion of raw data to
comply with some antiquated IT / business policy for a given institution. I
believe this is where the possibilities of cloud computing really shine -- as an
unlimited, persistent, durable resource. Google's technological advances are
made possible by their pervasive use of
[BigTable][3] internally, allowing a clear separation of concerns between those
doing the data collection vs. those doing data persistence (which is too often
shoveled off to the same person in a university setting). This facilitates
technologies like their near amazing real-time [Dremel][4] distributed query &amp;
analytical tool.

Imagine this technology on a much larger scale -- one that's
used by large portions of the university research community. Right now, if a
statistician wants to get historical weather data for a given region in time,
it's available to a large extent on [NOAA's site](http://www.ncdc.noaa.gov/oa/ncdc.html).
Sometimes, however, data is missing from readings and in general might need to be
normalized, which researchers end up doing independently and can't/won't share
their data due to aforementioned reasons.

Of course, even if suddenly all the data in world was instantly easily
accessible, getting everything into cooperative formats is huge effort in itself.
This is partially mitigated at companies like Google, as there can be some
top-down guidance for standardizing data formats. Determining a canonical data
format is an open question when one is dealing with a heterogeneously-owned
environment like a research community, but scientists have been good about cooperating
on standards given decent proposals (e.g., Berkeley sockets) and value.
[The Long Now][5] project is partially concerned with this question of
finding future-proof canoncial formats for our data today.

Like any good technology, this theoretical ubiquitous persistence and sharing of
data only becomes pervasive when two things happen -- 1:
anyone can use it, and 2: no one really has to put any cognitive effort into
making it work. Managing data has a long way to go in this regard, but it's
great to see companies out there making this something realizable.

[1]: http://chronicle.com/article/Dumped-On-by-Data-Scientists/126324/
[2]: http://news.ycombinator.com/item?id=2210945
[3]: http://labs.google.com/papers/bigtable.html
[4]: http://research.google.com/pubs/pub36632.html
[5]: http://longnow.org/

