# Python Project Management

We will now use `uv` to manage all python projects.

A *project* is anything python.
A *package* is python project meant to be published.
An *app* is a python project meant to be run as a program.
A *library* is a python project meant to be included as a dependency.
A *script* is a single python file- we now use python for ALL scripting.

A *project* can be a *package*, *app*, and/or *library* all at the same time, or any combination.

## Greenfield Projects

To create a new python project, use:

`uv init --app --package --lib` or remove any of those.
# TODO: can you do app and library at the same time?

`uv add` and `uv remove` can be used to mark dependencies in `pyproject.toml`.

Many `uv` commands will create the `.venv` environment for the project, including `uv env`. Use `uv env --name NAME` if you don't want to use the folder name.

`uv sync` will ensure that the `.venv` looks like the `pyproject.toml`.

`uv lock` creates a lockfile- a specification of the exact version you are using.

TODO: There should be a repo w/ better templates depending on the situation.

## Existing projects

Download it and run `uv sync`. See [Development Sources](#development-sources) and [Dependency Organization](#dependency-organization).

### Running

TODO: better on running
`uv run whatever.py` can be used to run a script, run an app (if there is a main), run a module `uv run -m module`, run ipython in an environment:

`uv run -- command`: `uv run -- python` `uv run -- python -m asyncio` etc.

# Tools

Some development dependencies can be installed with `uv tool install`: they won't be marked as any dependency of the project. They run in their own environment.


## Ruff

`uv tool install ruff`
`uv tool run ruff check`

## Markdown

TODO: live markdown renderer with uv tool

## Pytest

This isn't great for `pytest` since projects often have their own specific `pytest` dependencies.

# Development Source

For development, you can specify certain dependencies like `uv add --editable PACKAGE_NAME @ path`
TODO: check to see hows that's expressed

uv will prefer the (enforce?) the path version if you're working with uv out of the development directory, but will push it to pypi with the correct dependency.

# Dependency Organization

## Extras

Extras are a concept that allow pypi modifiers: `pip install package[dev,documentation]` etc. `uv add --optional dev pytest`, although in this case I don't recomend using extras for this.

## Groups

Groups allow us to specify dependency groups that *aren't* available in pypi, but are still registered for development
`uv add --dev PACKAGE` is the equivalent of `uv add --optional dev PACKAGE`.

These optional groups are not available via pypi, but uv will install, by default, the `dev` group if you are installing from `uv` (ie you downloaded the repo) and more groups if in `pyproject.toml` they are specified as installed by default. You can all install w/ sync, `uv sync --package --group` and other commands (todo).
