# Workflow Managers

:::{admonition} Learning Goals
:class: note
After this lesson, you should be able to:

* Explain what computing environments are
* Explain what virtual environments are and why they're useful
* List popular tools for installing software on POSIX computers
:::


## What's a Workflow?

A **workflow** is a series of steps that must be completed in order to achieve
a specific goal. Every project has workflows. For example, if you want to make
a pizza, you could use this workflow:

* Gather the ingredients 🛒
* Make the dough 🌾
* Make the sauce 🍅
* Chop the toppings 🔪
* Stretch the dough 🫓
* Add the sauce 🥄
* Add the cheese 🧀
* Add the toppings 🫑🍄‍🟫🫒
* Bake the pizza 🍕

There are a few details worth pointing out about this example. First, notice
that most of the steps **depend** on others. For instance, you can't make the
dough until after you've gathered the ingredients. You can't stretch the dough
until after you've made the dough, and so on.

Second, notice that there are some steps that can be done in any order. The
pizza will still turn out right if you make the sauce before you make the
dough, or if you chop the toppings before doing either of these. These steps
can even be done simultaneously: you can make the dough while a friend makes
the sauce and another chops the toppings. So there are some steps that must be
done **in sequence**, while there are others that can be done **in parallel**.
That said, even the steps that can be done in parallel depend others. Making
the dough, making the sauce, and chopping the toppings all depend on gathering
the ingredients.

Third, some steps in a workflow might be optional. If you want a cheese pizza,
you can skip the steps that involve chopping and adding toppings. Which steps
are necessary depends on what you want as the final result.

Finally, this workflow is one way to make a pizza, but there are other ways.
There might not be one specific "correct" workflow for a particular goal. Use a
workflow that works well for you.

:::{figure} /images/workflows/pizza.svg
:alt: A graph that shows each of the steps in the pizza-making workflow as a node, with directed edges to show which steps are dependent on others.
:align: center
:height: 40em
A workflow diagram of the pizza-making workflow.
:::


## Workflow Managers

A **package manager** is a tool that can download, install, update, and remove
software packages. If you've used R or Python, you might already be familiar
with the package managers they provide. Many modern operating systems also
provide a package manager, because package managers have several benefits. They
can:

* Automatically select packages compatible with the computing environment
* Automatically install dependencies for packages
* Update installed packages, often automatically or with a single command
* In some cases, provide guarantees that packages are not malicious

:::{note}
Most Linux distributions provide a package manager as the recommended way to
install software. Nevertheless, it's possible to install software on Linux
without a package manager. One way is to download the source code for the
software and compile it yourself; another is to download a pre-built binary.
[FlatPak][] and [AppImage][] are two popular formats for distributing pre-built
binaries.

Install software via a package manager when possible, but be aware that there
are alternatives when it's not.

[FlatPak]: https://flatpak.org/
[AppImage]: https://appimage.org/
:::

Some, but not all, package managers can create **virtual environments**:
self-contained environments that can coexist alongside others, even if they
contain conflicting packages. You can think of a virtual environment as being
like a terrarium for a collection of packages.

Virtual environments make it easier to work on projects with different software
requirements simultaneously. For example, suppose one of your projects requires
Python 3.13 or newer, but another uses a package that hasn't been updated since
Python 3.9. You can work on either project as needed if you create two virtual
environments and switch between them: one with Python 3.13 and one with Python
3.9.

In the strictest sense, an **environment manager** is a tool that can create,
modify, and delete virtual environments. There are environment managers that
are not package managers (and vice-versa), but from here on we'll use the terms
somewhat interchangeably.

[Pixi][pixi] is the environment manager we recommend and use. Pixi is related
to the popular environment manager [Conda][]: both install conda packages from
[conda-forge][], a community-led repository of packages for research computing.
There are packages on conda-forge for R and Python, as well as other
programming languages and tools. Pixi can also install packages from other
sources and repositories (most notably, from the [Python Package Index][pypi]).

[pixi]: https://pixi.sh/latest/
[Conda]: https://conda.org/
[conda-forge]: https://conda-forge.org/
[pypi]: https://pypi.org/

We recommend Pixi over Conda because Pixi creates environments that are fully
reproducible, takes a project-centric approach to environments, is noticeably
faster, and lacks many of Conda's quirks and pitfalls. That said, Pixi is
relatively new, so you might occasionally encounter missing features or bugs.

:::{important}
Pixi is available for Windows, macOS, and Linux, and generally doesn't require
administrator privileges to install.

Install Pixi by following [the official instructions][pixi].
:::

:::{note}
Examples of other popular package and environment managers are:

* [Homebrew][] for macOS and Linux
* [Chocolatey][] for Windows
* Advanced Packaging Tool (APT) for Debian-based Linux distributions
* [Nix][] for Linux and macOS
* [Spack][] for Linux and macOS, focused on high-performance computing
* [EasyBuild] for Linux, focused on research computing

[Homebrew]: https://brew.sh/
[Chocolatey]: https://chocolatey.org/
[Spack]: https://spack.io/
[Nix]: https://nixos.org/
[EasyBuild]: https://easybuild.io/
:::

:::{note}
Virtualization tools, such as [Podman][], [Docker][], and [VirtualBox][] are a
different way to create isolated computing environments. They provide complete
control over the operating system and software in an environment, so they
provide stronger guarantees of reproducibility. The cost is that these tools
are often slower than environment managers and using them requires more
technical knowledge.

For most research projects, using an environment manager provides adequate
flexibility and reproducibility.
:::

[Podman]: https://podman.io/
[Docker]: https://www.docker.com/
[VirtualBox]: https://www.virtualbox.org/
