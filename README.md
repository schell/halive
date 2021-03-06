```
██╗  ██╗ █████╗ ██╗     ██╗██╗   ██╗███████╗
██║  ██║██╔══██╗██║     ██║██║   ██║██╔════╝
███████║███████║██║     ██║██║   ██║█████╗
██╔══██║██╔══██║██║     ██║╚██╗ ██╔╝██╔══╝
██║  ██║██║  ██║███████╗██║ ╚████╔╝ ███████╗
╚═╝  ╚═╝╚═╝  ╚═╝╚══════╝╚═╝  ╚═══╝  ╚══════╝
```
Live recompiler for Haskell

![Halive Demo](http://lukexi.github.io/HaliveDemo.gif)

Halive uses the GHC API to instantly recompile
and reload your code any time you change it.

Usage:
`stack install halive`

and then

`halive <path/to/mymain.hs> <extra-include-dirs>`

Any time you change a file in the current directory or its subdirectories,
halive will recompile and rerun the `main` function in the file you gave it.

If the program is long-running (e.g. a daemon, GUI or game loop) it will be
killed and restarted. Learn how to maintain state in the next section.

Try a live-coding GL demo by running:
```
# Grab the demo package and install the demo's dependencies - only need to do this once
stack unpack halive
cd halive-0.1.0.8
stack build --test --no-run-tests

# Now run Halive
halive demo/Main.hs
```
Changing values in `Main.hs` or `Green.hs` and saving should live-update the program.

Keeping values alive
--------------------

To keep state alive, import `Halive.Utils` and wrap
your value in `reacquire` along with a unique identifier, like:

`win <- reacquire "win" (setupGLFW "HotGLFW" 640 480)`

to only create the resource the first time you run the program, and then
reuse it on subsequent recompilations.

You can see this in action in `demo/Main.hs`.

Thanks to Chris Done's
[`foreign-store`](https://hackage.haskell.org/package/foreign-store)
library for enabling this.

Passing command-line arguments
------------------------------

To use Halive with haskell code that is expecting command-line arguments,
separate the arguments to Halive and the arguments to the app with a `--`
such as:

`halive <path/to/mymain.hs> <extra-include-dirs> -- <args-to-myapp>`

Notes
-----

Creating, updating, and deleting modules in the include path should
work fine during a Halive session.

Halive supports Stack projects and Cabal sandboxes;
if run within a directory containing a stack.yaml or cabal.sandbox.config
file it will use the appropriate package databases when running the target.

Halive works nicely with either batch-processing or run-loop type
programs — if the program finishes, it will be restarted on next save,
and if it's still running, it will be killed and restarted on save.

To kill Halive during run-loop type programs, you may need to hold down Ctrl-C
to get GHC to recognize the double-Control-C-kill sequence.

Halive works on Windows, Mac, and Linux

As a Library
------------
Halive can also be integrated into your own project as a library in a few lines of code. See `test/TestSubHalive.hs` for an example.
IMPORTANT: You must link your binary with `ghc-options: -dynamic` for this to work! Otherwise you'll get mysterious linking errors on Mac and Linux.

Troubleshooting
---------------
If Halive with GLFW segfaults on windows, try `git clone -b win-halive-fix http://github.com/lukexi/bindings-GLFW` and adding the folder you cloned it to to your project's `stack.yaml` in the `packages` section

[@lukexi](http://twitter.com/lukexi)
