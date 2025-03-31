## Naming Branches

Branches should be named like:

`yourname/topic`.

Branches should be as independent as possible.

Release branches, for adminstrators, are like:

`release/v1.0.0`.

There is also a branch called latest-tag: `latest-tag`.

## GHA

It is expected that your pullrequest pass the required GHAs. Todo: Make list.

## Pre-commit


Pre-commit does a bunch on your code.

Facts:

1. It can be hard to organize well in the beginning
2. If things get TOO organized
3. Send commits intelligently, broken off into correct pieces, I will read them.
  - Use `git add . --patch` for that.
  - It is harder to do in the beginning

Ruff and other things will block you from commiting if there are errors. If you
have ruff installed in your editor, you can avoid having to redo all your code.

You can use `SKIP=pre-commit_id git commit...` to skip a particular pre-commit check.
You can `git commit --no-verify` to skip all checks.
