---
layout: post
title: "Midnight Code Cup"
date: 2026-04-12
tags: [competitive programming]
---

Long time ago, I participated in programming competitions like national olympiad in informatics, ACM ICPC, TopCoder, and so on. One of the most fun events I took part in was Challenge24: a mix of algorithmic and weird optimization problems, sometimes involving image, audio, and video analysis. The coolest part of it was the on-site finals in Budapest which lasted 24&nbsp;hours. Unfortunately, it does not exist anymore. But few months ago I learned about [Midnight Code Cup](https://midnightcodecup.org/), a similar by spirit competition. It's organized mostly by people from Russian competitive programming community, I even know some of the organizers. The online qualification round was yesterday, here are my impressions on it. TLDR we didn't make it to the finals :D

<!--more-->


I needed two more people to make a team. First, I asked my good old friend Anton from school/university era. He had some experience with competitive programming, but forgot everything long ago. Just like me. Then, I reached my former colleague [Marci](https://marci.gunyho.com/) who did his PhD in our group at Aalto. He just likes weird programming stuff so I thought it would be no big deal convincing him. Since two of us had no practice in more than a decade and one had only a vague idea of what it's going to be, we didn't have high expectations :D

The cool thing about this contest is that almost everything is allowed: as many computers as you have access to, any software, including AI. There are only two forbidden things: discussing problems outside of the team and breaking the system intentionally. There were [three problems](https://codeforces.com/gym/106478) in the qualification round.

#### Problem A

The first one was some scheduling nightmare: traveling salesman problem on steroids. You're given a graph of cities and a schedule of salesmen doing their own trips. Each of the salesmen pops up each at their own time in their own city and they need to get to their own destination. You need to arrange a train schedule between the cities to minimize some cost function based on the traveling time and number of changes. Some horrendous optimization with ugly constraints, I'm glad Anton took this problem and I didn't need to worry about it.

#### Problem B

The second problem was conceptually much simpler. There is a bitmap picture, each pixel is either white or black. You're landed somewhere on this picture and can move left, right, up, or down. If you try to leave the picture, you stay on the place. You need to come up with as short as possible sequence of moves, such that regardless of the starting point you gonna visit at least one black pixel.

Marci decided to take this problem. He vibe coded some algorithm which does the job at least in some way and then tried to improve results with manual heuristics (the test pictures were given in advance, so that was possible). I also did a little bit of heuristics when I distracted myself from problem C. For the test in the pic below I came up with super naive approach ```RRURRURRU...``` which scored next to the best among all the solutions for that test :D

<figure>
  <img src="{{'/images/mcc-05.png' | relative_url}}" style="max-width:50%; height:auto; display: block; margin: 0 auto;" alt="Cool test">
  <!--figcaption style="text-align:center;font-size:0.9em;">
  </figcaption-->
</figure>

#### Problem C

This problem was within the domain of my responsibility. It also had a simple statement: there is a randomly-generated maze on a 125\\(\times\\)125 grid, each cell is either empty or occupied by an obstacle. A single randomly chosen empty cell has a *prize*. You are allowed to make 10000 queries in a format "row column": the answer to the query contains -1, if this cell has an obstacle; otherwise you're given the shortest distance to the *prize* from the queried cell, if moving by empty cells. If you find the *prize*, you score a point, after that the *prize* moves randomly to some empty cell. You're also given the code of the maze generator.

First thing I did, I fed the statement of the problem and code of the generator to the AI agent (GPT&nbsp;5.4 in my case). It told me that all the cells with odd-odd coordinates are empty, all the even-even cells are obstacles, and graph of empty cells makes a tree. So it suggested just to query all the uncertain cells (odd-even, even-odd) to make a complete map of the maze, and then the real guessing starts. Stupid me, I followed this idea and realized only at by the end that the makes construction eats almost 80% of all the queries :D

After that, I used minimax heuristics. I calculated all-to-all distances. For each cell \\(v\\) and positive \\(k\\) I define \\(f(v, k)\\) as number of cells with distance \\(k\\) from v, and \\(g(v) := \max_k f(v, k)\\). After some query I have a list of candidates for the cell with the *prize* in it. Then, my next query is the candidate \\(v\\) with the minimal \\(g(v)\\). After I found the *prize*, I query the center of the tree. It turned out to be reasonably good, and found the *prize* in 3-4 queries on average. But the total score was quite poor, since I spend big share of the queries on the map construction.

Another issue was that this problem was interactive and I needed to send the code. My favorite language Julia was not supported in this contest and I totally forgot python and C++ which I used in programming competitions, so I outsourced writing the code to the AI agent. Attempts to write something on my own turned out to be a disaster.


In retrospective, I should have came up with some idea of constructing the maze on the fly for problem C. Also, for problem B it seemed to be better not to code a general solver but use a dedicated heuristic for each test. Maybe even to feed each picture to the AI and see what it says. But it was still fun, at least for me.

Fun fact: we all used different AI agents, I had GPT&nbsp;5.4, Anton had copilot, and Marci had Claude. By the end of the contest Marci had spent all the tokens available in his plan :D
