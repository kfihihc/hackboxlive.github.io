---
layout: post
title:  "SOCOLA problem - SPOJ"
date:   2017-04-29 13:36:00 +0530
---

I spent almost two days on this problem <a href="https://www.spoj.com/problems/SOCOLA">SOCOLA</a> and now that I figured out the solution somehow after going through many resources and the beautiful insight from my friend, <a href="https://www.github.com/paramsingh">Param Singh</a>, I think this problem deserves an explanation.

In an instant, one could tell this problem involves DP+bitmasking, but the question is what should be the state of this DP.

Well, the first solution that came to my mind was, given a dimension of the chocolate, can we fit a certain mask of the friends into that chocolate. This solution was perfect in its own way, but then the dp matrix would have been of the size 100X100X(2^15) = 327680000 which I thought would not get passed on the judge. Even if we overlook the size constraint, to calculate each of the states you need an extra O(n)~100 computation or so, (all of the above calculations can be wrong, as it is being written in an after-sleep mode and if someone finds a solution using this way, please do let me know)