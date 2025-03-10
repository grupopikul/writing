Basics
- basic variables (mutable/non-mutable)
  - why numbers int
  - why floating point numbers
- fors
- whiles
- variables
- ifs
Imports
Types and Objects (ooh boy)
- types
-
Variables/Memory (ooh boy)
- references, = signs
  - How does it work in C
  - How does it work in
- scoping (complex)



Releasing, versions


Overall goals:

Tags, Releases, and Published Packages in Sync
Changelog Updated for Each Version
Version accessible via `__version__`
Version mainly driven by git

Stories

So we have release, where we tag, we push a tag, and it gets released on
everything
We should also be able to manually run the big testing on a pull request
There could be errors, so the pypi should go out last, since its the most receptive
to errors
It needs to be somewhat baby sat, because if there are errors, it needs to be
"unreleased"

So how do we push the change
Some people don't want to use stuff from git, so they can't
generate the tag automatically.

The issue is that, if we're going to write the tag into the version,
you get into a cyclical issue. You have to... predict what the version tag will be
since we use a hash... so like, if we write the hash, and calculate the hash, the
hash changes. There is no way to precalculate the hash. This is fine for non-hash
inolved version releases (like pure tags), but any .post release will fail. So
you can't really write that into the version.
So, we can calculate the version tag from the wheel, and if that doesn't work
when we're building the wheel, setuptools-scm can ask, I guess, if it does, i'll have
to test.

This might be accompanied by a github action? Would the github action be a different
repository
I'm not sure

But i think that's good, that means the dev can't use any version that has a period
in it- you can use X.Y.Z[a|b|rc] but not .dev or .post since steptools-scm will
break those.

Also not entirely done with the github workflows, wondering if we can split them
into github actions.
- tagging and pushing to MAIN BRANCH
- not doing anything unless its _all good_

Releasing will be tagging and pushing

Something to do some basic consistency checks

But tehre's also the issue, with setuptools-scm, about how it handles package data, and
what exactly goes on, and I kind of want to resolve that.

package_data, problems with source, scm-version

versioning python guide
