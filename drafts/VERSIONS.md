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

# Packaging, and Versions

## Versions

I want you to use git. Not everyone uses git. If you want to build an tagged release,
you should be able to without git. If you want to build an intermediate release,
one that you downloaded from github, better to use git, but maybe we can provide some
other method. A build hash.

So imagine a release protocol that... writes somewhere a starting versioning and a
recalculatable hash. Hash of what, the source? Yes, the source dist.

If you build something without git without that hash, the version should come back
and calculate dirty.

## Packaging

Source, everything is source. SCM is right, because how we build is just as important
as what we built. The binary is just what you need to run. But the source build
without configuration files doesn't really make sense, since directing the build is
part of the source.





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

# FINAL THOUGHTS ON VERSIONING AND RELEASING

Context: we're working with setuptools, not hatch or other build backends.

1. Version Generators: `setuptools-git-versioning` (GV) vs `setuptools-scm` (SCM)

  GV: Can change template for how to print version
    -- GV and SCM have similiar default templates:
       GV:  {tag}.post{distance}+git.{hash}.dirty (customizable easily)
       SCM: {tag}.dev{distance}+g{hash}.d{date}
       Where:
       - {tag}: git tag, e.g. v1.0.0
       - {distance}: how many commits beyond last tag we are
       - {hash}: hash of the last comit
       - {date}: if the repo is dirty, ie uncommited changes, SCM includes the date
      Suffixes will only be printed if relevant, so a fresh v1.0.0 tag will show
      just v1.0.0, not v1.0.0.post0+git.???.dirty.
  GV: Can set starting version (for when no tag).
  SCM: impacts what files are packaged as well (see 2a.)

  Neither obeys version = "X", I'm not happy with either behavior without a tag.
  **DECISION: STATIC VERSIONING AND DEFAULT**.

2. Context about file inclusion
  a. What files are included
    Fact 1: Every build creates 1. a wheel (*bdist*) and 2. a tar (*sdist*).
    The *bdist* is just what is needed to run, where as a *sdist* is whats needed
    to build from scratch.

    Fact 2: There are two common directory structures:
      1. "src": /repo_root/src/package_name/...
      2. "flat": /repo_root/package-name/...
      Where "..." is all your py files. These we call the "**package directory**".
      **DECISION: NO MORE FLAT.**

    Fact 3: There is an advanced feature called *namespaces which allows import to
    "feel" like one package but are actually packaged and installed differently:

    ```python
    import plotly.kaleido # package 1
    import plotly.io # package 2
    # both namespace plotly
    ```

    Setuptools searches for py files and include some other stuff (pyproject.toml) in
    both *bdist* and *sdist*. *bdist* files always have to be in the *package
    directory*, whereas *sdist* searchpaths depend on some settings (more later).

    If namespace is enabled, folders don't need a `__init__.py`, all py files
    will be included in the *package directory*. **DECISION: NAMESPACE**

    So this leads to two common questions:
      - How do we add non-python files to the dists? **DECISION: FILE LIST**
        - package-data or MANIFEST.in if include_package_data=True
        - If we have a data folder, with data files included, but
          no __init__.py, setuptools will cry unles namepsace is enabled.
          **DECISION: DATA __INIT__.py**.

      - How do we control what gets added to the *sdist*?
        **DECISION: SDIST COMPILING, SRC IS NOT DEV**
        Some default behaviors:
          - If we're using a flat *package directory*, setuptools will include all
            py files from root (ew).
          - If we're using SCM, all git-track files are include (sometimes good?)

        package_data (like in bdist) can only be used for non-py files inside the
        *package_directory*. But MANIFEST.in and [tools.setuptools.include] (from
        pyproject.toml) can be instructed to include files and directories.
        Supposedly MANIFEST.in is more flexible.

3. What Are My Requirements + DECISIONS
  The "DECISIONS" are *how* we will achieve the rest.
  - Build Without git
    I want to improve fallbacks for automatic git versioning.
    If there is no tag available, or we are not in a git directory, we should generate
    the version from file if there is one, or throw an error. If there a version tag
    set, we should use that version. If we have generated the version from a version
    file, we should check a hash.
  - Use Tags
    Versions will be slightly different if we're coming out of a git directory or not
    If not in a git directory, and the hashes don't match, we just put a note
    in the version that things are different than they were when the build was built.
  - Changelog
    Pull requests should require the addition to the top of changelog. The merge should
    always be adding to the top of the changelog. We will have to enforce that.
  - Release steps
    Releases should be be kind of automatic. Add the version to the changelog.
    Changelog should include an sdist hash. We can pull the last version
    directly from the changelog, so when we're releasing, we should be adding
    a version title to the changelog. It could even come from the branch name?
    And the tag would be generated automatically if the tests are passing.
    Release branches would only make change to the changelog. release/v1.0.0023 tag name.
    And it should check the merge to make sure it's just the changelog and thats it.
    It can make other changes to the changelog besides adding a title. Branch
    name should follow semantic versioning rules.

4. Decisions:


After thought:

Config files are abused.

