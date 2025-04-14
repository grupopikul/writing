mypy (alternatives)

so basically, you can run mypy in a non-commital way (don't run --strict)
and mypy accepts that your not typing but also looks for any obvious errors resulting
from type issues or any type annotations floating around in other packages or in yours.
I found it to be very friendly.

running --strict means asking your employees to type.

SO maybe do the first, not the second
And maybe recommend to your employees that they use basic types with the first (int, str, etc), but lets leave
the complex types along for a while, as nice as they would be to have stably.

Here's where the deal:

If you want to type python, 80% of your type annotations are parsed only when type-checking, (we'll call it type-time, like build-time and run-time).
but 20% of the them produce real objects (such as when you create custom types,
you're actually creating new class objects!) and those impact run-time.

Because typing is so unstable (big improvements every python version), I could say:

Only run type checking at the newest version, they don't get parsed during runtime anyway, so you don't need
to worry about a user using an older supported version not supprting the types- the users will not be type checking.
The users only do runtime, so you can use the latest typing syntax!

However, if there is brand new syntax in that 20%, that doesn't work, so any type annotation with type-time side effects
will need to support all your supported versions, and you may need to also run mypy against the version range, but then
you'll get errors for all the new (and actually it is really important) syntax you use.

Now imagine you have version switches in your code to support your customer's older versions while using newer features that you need them to eventually up to, so you have conditional branches that a) need to be checked by older parsers and others that b) can only be checked by newer parsers. Mypy with conditional ignores would work here (only typecheck if > 3.12)

Would would be useful would be a "run an older type checker but only on things that don't occur during type checking."
Ie, if this statement is ONLY reachable during type-time, then you can use the newer type-checking syntax, but if this statement is reachable during run-time, then check it with the _older_ typing syntax supported by the older versions of python we're supporting for our users.

Async/Await

You may not know this, but async functions are considered python Generators (functions which yield), which were implented to create dynamic iterators.

```
onetwothree = [1, 2, 3] # static iterator

def count_to_three(): # dynamic iterator
	yield 1
	yield 2
	yield 3
	# shoulda been a loop!

for x, y in zip(onetwothree, count_to_three):
	assert x == y

```
There's a whole series of presentations on "how to build an python operating system with yield statements",
I won't put any examples here, but basically the task manager runs the iterators and switches between everytime one yields
and does the other one. 50 years ago it was called cooperative routines(tasking?) and was how we wrote schedulers in microprocessors.
As opposed to the modern day, where the kernel uses a hardware timer to tick out of an executing thread and uses a red-black tree
to decide which thread it should run next.

Anyway, to finish this python operating system, the couroutines need to be able to send and receive information to the task manager, which is function calling the tasks (task manager = scheduler),
and the org went in this direction and allow yield statements to not just *yield* values but also *receive values*.
Again, more on this in the presentations, I won't even show you how to send a value INTO an iterator because its... not the point.


So a generator, which is kind of like a function? but also an iterator? has a signature with three values: the value yielded, the value the yield recieves, and the value the generator returns. `Generator[?, ?, ?]`.

Now, this is super abstract, and most programmers dont use generators this way- they treat them (like we do in kaleido), just like
dynamic lists. But python's programmers doubled down on the abstraction and used this to implement to the async/await system in python.

This means that, when you're typing a function like `async def foo()` ie passing such a function an object to another function or variable (asyncio.call_later(foo)) you have to deal with the fact that the typer doesn't really see it as a function, it sees it as a generator object, and now your exposed to this scientifically abstract computer science concept which amounts to "hey, lets see if we can compress two separate concepts (async/await functions and dynamic iterators) into one implementation (python generator-coroutines which recieve and yield values) because on an abstract level, the concepts are equivelent."

So your juniors aren't gonna get that, sorry.

There are other complexities, the general ones that come along with an unstable python api (from __future__ import).

So:

- [ ] Use typing at the end of 2025? Or reasses
- [ ] Use typing for internal tools that you can force up to new version (worry about UV here, see note)
- [ ] Use simple typing
