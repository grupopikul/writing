# Versioning and Releasing

!!! note
    My thought-process and reasoning is presented in the note blocks, similiar
    to this one. They render better on the website. Skip them if you don't care.

We work exclusively with setuptools, python's classic build system.

!!! note "Why?"
    There are many tools, and no clear winner, so we use the classic. Hopefully
    Astral Software comes out with a final solution.

We use dynamic versioning in pyproject.toml, and `setuptools-git-versioning`
is responsible for determining the version being build.

It uses git tags for that, but its behavior is customized by a callback, to allow
users without git to also build our package with correct metadata.

!!! note "Why `setuptools-git-versioning?`, Why git?"
    We are fully invested in using git as a vcs, so we leverage this integration.
    For users who want to build without git, we wrote an extension to allow that.

    We do not use setuptools-scm because it changes how setuptools determines which
    files to include in sdists (see below), and this is bad *seperation-of-concerns*.

??? note "How is the version string decided?"
    GV's default version template is: `{tag}.post{distance}+git.{hash}.dirty`
    Where:
    - {tag}: git tag, e.g. v1.0.0
    - {distance}: how many commits beyond last tag we are
    - {hash}: hash of the last commit
    - dirty: if build with unstaged changes, ".dirty" is appended.
    If distance is 0, then it won't be printed (nor will what follows).
    GV allows changing this template.


The above means that you, a developer, cannot tag with `.post`, `.dev`, or any
version suffix that requires a period ".". You can use `a`, `b`, and `rc`, as they
are written as `v1.0.0a`, not `v1.0.0.a`. But you can't do `.post`, it's written as
`v1.0.0.postN`. Otherwise, our automatic versioner will produce a non-compliant
version number.

That's why `release/vXXX` branch names are scrutinized.

## Runtime Version Calculation

When a package is imported, `package.__version__` is set to the proper version string.

```python
from importlib.metadata import version, PackageNotFoundError

try:
    __version__ = version("package-name")
except PackageNotFoundError:
    pass # package is not installed
```

## Which Files are Included In A Package Release

!!! warning
  This is a lot of background info of python. Skip what you already know.
  **Do not skip the warnings.**

### Package Distributions: bdist vs sdist

Builds produce two packages:

1. **bdists:** what you need to run the package
2. **sdists:** what you need to build the package

### Package Directory: flat vs src

Your main python files go in a *package directory* (pkg dir).
There are two structures:

1. **flat** (older): *./package-name/...*
2. **src** (newer): *./src/package_name/...*

!!! warning "Use src."
    "Flat" provokes weird behavior from setuptools, we don't use it.

### namespaces

!!! warning "Disable namespaces."
  No need for it or its complications yet.
  Its set to false in the pyproject.toml.

Namespaces which allows imports to "feel" like one package but are actually packaged
and installed separately:

```python
import plotly.kaleido # package 1
import plotly.io # package 2
# both namespace plotly
```

### What Files are Included

By default, only py files are included. Default directories to search for py:

|           | flat       | src       | with setuptools-scm |
|-----------|------------|-----------| ------------------- |
| bdist     | pkg dir    | pkg dir   | pkg dir             |
| sdist     | whole repo | pkg dir   | git repo, py or not |
    If namespace is enabled, folders don't need a `__init__.py`, all py files
    will be included in the *package directory*. **DECISION: NAMESPACE**

#### How to Include Data Files

In the bdist, all non-py files must be in the pkg dir. In pyproject.toml,
there is a `package_dir` setting. All folders must have a `__init__.py`.
Those files will be in bdist and sdist.

!!! warning MANIFEST.in
    You could use MANIFEST.in with `include_package_data=True`. We do not.

!!! warning __init__.py
    Even data-only folders must have an __init__.py. It could be used to
    provide utitilies related to the data, if desired.

#### Adding Extra Stuff to sdist

sdist needs files outside of the pkg dir.

!!! warning "sdist is NOT dev"
    The goal of sdist is not to provide all files needed for developing.
    The goal is to provide all files needed for building the package.

!!! warning "Best Strategy Depends..."
    1. If its really pure python, then don't do anything.
    2. You can use `[tools.setuptools.include]` and `[tools.setuptools.exclude]`.
       exclude always wins.
    3. MANIFEST.in is an alternative, we don't use it.

## Releasing

!!! warning
    This has a lot to do with our rules for git branching.

Every pull request should (can) put a new line item at the top of the CHANGELOG.

Example:

```text
- Added some feature in my pull-request
v1.0.0:
- Added some feature in previous version
- Another feature from a pervious version
```

When a release is coming, a branch will be pushed `release/v1.1.0` where
the top of line of the CHANGELOG must be that same feature, and when accepted
(and all passed), a tag will be pushed automatically.

## Building Without git

If our extension to `setuptools-git-versioning` cannot pull a tag, for whatever
reason, it will use the changelog to determine the version, and warn so.

If it finds a `version` in pyproject.toml, it will use that with warning.
This will always take priority.

If it has no changelog and no tag, it will use 0.0.1 w/ git versioning. If
there is no git, it will... warn and use "0.0.1+dirty".
