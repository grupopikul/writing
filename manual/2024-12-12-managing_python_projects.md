# Writing Python (DevOps)

TODO: include references to links as we do them.

A *project* is anything python.
A *package* is an installable python project.
An *app* is a python project with cli utilty.
A *library* is an importable python project.
A *script* is a single python file.
A *notebook* is a jupypter notebook.

We use uv to manage all python projects, and we rely on uv-native commands,
not their integration commands (like `uv venv` and `uv pip`).
So we use: `uv init|sync|run|add|remove|build|tree`.

## Starting a Project

You can use `uv init` to start a new project.
Use any [logistro](https://www.github.com/geopozo/logistro) as a template.

If your project is a collection of notebooks, or examples, you may not want to
write it as project, you can eliminate stuff (build).

### pyproject.toml and dependencies

See the logistro template mentioned above.

- use setuptools (for building)
- don't use MANIFEST.in (for including non-py files) (see [versioning python](./3-25-versioning_python.md))
- use "src" directory structure (see [versioning python](./3-25-versioning_python.md))
- use setuptools-git-versioning (with our callback) (see [versioning python](./3-25-versioning_python.md))
- use [logistro](https://www.github.com/geopozo/logistro)
- use .editorconfig
- use ruff (our pyproject has ruff stuff)
- use mypy (will run in --strict if py.typed)
  this must be in package data! double check!

And use the following below:

### mkdocs

Also use [mkquixote](https://www.github.com/geopozo/mkquixote). It's private
and must be turns on and off with comments when pushing.

This will force you to structure your packages intelligently and in a pythonic way.

### pre-commit

Use our pre-commit config for your python projects. See our
[working with git](./30-03-2025-working-with-git.md).

### GHA

Much of pre-commit, plus testing and converage, is required by mandatory GHA files.

If you are publishing a package, there are more.

## Jupyter notebook

A bit different than uv's recommendations, we include juptyer as a main dependency
or an [extra] dependency if jupyter is part of the workflow. The project has
its own jupyter build that way.
