---
layout: post
title:  "Literary Criptograms"
date:   2021-10-11
categories: optimization
---

# Sunday

It was a Sunday morning just like any other. I was having my coffee and casually reading the puzzle section of the newspaper when suddenly the question struck me: *how does the editor prepare the "Literary Criptogram" every week?* 

Before proceeding, let me describe what exactly the puzzle looks like, since there are many different kinds of crosswords called "Literary Cryptogram". The one that I'm referring to is composed by a set of unknown words to guess disposed one after the other and aligned by its initial letter. Once you discover the words by using the given clues, the initials should form the title and author of some literary work. So far, nothing unusual or extraordinary. What caught my attention for the first time that morning was the final restriction: the blanks come with a mapping to a rectangular grid of white and black squares in such a way that if you transcribe the letters, an excerpt from the literary work should appear.

The creator of this puzzle, Stanko Jerebic, was born in Slovenia and became interested in crosswords as a means of perfecting his Spanish after he moved to Buenos Aires, Argentina. Although he held a degree in Philosophy, he had several jobs ranging from carpenter to undertaker before becoming a full-time puzzle maker. According to the few notes about him I could find, his use of technology was limited to the digitalization of its puzzles. The "Literary Cryptogram" stopped featuring in the newspaper after his [death](https://www.lanacion.com.ar/cultura/stanko-jerebic-nid948897/) in 2007. To this day, I have no clue what method he followed to prepare these peculiar crosswords. What I do know is how *I* would go about creating them: integer programming!

> **Disclosure:** Some months after that morning, I discovered that Steve Morse had independently described the same technique I will discuss below for a similar problem [here](https://stmorse.github.io/journal/IP-Crossword-puzzles.html), so you might want to check his interesting post as well!

# Integer Programming

So what's an integer programming (IP) problem? Quite simply, it is a constrained optimization problem in which both the objective function and the constraints are linear in the decision variables, which can only take **integer** values. Let's see what this means in a concrete example. The knapsack problem is a very well-known combinatorial optimization problem which requires maximizing the total value of items that can be chosen without exceeding the capacity $$W$$ of the knapsack. If we have $$n$$ items with values $$\{v_i\}$$ and weights $$\{w_i\}$$, it is clear that the problem can be formulated as:

$$
	\begin{align*}
		\max & \sum_{i=1}^{n} x_i \cdot v_i \\
		\text{s.to } & \sum_{i=1}^{n} x_i \cdot w_i \leq W \\
			& x_i \in \{0,1\}
	\end{align*}
$$

You probably know that the knapsack problem is [NP-complete](https://en.wikipedia.org/wiki/NP-completeness), so you might be wondering: what good is IP? Although there is no known algorithm guaranteed to solve this problem faster than brute force in the worst-case scenario, the optimal solution for its *continuous* counterparts or relaxations (that is, the problem we obtain by removing the constraint that the variables be integers) can be *often* effectively obtained. Thus, by combining branch and cut with continuous relaxation and some more magic, we can expect to find optimal solutions for medium-sized IP problems (say, with up to roughly a hundred variables and constraints) in a reasonable amount of time. Of course, the true story is way more complex and nuanced than what I'm doing it justice, but the bottom line is that if you can express your particular NP problem as an IP problem of a modest size, it is definitely worth giving it a try armed with an IP solver.

# The model

Without further ado, let's create cryptograms! Let's say that we want to pay homage to *War and Peace* by Lev Tolstoy. How do we know if a particular passage can give rise to a cryptogram? After a moment's reflection, the question becomes: do there exist words such that if we sum the occurrences of each letter in all of them, they will exactly match the frequencies of letters from the passage? 
In other words, the excerpt is nothing but a bag of letters ordered in a particular way; we need that this bag of letters can be rearranged to form some English words too. 

> **Note:** I consider this to be one of those rare problems which anyone acquainted with the basic tools can solve, but the mere fact that it is solvable comes a pleasant surprise. That's why I enthusiastically proposed this as a final [project](https://cms.dm.uba.ar/academico/materias/2docuat2017/investigacion_operativa/2017/tp-operativa.pdf) at an Operations Research course a couple of years ago, certain that the students would appreciate the challenge. Boy, was I wrong...

# Code
