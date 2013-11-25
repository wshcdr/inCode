The Quantum Mechanical Feynman Path Integral Problem
====================================================

Categories
:   Physics
:   Computation
Tags
:   numerical methods
Series
:   Path Integral Monte Carlo
CreateTime
:   2013/11/19 22:05:34
PostDate
:   Never
Identifier
:   path-integral-intro

Happy to get a chance to write about my first love, Physics and Computational
Physics.  In this series we will be going over many subjects in both physics
and computational techniques, including the Lagrangian formulation of
classical mechanics, basic principles of quantum mechanics, the Path Integral
formatulion of quantum mechanics, the Metropolis-Hastings Monte Carlo method,
dealing with entropy and randomness in a pure language, and general principles
in numerical computation!  Fun stuff, right?

The end product will be a tool for deriving the wave state solutions of
arbitrary quantum systems, which is somewhat of a big deal in any field that
runs into quantum effects (which is basically every modern field).  But the
real goal will be to hopefully impart some insight that can be applied to
broarder and more abstract applications.  I am confident that these techniques
can be applied to many problems in computation to great results.

I'm going to assume little to no knowledge in Physics and a somewhat
intermediate working knowledge of programming.  We're going to be working in
both my favorite imperative language and my favorite functional language.

In this first post I'm just going to go over the basics of the physics before
we dive into the simulation.  Here we go!

Classical Mechanics
-------------------

### Newtonian Mechanics

Mechanics has always been a field in physics that has held a special place in
my heart.  It is most likely the field most people are first exposed to in a
physics course.  To me, there really is no more fundamental and pure form of
physics.  I mean...it's the study of how things move under forces.  How can
you get any deeper to the heart of physics than that?

When most people think of mechanics, they think of $F = m a$, inertia, and
that every reaction has an equal and opposite re-action.  These are Newton's
"Laws of Motion" and they provide what can be referred to as a "state-updating
function": Given a state of the world at time $t_0$, Newton's laws can be used
to "generate" the state of the world at time $t_0 + \Delta t$.

This sounds pretty useful, but it wasn't long before physicists began wishing
they had other tools with which to study the mechanics of certain systems.
Newton's equations worked very well for the cases that made it famous, but
were surprisingly unuseful, impractical, or clumsy in many others.

So it was almost exactly one hundred years after Newton's laws that two people
named [Lagrange][] and [Euler][] (who is the "e" in $e$) followed a wild hunch
that ended up paying off and being the solution everyone had been waiting for.

[Lagrange]: http://en.wikipedia.org/wiki/Joseph-Louis_Lagrange
[Euler]: http://en.wikipedia.org/wiki/Leonhard_Euler

### Lagrangian Mechanics

To understand Lagrangian Mechanics, we must abandon our idea of "force" as the
fundamental phenomenon.  Instead of forces, we deal with "potential fields".

You can imagine potential fields as a roller coaster track, or as a landscape
of rolling hills.  The "potential" at a given point on a track is equal to the
height. Potential fields work like this: Every object "wants" to go
*downwards* a potential field --- it will want to go in the direction (left or
right) that will take it downwards.  We don't care why, or how --- it just
"wants" to.  And the steeper the downwardness, the greater the compulsion.

We call this potential field $U(x)$, which means "$U$ at the point $x$".

Now, for Lagrangian Mechanics:

Let's say I tell you an object's location at time $t_0$, and its
location later at time $t_1$, and the potential energy field.  What path did
that object take to get from point A to point B?

A pretty open question, right?  You don't really have that much information to
go off of.  You just know point A and point B.  It could have taken any path,
for all we know!

The solution to this problem is actually rather unexpected.  Consider every
single path/curve from point A to point B.  Every single one.  Now, assign
each path a number known as the **Action**:

1.  For every point, add up $U(x)$ at that point.
2.  For every point, add up the square of the object's speed.  Multiply it by
    $\frac{m}{2}$.
3.  Subtract (1) from (2).

Think about every possible path.  Calculate the action for each one.
The path that the object takes is *the path with the lowest action*

It's almost as if the object "does the math" in its head: "I'm going to go
from here to there...let me calculate which path I can take has the lowest
action.  Okay, got it!"

Lagrangian Mechanics provides for us a way to find out just what path an
object must have taken to get from point A to point B.

As it turns out, looking at things this way opens up entire worlds of
understanding.  For example, just from this, we find that **total energy is
conserved** over time for a closed system (trust me on this; the calculus is
slightly tricky).  And yes, if you actually try to find the path of lowest
action, the path will somehow magically always be the same one you would get
if you used $F = m a$.  It's just now we have a much more insighftul and
meaningful way to look at the universe.

Paths **always attempt to minimize their action**.

Okay.  We don't have that much time to spend on this, or its philosophical
implications, so we're going to move on now to Quantum Mechanics.

Quantum Mechanics
-----------------

### Schrödinger Formulation

If there was one thing that "everyone" knew about quantum mechanics, it would
either be [Scrödinger's Cat][cat] or the fact that objects are no longer "for
sure" anywhere.  They are only *probably* somewhere.

[cat]: http://en.wikipedia.org/wiki/Schr%C3%B6dinger's_cat

How can we then analyze the behavior of *anything*?  If everything is just a
probability, and nothing is certain, we can't really use the same
"state-updating functions" that we used to rely on, because the positions and
velocities of the objects in question don't even have well-defined values.

Physicists' first solutions involved creating a new "state" that did not
involve particles at all.  They invented a new, abstract sort of "state" that
sort-of corresponds to the probability of finding an object here or there, and
then created state-updating equations that updated that new, abstract state.

This approach is useful...just like $F = m a$ was useful.  But we want
something more.  How can we apply what we learned about actions and Lagrangian
mechanics to Quantum Mechanics?  How do we make Lagrangian mechanics
"quantum"?

### Path Integral Formulation

The answer is a bit obvious, actually.

Instead of saying "the object will chose the path with the least action", we
say **the object chooses a random path, with probability depending on the
action**.  That is, again, if an electron is shot from point A to point B, the
electron picks a random path from point A to point B.  It is a *weighted
random choice* based on the action of each path --- if Path $\alpha$ has
lower action than Path $\beta$, the electron will pick path $\alpha$ more
often than path $\beta$.

There are some small technical differences, but the fundamental principle
remains the same.

So say we have an electron floating around a hydrogen atom.  We know it is at
point A at time $t_0$, and point B at time $t_1$.  What path did the electron
take to get there?

Simple: We don't know.  But we can say that it *probably* took the path with
the least action.  It *could have also* taken the path with the second to
least action...just slightly less likely.  It *probably did not* take the path
with the greatest action...but who knows, it might have.  It's like it rolls a
dice to determine which path it goes on, but the dice is weighted so that
lower-action paths are rolled more often than higher-action paths.

The electron *wants* to take the lowest-action path...but sometimes decides
not to.  Sometimes fails.

So now we see what Lagrangian Mechanics really *is*: It's quantum mechanics,
except that the lowest-action path is *so much likelier* than any other path
that we almost never see the second-to-least action path taken.

As it turns out, like Lagrangian mechanics opened eyes to new worlds in
classical mechanics, the Path Integral formulation[^naming] of quantum
mechanics opened up totally new worlds that the previous "state updating
formula" approach could have never dreamed of.

[^naming]: Why is it called the "Path Integral" formulation?  When we add up
something at every single point on a path, we are mathematically perfoming a
"Path Integral".  So Path Integral Formulation means physics based on the
adding up stuff for every point on a path.

Implications
------------

Okay, so what does this all have to do with us?

How many processes do we know that can be modeled by something trying to
minimize itself, but not always succeeding?  What data patterns can be gleamed
by modeling things this way?

I'll leave this question slightly open-ended, but I'm also going to hint at
the next installment's contents.

### Particle in a potential

Let's go back again to our electron next to an atom.  Let's say that this
electron will move around and return back to its current position at time
$t_0 + \Delta t$, for very large $\Delta t$.  Basically, it can do whatever it
wants and has plenty of time to do so, following path integral mechanics...it
just has to somehow eventually end up back at its original position.

The electron basically wanders around aimlessly, following path integral
mechanics.

We pick a few random points in our path...those points pretty much represent
**where an electron is most likely to be found**.

We now have a way, given any quantum potential, find the probability
distribution of a particle in that potential.  Take thousands or millions of
such points, and find the probability distribution of those points.

Now let's implement it.
