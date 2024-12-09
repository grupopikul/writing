# Environment and Package Management

We're talking about `pip` vs `go.mod` vs `gem` vs `npm`. We're not talking about publishing.

Facts:

1. Developer and computer shouldn't compete to manage a file.
2. Computer should respect what the developer wants to track (github branch, version release, etc)
  `go` has this problem in `go.mod`- updates will change your tracking specifications there.
3. There dependency list should not be a lock file (`pip freeze`). So `requirements.txt` and `requirements.lck`.
4. Environments need to be silo'd to particular applications.

So `npm` and `gem` seem to get this right. Python's strategy has been piecemeal: `pip` has few opinions, and
other programs are needed to manage environments; `pyenv` and `virtualenv`- all of this can be handled by `uv` now.

In all cases, the environment can be confounded if its laden with configuration. `jupyter` will treat the `venv` folder
like a root folder and place tons of configuration files there. Not withstanding `jupyter`'s difficult configuration
situation, it becomes difficult to properly version and migrate configuration directives if they're mixed around and
scattered.

In the spirit of 1., we should allow whichever environment management tool you use to manage the environment it creates
entirely, and not make any many modifications. If you change configuration files installed by the environment manager- unlike
in your linux system where your package manager has a diff system for dealing with this, behavior for in python environments
is undefined.

So for `go`, it sucks, but I generally keep a separate `requirements.txt` which
regenerates the `go.mod` each time- but `go`'s environment manager may have improved.
Eitherway, it requires a lot of reading, the solutions tend to be over engineered.

In your development, you should fight against making changes to the folder you intend the manager to manage.

npm --save and all that is fine.
