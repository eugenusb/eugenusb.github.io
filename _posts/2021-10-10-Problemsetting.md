---
layout: post
title: "Problemsetting"
subtitle: "Preparing problems for math mid-term exams can be an excruciating task if you don't want to copy exercises from a book. Unfortunately, I own no secret formula for coming up with cool exercises, but I can show you a couple of ideas accompanied by examples."
date:  2021-10-10
categories: math
background: '/img/problems_draft.jpg'
caption: 'Backstage of creating problems.'
---

# The challenge

A couple of years ago, I was assigned as Teacher Assistant to the *Advanced Calculus* course for second-year math students. There is a wonderful textbook for this course, *Espaços Metricos* by Lages Lima (*Metric spaces*, original in Portuguese) which contains an awful lot of interesting exercises. When the time to prepare the exams arrived, I was faced with the seemingly impossible task of coming up with exercises that:

+ Didn't appear in Lima's book, which most students read and know,
+ Didn't appear in a previous exam, since student got access to statements and resolutions of many old exam,
+ And were appropriate and meaningful.

I feel that I kind of succeeded, but I'll let you decide!

# Exercises and techniques

As I anticipated, this post is far from pretending to be a guide to problemsetting. I will content myself with presenting some hacks to come up with problem statements and a concrete example for each of them. So, without further ado, here we go!

**Idea.** One of the most fascinating facts to discover during the course is, at least to me, that metric spaces which are difficult to visualize behave in many ways exactly like our familiar Euclidean spaces. Hence, many times you can formulate an easy problem, yet not devoid of interest, just by taking a known result from real numbers and ask for the analogue in say, a function space. More generally, this principle can be summarized as something like *sometimes mundane facts are surprising when viewed in a more abstract setting.*

**Example.** Let $$X$$ be a metric space and let

$$
	{\mathcal B}(X,\mathbb{R}) := \{f: X \to \mathbb{R} \text{, bounded and continuous}\}
$$

equipped with the infinity norm. Let $$(f_n)_{n \in \mathbb{N}} \subseteq {\mathcal B}(X,\mathbb{R})$$ be a sequence of functions such that $$\sum_{n \geq 1} f_n$$ converges in $${\mathcal B}(X,\mathbb{R})$$. Prove that $$(f_n)_{n \in \mathbb{N}}$$ converges to the zero function $$0 : X \to \mathbb{R}$$ in $${\mathcal B}(X,\mathbb{R})$$.

<details style="color:gray">
	<summary>Hint</summary>
	My students had a tougher time with this exercise than I anticipated. Simply remember how you can prove the analogous fact for real sequences.
</details>

**Idea.** Ok, this one is really a classic, but I have to mention it: generalize a particular case. A problem from one of the course's assessment asked proving that the set $$\{ f \in C[0, 1]: f(x) \neq 0 \text{ for all } x \in[0, 1]\}$$ is open and to compute its connected components. As a result,...

**Example.** Let $$E$$ be a normed real vector space and $$S \subseteq E$$ a closed hyperplane. Prove that $$E \setminus S$$ has exactly two connected components.
<details style="color:gray">
	<summary>Hint</summary>
	Since \(S\) is a hyperplane, there exists a vector \(v \in E\) such that every \(x \in E\) can be uniquely expressed as \(x = s + \lambda \cdot v\) for some \(s \in S\), \(\lambda \in \mathbb{R}\). Intuitively, the pieces of the space \(E \setminus S\) will be formed by the \(x\) with \(\lambda > 0\) on one hand and those with \(\lambda < 0\) on the other. To formalize this, the fact that the map \(x \in E \to \lambda \in \mathbb{R}\) is a continuous linear functional (its kernel is closed) is helpful.
</details>

**Idea.** Think of variants of problems you read. Why the hypotheses are necessary? What happens if you change some of them?

**Example.** Let $$E$$ a normed real vector space and $$S \subseteq E$$ a hyperplane that is **not** closed. Prove that $$E \setminus S$$ is connected.

<details style="color:gray">
	<summary>Hint</summary>	
	Ok, so now we know that both the "positive" and "negative" semiplanes (let's call them \(S_{+}\) and \(S_{-}\)) with respect to \(S\) are connected. Suppose that \(E \setminus S\) is not connected, take a disjoint union of open sets \(U, V\) in \(E\) that intersect non-trivially \(E \setminus S\) and show that \(S_{+} \subseteq U\) and \(S_{-} \subseteq V\) (or vice-versa, it doesn't matter). Derive a contradiction using the fact that \(S\) is not closed.
</details>

**Idea.** Take a well-known and sufficiently general result. For instance, any fact concerning convergence of sequences is widely applicable in metric spaces. Then, embed it in another area of the theory. For instance, continuity of functions.

**Example.** Let $$X$$ be a compact metric space and let $$F \subseteq C(X,\mathbb{R})$$, considered as a metric space with the infinity norm. Let us suppose that every $$f \in F$$ has a unique maximum at $$x_{f} \in X$$. Prove that the map $$f \in F \to x_{f} \in X$$ is continuous.

<details style="color:gray">
	<summary>Hint</summary>
	This is a trick that I didn't fully appreciate when I was a student. Let's take a convergent sequence \(f_n \to f\) in \(F\). We would be done if we could prove that the corresponding sequence of maxima \(x_n\) converges to the maximum \(x\) of \(f\). If we knew that \(x_n\) converges, then it would be clear that the limit point is \(x\). Since \(X\) is compact, we know that some subsequence of \(x_n\) converges to \(x\). If we repeat the reasoning with any subsequence of \(x_n\), we get our result.
</details>

**Idea.** Solve old mid-term exams. Can you solve them using two genuinely different strategies, or via a method that is not the expected one? If the answer is yes, you have an edge.

**Example.** Let $$Lip_M^0[a,b] := \{ f:[a,b] \to \mathbb{R} : |f(x) - f(y)| \leq M|x -y| \text{ , } f(a) = 0\}$$ for a given $$M > 0$$. Let $$(f_n)_{n\in \mathbb{N}} \subseteq Lip_M^0[a,b]$$, $$f \in Lip_M^0[a,b]$$. Prove that $$\int_{0}^{1} |f_n(x) - f(x)| \to 0$$ implies $$\|f_n - f\|_{\infty} \to 0$$.
<details style="color:gray">
	<summary>Hint</summary>
	Probably, the easiest way to prove this is by using Arzela-Ascoli theorem. It is not difficult to show that the sequence \((f_n)_{n \in \mathbb{N}}\) should be uniformly convergent and in this case, \(f\) is the only possible candidate. However, it could also be solved by hand by noting that for any given \(\delta > 0\) and \(x \in [a,b]\),
	
	$$
		\begin{align*}
			& |f_n(x) - f(x)| \cdot \delta = \int_{x}^{x+\delta} |f_n(x) - f(x)| \, dt \leq \\
			& \int_{x}^{x+\delta} |f_n(x) - f_n(t)| \, dt + \int_{x}^{x+\delta} |f_n(t) - f(t)| \, dt + \int_{x}^{x+\delta} |f(t) - f(x)| \, dt \leq \\
			& M \int_{x}^{x+\delta} |x - t| \, dt + \int_{0}^{1} |f_n(t) - f(t)| \, dt + M \int_{x}^{x+\delta} |t - x| \, dt
		\end{align*}
	$$

	and some additional computations.
</details>

**Bonus:** Last year I was in charge of preparing the exams for the Differential Geometry course. I wasn't very inspired, but I tried to keep an open mind. Then, I came upon this beautiful [video](https://www.youtube.com/watch?v=AmgkSdhK4K8) by [3Blue1Brown](https://www.youtube.com/channel/UCYO_jab_esuFRV4b17AJtAw) and all of a sudden the first exercise for the exam appeared in front of my eyes!

**Example.** Let $$c:(0,1) \to \mathbb{R}^2$$ be a smooth injective curve. Let's suppose that it satisfies the following condition: for every $$x \in (0,1)$$, no secant line is parallel to $$c'(x)$$, that's to say, $$c(y) - c(x)$$ and $$c'(x)$$ are linearly independent for each $$y \in (0,1)$$, $$y \neq x$$. We'll say that three **distinct** points $$c(x), c(y), c(z)$$ form an ordered rectangular triangle if the set $$\{ c(x), c(y), c(z) \}$$ forms a rectangular such that the segments $$\overline{c(x)c(z)}$$ and $$\overline{c(y)c(z)}$$ are orthogonal. Let $$R$$ be the set of ordered rectangular triangles over the curve. Prove that if $$R$$ is non empty, it has a natural smooth manifold structure and compute its dimension.

<details style="color:gray">
	<summary>Hint</summary>
	Provided it is not empty, the set \(R\) can be parameterized by three distinct points \(x, y, z \in (0,1)\) so that the vectors \(c(y) - c(x)\) and \(c(z) - c(x)\) are orthogonal. In other words, \(R\) is simply the zero set of the map \(f:Conf_{3}((0,1)) \to \mathbb{R}\) given by

	$$
		f(x,y,z) = \langle c(y) - c(x), c(z) - c(x) \rangle,
	$$

	where for a manifold \(M\) and an integer \(k\), \(Conf_{k}(M) = M^{k} \setminus \cup_{i \neq j} \{x_i = x_j\}\) is the configuration space of \(M\). Hence, it is enough to show that \(0\) is a regular point of the smooth map \(f\), which follows easily upon analyzing the zeros of its differential:

	$$
		D_{(x,y,z)}f = (\langle c(y) - c(x) + c(z) - c(x), c'(x)\rangle, \langle c(z) - c(x), c'(y) \rangle, \langle c(y) - c(x), c'(z)\rangle).
	$$

</details>