### Why use choreographer instead of playwrite?

1. choreographer is phenomenally smaller (30kB vs 50MB)
2. choreographer is significantly less memory hungry in running (enabling the impressive videos, not a use-case for playwrite)
   likely because playwrite is a testing framework on top of browser control.
3. choreographer supports all the same versions that plotly.py does (plotly.py would have either drop support for certain versions of
python, go on playwrites schedule, or deal with strict version resolvers like poetry and uv that expect 100% resolving of all *possible*
version combinations on any sync/run)
4. choreographer is stabler from our perspective, playwrite will drop versions faster, will drop also supported browsers faster, and
then plotly would have to play catch-up, and its a 50MB moving target. As it stands now, choreographer is update or changed
when it suits plotly.

Playwrite does some have advantages that I'm looking at regarding getting browsers downloaded.

Addendum:

Development time was higher than I anticipated, mainly because, even though choreographer is relatively simple, IMO,
I choked on low level async structuring, where function tracebacks no longer behave like linear function stacks
(ie errors propagate up through calling functions), but instead are (bi)directed graphs, meaning errors from a task can
propagate up (to calling functions) down (to called functions!) and side-wise
(to any task or future created or cancelled or otherwise interacting with the connected subgraphs).
