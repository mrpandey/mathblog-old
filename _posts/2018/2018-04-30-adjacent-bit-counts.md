---
layout: post
title: "SPOJ Problem: Adjacent Bit Counts"
show_updated: false
updated_on: 2018-04-30 00:00:00 +0530
---

### A Little Background

I was trying to solve [this](https://www.spoj.com/problems/GNYR09F/) problem on SPOJ. It's a dynamic-programming problem. I tried to find some recursive relation. I spent hours. But no breakthrough.

I even tried looking for hints in comments and I came to know it's a 3D dynamic-programming problem. Some of the users were even able to reduce it to a 2D technique.

Then I realized I need to rethink the entire approach. So somehow, I started treating it like a combinatorics problem and I landed on a solution — a formula that guarantees linear time execution. I love moments like these when you solve an interesting problem with an uncommon approach. And that's why I am writing this post.
<!--end_excerpt-->

### The Problem

The problem involves dealing with bit strings. A bit string is basically a sequence where each term can have value either $$0$$ or $$1$$.

Usually, bit strings are represented without commas. So the bit string $$1,0,0,1,0$$ is abbreviated as $$10010$$.

Now we define a function $$f(s)$$ which takes a bit string $$s = x_1 x_2 \ldots x_n$$ and gives the output:

$$
  f(s) = x_1 x_2 + x_2 x_3 + \ldots + x_{n-1} x_n
$$

This function is what they call the adjacent bit count of $$s$$. This function basically tells us the number times the bit string $$11$$ appears in $$s$$.

E.g.   $$f(101)=0$$,   $$f(1011)=1$$,   $$f(111)=2$$,   $$f(110111)=3$$.

Now we're ready to state our problem.

Given positive integers $$n$$ and $$k$$, find the number of bit strings $$s$$ of length $$n$$ which satisfies $$f(s)=k$$.

For example, let $$n=5$$ and $$k=2$$. The possible bit strings are $$11100$$,   $$01110$$,   $$00111$$,   $$11011$$,   $$10111$$,   $$11101$$. So our answer is $$6$$.

### Solution

We are given the fixed values $$n$$ and $$k$$. We introduce a variable value $$a$$ which we define as the number of continuous blocks of 1-bits in a bit string. Now onwards, we will refer to this as 1-block.

For example, $$101110011$$ has $$a=3$$ and $$1010101$$ has $$a=4$$.

If we fix the value of $$a$$, our problem becomes simpler as we will see later. We then have to calculate adjacent bit count for each possible value of $$a$$ and then sum them to obtain our final answer.

Before we jump to the actual solution, we need to observe a few things.

Let $$m_1, m_2, \ldots ,m_a$$ be the number of 1-bits in the $$a$$ 1-blocks from left to right respectively. E.g., for $$101101$$ we have $$m_1 = 1$$, $$m_2 = 2$$ and $$m_3=1$$.

Note the following points:

In a 1-block containing $$m_i$$ 1-bits, the bit string $$11$$ appears $$m_i - 1$$ times. This gives adjacent bit count for a single 1-block. So for the entire bit string, we must have $$k = \sum_{i=1}^{a}\left(m_i -1\right) = {\sum m_i }-a$$. So we get

$$
  \text{number of 1-bits} = {\sum m_i} = k+a
$$

Since a bit string consists of only zeros and ones, we get

$$
  \text{number of 0-bits} = n-\sum m_i = n-k-a
$$

Since $$k \geq 1$$, we must have at least one 1-block, and hence $$a\geq1$$. Also note that we must have at least $$a-1$$ 0-bits for separating the 1-blocks. This gives $$a-1 \leq n-k-a \Rightarrow a \leq \frac{n-k+1}{2}$$. So the range of $$a$$ is

$$
  1 \leq a \leq \left[\frac{n-k+1}{2}\right]
$$

where $$\left[x\right]$$ denotes the floor function.

Now we move to the actual solution.

For a fixed value of $$a$$, we employ [Beggar's Method]({% post_url 2018/2018-04-29-beggars-method %}) to our rescue. We take an empty bit string and break our problem into two parts.

Number of ways to fill 1-bits in the bit string: We have $$k+a$$ 1-bits, which we want to distribute in $$a$$ 1-blocks, which are currently empty.
Number of ways to fill 0-bits in the bit string: We have $$n-k-a$$ 0-bits, which we want to distribute in the gaps between the 1-blocks.

So, for fixed $$a$$, our answer will be the product of above two values. Our final answer will be sum of these products.

#### Filling 1-bits

Imagine our $$a$$ 1-blocks as $$a$$ empty buckets lined up in a row from left to right, with their positions fixed. We need to find the number of ways we can distribute $$k+a$$ available 1-bits in them.

But we have to keep in mind that none of the bucket remains empty. So first, lets put one 1-bit in each of the buckets. Now, we have to distribute the remaining $$k$$ 1-bits in whatever way we wish.

Let $$m_1, m_2, \ldots ,m_a$$ be the distribution of remaining $$k$$ 1-bits in the buckets from left to right, respectively.

$$
  m_1 + m_2 + \ldots + m_a = k, \quad m_i \geq 0
$$

By Beggar's Method, we get

$$
  \text{number of ways to fill 1-bits} = {^{k+a-1}C_{a-1}} \tag{1}
$$

#### Filling 0-bits

We have $$a$$ buckets, which gives us $$a+1$$ gaps to fill zeros in.

The first and the last gaps can remain empty. But the inner $$a-1$$ gaps must have at least one 0-bit, otherwise we will have less than $$a$$ 1-blocks which contradicts that we have $$a$$ blocks.

So we distribute one 0-bit in each of the inner gaps. Now we can freely distribute the remaining $$n-k-a-\left(a-1\right) = n-k-2a+1$$ 0-bits in $$a+1$$ gaps.

Let $$x_1, x_2, \ldots , x_{a+1}$$ be the distribution of remaining 0-bits in gaps. Then we have to solve

$$
  x_1 + x_2 + \ldots + x_{a+1} = n-k-2a+1, \quad x_i \geq 0
$$

Applying Beggar's Method, we get

$$
\begin{align}
  \text{number of ways to fill 0-bits} & = {^{\left(n-k-2a+1\right)+\left(a+1\right)-1}C_{\left(a+1\right)-1}}\\
  & = {^{n-k+1-a}C_{a}} \tag{2}
\end{align}
$$

#### Final Answer

As discussed earlier, our final answer is the summation of product of the binomial coefficients obtained in $$\left(1\right)$$ and $$\left(2\right)$$.

$$
  \sum_{a=1}^{\left[\frac{n-k+1}{2}\right]} {^{k+a-1}C_{a-1}} \cdot {^{n-k+1-a}C_{a}} \tag{3}
$$

### Conversion To Code

To achieve linear runtime, it's clearly visible that we have to run a for loop on $$a$$, the number of 1-blocks. In each pass of loop we have to calculate the product of binomial coefficients in constant time.

But the usual dynamic programming method takes $$\mathcal{O}\left( q^2 \right) $$ time for calculating $$^{q}C_{r}$$. But we can do it in constant time if we know the binomial coefficients in last pass. We do it using the following formula

$$
  ^{q}C_{r} = \frac{q}{r} \cdot {^{q-1}C_{r-1}} \tag{4}
$$

Applying the above formula on the binomial coefficient in $$\left(1\right)$$, we get

$$
  {^{k+a-1}C_{a-1}} = \frac{k+a-1}{a-1} \cdot {^{k+\left(a-1\right)-1}C_{\left(a-1\right)-1}} \tag{5}
$$

For the binomial coefficient $$\left(2\right)$$, we substitute $$p=n-k+1$$ so as to make it look cleaner. $${^{n-k+1-a}C_{a}}$$ becomes $${^{p-a}C_{a}}$$. Using identity $$\left(4\right)$$ on this, we get

$$
  {^{p-a}C_{a}} = \frac{ \left(p-2a+1\right) \left(p-2a+2\right) }{ a \left(p-a+1\right) } \cdot {^{p-\left(a-1\right)}C_{a-1}} \tag{6}
$$

Note that the initial values of these coefficients i.e. when $$a=1$$, are $$1$$ and $$n-k$$ respectively. Keeping this in mind and using equations $$\left(3\right)$$ and $$\left(6\right)$$, this is what our final code solution looks like.

{% highlight cpp %}
long long x=n-k, y=1, p=n-k+1, sum=0;
sum += x*y;

for(int a=2; a<=p/2; a++){
	x = (x*(p-2*a+1)*(p-2*a+2))/(a*(p-a+1));
	y = (y*(k+a-1))/(a-1);
	sum += x*y;
}
{% endhighlight %}

As you can see, the for loop runs $$p/2$$ times. Hence, the time complexity of program is $$\mathcal{O}\left(n-k\right)$$.

You can find the complete program [here](https://github.com/mrpandey/spoj/blob/master/solved/GNYR09F.cpp).
