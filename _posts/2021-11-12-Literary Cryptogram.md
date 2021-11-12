---
layout: post
title:  "Literary Cryptograms"
subtitle: "Ever wondered how complex crosswords and cryptograms are generated?  
		In this post I explain how to build instances of a particularly challenging puzzle using Integer Programming."
date:   2021-11-12
categories: optimization
background: '/img/crossword.jpg'
caption: 'Photo by <a href="https://unsplash.com/@alexlowenthal?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Alexandra Lowenthal</a> on <a href="https://unsplash.com/s/photos/crossword?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>'
---

# Sunday

It was a Sunday morning just like any other. I was having my coffee and casually reading the puzzle section of the newspaper when suddenly the question struck me: *how does the editor prepare the "Literary Cryptogram" every week?* 

Before proceeding, let me describe what exactly the puzzle looks like, since there are many different kinds of crosswords called "Literary Cryptogram". The one that I'm referring to is composed by a set of unknown words to guess disposed one after the other and aligned by its initial letter. Once you discover the words by using the given clues, the initials should form the title of some literary work. So far, nothing unusual or extraordinary. What caught my attention for the first time that morning was the final restriction: the blanks come with a mapping to a rectangular grid of white and black squares in such a way that if you transcribe the letters, an excerpt from the literary work should appear (check an example [here](https://resizer.glanacion.com/resizer/dwJ_HMdF4gkYBGmFKhmyAy33GxM=/768x0/filters:quality(80)/cloudfront-us-east-1.images.arcpublishing.com/lanacionar/SHL2AAQ73JGKXLJEEOMYBJ5Z5E.jpg), in Spanish).

The creator of this puzzle, Stanko Jerebic, was born in Slovenia and became interested in crosswords as a means of perfecting his Spanish after he moved to Buenos Aires, Argentina. Although he held a degree in Philosophy, he had several jobs ranging from carpenter to undertaker before becoming a full-time puzzle maker. According to the few notes about him I could find, his use of technology was limited to the digitization of its puzzles. The "Literary Cryptogram" stopped featuring regularly in the newspaper after his death in 2007. To this day, I have no clue what method he followed to prepare these peculiar crosswords. What I do know is how *I* would go about creating them: integer programming!

> **Note:** Seems interesting but too long? Head straight to my Github [repository](https://github.com/eugenusb/Cryptogram) to check the code.

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

You probably know that the knapsack problem is [NP-complete](https://en.wikipedia.org/wiki/NP-completeness), so you might be wondering: what good is IP? Although there is no known algorithm guaranteed to solve this problem faster than brute force in the worst-case scenario, the optimal solution for its *continuous* counterparts or relaxations (that is, the problem we obtain by removing the constraint that the variables be integers) can be *often* effectively obtained. Thus, by combining the branch and bound method with continuous relaxations and some magic tricks, we can expect to find optimal solutions for medium-sized IP problems (say, with up to roughly a hundred variables and constraints) in a reasonable amount of time. Of course, the true story is way more complex and nuanced than what I'm doing it justice, but the bottom line is: if you can express your particular NP problem as an IP problem of a modest size, it is definitely worth giving it a try armed with an IP solver.

# The model

Without further ado, let's create cryptograms! 

Imagine that we want to pay homage to *War and Peace* by Leo Tolstoy. How do we know if a particular passage gives rise to a cryptogram? After a moment's reflection, the question becomes: do there exist words such that if we sum the occurrences of each letter in all of them, they will exactly match the frequencies of letters from the passage? 
In other words, the excerpt is nothing but a set of letters ordered in a particular way; what we need is that this set of letters can be rearranged to form some English words too. This sounds somewhat similar to the knapsack problem, doesn't it?

Let's take a list of words $$w_1, \dots, w_n$$ (could be the whole dictionary or just some *interesting* words) and call $$o_{i,l}$$ the number of occurrences of letter $$l$$ in word $$i$$. If $$a_l$$ is the number of occurrences of letter $$l$$ in the excerpt, then we can model it as:

$$
	\begin{align*}
		\sum_{i=1}^{n} x_i \cdot o_{i,l} &= a_{l} \text{ for every letter } l \\
		x_i &\in \{0,1\}
	\end{align*}
$$

What about the initials? To satisfy that restriction, let's put $$b_{i,l} = 1$$ or $$0$$ according to whether the $$i-$$th word starts with letter $$l$$ or not and let $$c_l$$ be the number of occurrences of letter $$l$$ in the target phrase. For instance, if we wanted it to be *War and Peace*, it would be $$c_{a} = 3$$, $$c_{w} = 1$$ and so forth. Considering this, the constraints become:

$$
	\begin{align*}
		\sum_{i=1}^{n} x_i \cdot o_{i,l} &= a_{l} \text{ for every letter } l \\
		\sum_{i=1}^{n} x_i \cdot b_{i,l} &= c_{l} \text{ for every letter } l \\
		x_i &\in \{0,1\}
	\end{align*}
$$

And that's it! Ok, I lied, but we are almost there. 

The only missing component is the objective function. One possibility would be to choose a constant function to maximize as in $$\max \text{ } 1$$. The programs of this form only care about whether there exist *feasible* solutions, that is, solutions that satisfy the constraints. However, it would be better to assign a value to every word and try to generate the *best* possible cryptogram. A simple idea to define the value $$v_i$$ of word $$i$$ is to simply take the reciprocal of its relative frequency in books (the stranger, the better). These numbers can be obtained from [Google Ngrams Viewer](https://books.google.com/ngrams), and it was compiled in a simple list at this nice Github [repo](https://github.com/hackerb9/gwordlist).

$$
	\begin{align*}
		\max & \sum_{i=1}^{n} x_i \cdot v_i \\
		\text{s.to } \sum_{i=1}^{n} x_i \cdot o_{i,l} &= a_{l} \text{ for every letter } l \\
		\sum_{i=1}^{n} x_i \cdot b_{i,l} &= c_{l} \text{ for every letter } l \\
		x_i &\in \{0,1\}
	\end{align*}
$$

> **Note:** I consider this to be one of those rare problems which anyone acquainted with the basic tools can solve, but the mere fact that it is solvable comes as a pleasant surprise. That's why I enthusiastically proposed this as a final [project](https://cms.dm.uba.ar/academico/materias/2docuat2017/investigacion_operativa/2017/tp-operativa.pdf) at an Operations Research course a couple of years ago, certain that the students would appreciate the challenge. Boy, was I wrong...

# An example

Let's see how it works in a concrete example. Take the following quote from *War and Peace*:

> "If we admit that human life can be ruled by reason, then all possibility of life is destroyed"
>> **Leo Tolstoy, War and Peace**

We need to find 11 words (the number of letters in the title) in such a way that it is possible at the same time to write the title with its initials and the quote after reordering its letters. A run of the model looking for the most pretentious words satisfying the conditions produces the following:

$$\qquad$$ **W**ishfulfilment  
$$\qquad$$ **A**udile  
$$\qquad$$ **R**anty  
$$\qquad$$ **A**ridly  
$$\qquad$$ **N**ihili  
$$\qquad$$ **D**oost  
$$\qquad$$ **P**otboy  
$$\qquad$$ **E**stal  
$$\qquad$$ **A**sbestine  
$$\qquad$$ **C**haffered  
$$\qquad$$ **E**mbe  

Well, English is not my native language but it looks like a tad too pretentious. Running the solver again, but this time only checking for feasibility delivers:

$$\qquad$$ **W**heelless  
$$\qquad$$ **A**fter  
$$\qquad$$ **R**ummy  
$$\qquad$$ **A**fford  
$$\qquad$$ **N**ational  
$$\qquad$$ **D**isinhibition  
$$\qquad$$ **P**iyyut  
$$\qquad$$ **E**bbed  
$$\qquad$$ **A**llotted  
$$\qquad$$ **C**ase  
$$\qquad$$ **E**lfish  

which seems more reasonable.


<details style="color:gray">
	<summary markdown='span'>
		To those who lack Faith
	</summary>
	Reorder the letters in the words according to the indices between brackets to get the desired excerpt, starting from 0:  <br />
	<b>W</b>(2) i (0) s (37) h (10) f (1) u (14) l (18) f (20) i (7) l (29) m (6) e (3) n (17) t (8);   <br />
	<b>A</b> (4) r (27) i (19) d (5) l (45) y (33);  <br />
	<b>R</b> (34) a (11) n (24) t (9) y (57);  <br />
	<b>A</b> (16) u (28) d (31) i (51) l (46) e (21);  <br />  
	<b>N</b> (39) i (53) h (13) i (55) l (54) i (61);  <br />
	<b>D</b> (66) o (38) o (48) s (49) t (12);  <br />
	<b>P</b> (47) o (58) t (40) b (25) o (71) y (72);  <br /> 
	<b>E</b> (26) s (50) t (56) a (23) l (60);  <br />
	<b>A</b> (36) s (65) b (32) e (30) s (68) t (69) i (64) n (43) e (35);  <br />
	<b>C</b> (22) h (41) a (44) f (59) f (62) e (42) r (70) e (63) d (74);  <br />
	<b>E</b> (67) m (15) b (52) e (73).

</details>

# Wait, why does it work?

At the beginning, I was surprised by how the model effortlessly, every single time, found a solution for any *reasonable* combination of quote and title I threw in. However, it turns out that the probability that there exist solutions is **overwhelmingly** large.  

Since we are only interested in the frequency of the letters, it is natural to model the quotes as realizations of [multinomial random variables](https://en.wikipedia.org/wiki/Multinomial_distribution), where the number of trials $$n$$ is just the length of the excerpt and the probabilities $$p_a, p_b, \dots, p_z$$ are the relative frequencies of the letters in the English language. Of course, the same is true for modelling the selection of some words from the dictionary. And since the relative frequencies of letters in the dictionary are very similar to those in texts (see this Wikipedia [entry](https://en.wikipedia.org/wiki/Letter_frequency)), it is an acceptable approximation to use the same set of probabilities for both events. 

Let's return for a moment to the *War and Peace* example. The probability of the quote under the model I just described is approximately $$2.85 \cdot 10^{-16}$$. This means that if we were to sample from a multinomial distribution with the adequate parameters, we would expect to draw around $$\frac{1}{2.85 \cdot 10^{-16}} \simeq 3.51 \cdot 10^{15}$$ realizations to see the exact configuration corresponding to the quote. It may seem as a monstrous figure, but the number of ways to choose $$11$$ words (the number of letters in *War and Peace*) from a dictionary of $$64250$$ words is the combinatorial number $${64250}\choose{11}$$, which is greater than $$1.92 \cdot 10^{47}$$! 

Of course, not all of these combinations will be valid candidates. For instance, they need to satisfy the restrictions on the initials: that is, exactly one of them should start with "w", three with "a", and so forth. But even when imposing this condition, we are left with more than $$3.77 \cdot 10^{37}$$ configurations. It is necessary to further restrict the candidates to have the same total length as the quote, but I think you get the idea: the number of possible combinations from the dictionary is orders of magnitude larger than the probability of drawing the quote's configuration. 

Finally, in order to ensure that many of the possible sets of words have the combined length of the quote, we should take into account that the average length of the words in the dictionary (at least, the one I am using) is roughly $$\mu = 8.44$$, with standard deviation $$\sigma = 2.65$$. So, to be on the safe side, I usually consider quotes that have between 6 and 11 ($$\simeq 8.44 \pm 2.65$$) letters per each letter in the title. When the title is too short or too long to guarantee this condition, it is common to use the name or initials of the author. The heuristics behind this design decision is that the combined length of $$K$$ words should follow approximately a normal distribution $${\mathcal N}(K \cdot \mu, \sqrt{K} \cdot \sigma)$$ by the Central Limit Theorem, provided that $$K$$ is large. In practice, this means that every length in the range $$[K \cdot \mu - \sqrt{K} \cdot \sigma, K \cdot \mu + \sqrt{K} \cdot \sigma]$$ is achieved by a sizeable proportion of combinations of $$K$$ words from the dictionary.