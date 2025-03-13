## Tags, Releases, and Published Packages in Sync

Upon release, there should be github actions to test and publish versions.
Mine are based off uv, but there is some work to do to make sure that

a. the testing is done with the right uv lock
b. the GHA fails properly

GHA needs to be audited for security.

### Auditing

There will be an audit tool that looks for differences in how versions are
presented to the public (via pypi, github releases, and repo tags).
They should all be in sync. It should also audit the CHANGELOG.txt

## CHANGELOG Updated for Each Version <-- release

When releasing a version, we need to make sure CHANGELOG is up-to-date.
So pushing a tag should show the CHANGELOG for confirmation. I think this will have
to be a pre-commit tho because github can't (in non-enterprise) reject tags.
I want there to be explicit changelog approval when submitting a tag.
People who can push tags need to also be limited.

But the question is how do we properly compile the CHANGELOG for a version. We don't
know what pull requests make it into what version. But it goes like this:
If it is getting merge into main, it should be present in _whatever the next tag to be
pushed to main is_. So you would theoretically, in your pull request, add your
changelog entries to changelog.

- How do people add their changelog entries.
- How do people verify that the correct entries are going to the tag.


1. You cannot push a tag if your changelog is not set to the proper version.
2. So you would need a release branch in that case? That would be like
   release/vX.Y.Z
   And that would be merged and tagged.
3. And in that branch, you would compile the changelog, and eliminate the list
   list of changelog entries.

So you would be tagging a merge of a release branch with the proper tag.
Basically, I want an automatic release process. I want every pull request to _require_
a changelog entry (even if its a blank one), and should have a summary for the
reviewer. Then a release branch would compile those things. Release branches
would only be allowed and approved by certain people, and it would get pushed.
And you could make reports about releases. This is a little bit more involved, but
it makes a lot of sense. But the tag would get pushed after the release.

## Version Accessible Via `__version__`

There needs to be boiler plate for this, and I think it should be setting the
`__version__` attribute upon import:
```python
from importlib.metadata import version, PackageNotFoundError

try:
    __version__ = version("package-name")
except PackageNotFoundError:
    pass # package is not installed
```

## Version Mainly Driven By Git

https://setuptools-git-versioning.readthedocs.io/en/stable/comparison.html

So it's fine to stick with `git-setuptools-versioning`.
Developers cannot tag with .post or .dev or . anything. A precommit check
should make sure of that, and I guess maybe we can block tags that don't have this
format.
Also checks on tags.

------------------------

# old below

------------------------


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
