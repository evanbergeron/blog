---
layout: post
title: A Turing Machine Quine
date: 2015-03-13
---

Today, we'll talk about something quite exciting. We define a Turing machine that prints its own source code. This construction offers us insight into how one may construct quines in any programming language.

First, some quick definitions. A quine is a program that prints itself. At first this may seem impossible! A first attempt in python may look something like

{% highlight python %}
print "print"
{% endhighlight %}

But wait. We missed the first print. So perhaps we'll add another print? But then we have

{% highlight python %}
print "print 'print'"
{% endhighlight %}

and we have the problem we started with. Let's revisit this is a moment.

A Turing machine is an abstraction of a computer. It has some finite number of states, transitions between those states, and infinite memory. Excitingly, this turns out to be a quite reasonable definition of computation. There's a very important result in computer science called the Church-Turing Thesis, which basically says that anything your-programming-language-here can do, so can a Turing machine.

Consequently, offering a Turing machine quine is a way of offering a quine for every programming language! We'll find that it's actually quite instructive to talk about quines in the abstract first, before moving into specific programming languages.

Right. So let's get started. We present the following lemma:

There is a computable function q, where if w is some string, q(w) is a description of a Turing machine that prints out w and halts.

We offer the following TM as a construction of this function:

{% highlight python %}
Q = "On input string w:
    1. Construct the following TM P_w:
        P_w = 'On any input:
            1. Erase the input
            2. Write w to the tape
            3. Halt'
    2. return P_w"
{% endhighlight %}

So our TM Q takes a string w and outputs a TM that prints w. Perfect! Exactly what we wanted. We'll see why this is useful in a moment. Remember, this TM acts as a definition of a computable function, which we called q.

With this lemma in hand, we proceed to the main task: building a TM that prints itself. We'll split the machine up into two parts - A and B. Let's start with a description for A.

A's description depends on B, so let's assume we've written B. Remember the function q that we wrote earlier? We define A to be q(B). Which is to say, A is just a TM that, on any input, just prints a description of part B. This depends on our definition of B, so let's talk about that now.

B's the second and last part of the program, so at the end, we should have printed a full description of AB. By the time we get to B, A just ran, leaving a copy of B's source code sitting on the tape. Which means at this point, B has a description of itself. So then how do we get a description of A?

Here's the trick: we apply q to our description of B. By our definition, q(B) is a TM that, on any input, prints a copy of B. This was exactly our definition of part A!  So B takes its own source code and applies q to it, obtaining a description of A. Then B outputs AB, completing the proof.

To summarize:

{% highlight python %}
QUINE = "On input string w:
    1. A = q(B)
    2. B = 'On input M, where M is a part of a TM:
        1. return q(M) + M'"
{% endhighlight %}

Using this proof as a template, let's consider how we would write a quine in python. As before, let's consider part A first. Part A needs to give B a copy of B's source code. In the TM model, this was achieved by leaving a copy of B's description on the tape.

In python, we can just assign into a variable to achieve the same effect. So our part A should look something like

{% highlight python %}
b = "b's source code here"
{% endhighlight %}

Part B should print part A and then print part B. Something like:

{% highlight python %}
print "b = %s" % b  # Print part A
print b             # Then print part B
{% endhighlight %}

Combining these two together (along with some careful tiptoe-ing around python formatting) yields:

{% highlight python %}
b = 'print "b = %r" % b; print b'
print "b = %r" % b; print b
{% endhighlight %}

And there you have it! A general guideline to make quines followed by an example. You are now equipped to go out and impress all your friends with your quine-making abilities. :P
