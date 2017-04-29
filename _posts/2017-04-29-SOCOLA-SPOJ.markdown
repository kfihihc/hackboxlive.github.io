---
layout: post
title:  "SOCOLA problem - SPOJ"
date:   2017-04-29 13:36:00 +0530
---

I spent almost two days on this problem <a href="https://www.spoj.com/problems/SOCOLA">SOCOLA</a> and now that I figured out the solution somehow after going through many resources and the beautiful insight from my friend, <a href="https://www.github.com/paramsingh">Param Singh</a>, I think this problem deserves an explanation.

In an instant, one could tell this problem involves DP+bitmasking, but the question is what should be the state of this DP.

Well, the first solution that came to my mind was, given a dimension of the chocolate, can we fit a certain mask of the friends into that chocolate. This solution was perfect in its own way, but then the dp matrix would have been of the size 100X100X(2^15) = 327680000 bytes which I thought would not get passed on the judge. Even if we overlook the size constraint, to calculate each of the states you need an extra O(n)~100 computation or so (all of the above calculations can be wrong, as it is being written in an after-sleep mode and if someone finds a solution using this way, please do let me know), so I just dropped this idea.

After googling, I came across this <a href="http://www.csc.kth.se/~austrin/icpc/finals2010solutions.pdf">resource</a>, which stated this:

>This problem can be solved with dynamic programming. A good state is the following: given a subset S ⊆ [n] of friends and a desired width w, is it possible to split some chocolate bar of width w among the friends in S? Note that the height h of such a chocolate bar must necessarily be the sum of the demands of the friends in S divided by w (and if this is not an integer the answer must be “No”). To compute whether a state (S, w) has answer yes, we iterate over all non-empty proper subsets 0 != T ⊂ S. For each such T, we try cutting the w × h bar either vertically or horizontally, putting T in one part and S \ T in the other part. The position to cut the bar is determined by the set T (and some T can make the cut impossible, e.g., if the sum of demands in T is not a multiple of w then no horizontal cut can be made).

