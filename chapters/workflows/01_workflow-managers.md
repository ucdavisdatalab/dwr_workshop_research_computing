# Workflow Managers

:::{admonition} Learning Goals
:class: note
After this lesson, you should be able to:

* Explain what workflows are
+ Explain what workflow managers are and why they're useful
+ List some popular tools for workflow management
+ Explain the relative strengths and weaknesses of different workflow managers
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

Research projects have workflows too. For example, suppose you're working on a
study of fish populations in local rivers. One workflow in your project might
be to go out in the field and collect fish count data. In most research
projects, some of the workflows involve computing. For instance, in the fish
population study, you might need to:

* Clean up the data collected in the field;
* Fit a statistical model to the data;
* Create a visualization of the data.

You could use R, Python, or other tools for these. The point is that all of
them are workflows.

Sometimes, you might need to run a workflow more than once. For example, you
might need to collect, clean, model, and visualize fish count data every year
for several years. For workflows that are partially or entirely on a computer,
it's possible to reuse some or even all of the code and commands.

The workflows in a project matter because:

* You'll need to explain the workflows to anyone who joins the project as a
  collaborator.
* You might need to repeat workflows to generate new results or verify old
  ones.
* You'll need to explain the workflows when you share results (for instance, in
  a presentation, report, or peer-reviewed article).
* Others might want to run the workflows to reproduce your results.

Thoughtful workflow planning and detailed documentation makes a project much
more transparent, robust, and reproducible. The subsequent sections discuss how
to document workflows and some software tools that are helpful for organizing
and running computational workflows.


## Documenting Workflows

The simplest thing you can do to improve a project's workflows is document
them. For each workflow, at a minimum the documentation should explain:

* The purpose (what goal does it achieve?)
* How to run it (what are the steps, code, or commands?)
* Any necessary inputs and dependencies (what needs to be done first?)
* All outputs (what does it produce?)

README files (`README.md`) are often a good place for workflow documentation,
but you can provide these details in whatever place and format you prefer. The
important things are to provide the documentation and to do so in a way that
makes it easy to find and access.

Diagrams, graphs, or flow charts are often a good way to represent workflows
visually. In the graph, show each step as a node or vertex, and show dependence
between steps with arrows or edges. For example, {numref}`fig-pizza` shows a
diagram of the pizza-making workflow.

:::{figure} /images/workflows/pizza.svg
:alt: A graph that shows each of the steps in the pizza-making workflow as a node, with directed edges to show which steps are dependent on others.
:align: center
:height: 40em
:name: fig-pizza
A workflow diagram of the pizza-making workflow.
:::

The benefit of workflow diagrams is that you can see how steps, or even whole
workflows, are related at a glance. Together with other documentation, they
typically make it much easier to understand how the project works.

:::{note}
A workflow diagram is usually a **directed acyclic graph** (DAG), which means
that it has direction (an order to the steps) and that there are no cycles
(circular sequences of steps).
:::

:::{seealso}
For more about how to document workflows, see the [Document the
Workflows][rrg-document] section of the UC Davis Library's Reproducible
Research Guide.

[rrg-document]: https://ucdavisdatalab.github.io/guide_reproducible_research/chapters/04_reproducible-computing.html#document-the-workflows
:::


## Workflow Managers

A **workflow manager** is a tool that can keep track of and run computational
workflows. Generally, to set up workflow manager for a project, you have to
record the details about each workflow in a structured format. Once that's
done, the workflow manager provides commands you can use to run any of the
workflows. Most workflow managers can also:

* Automatically run any prerequisite steps or workflows for the workflow you
  requested;
* Automatically **cache** output files, saving them so that a workflow won't
  run again unless its inputs or code change;
* Automatically **log** output messages, saving them to a file so that you can
  review them as needed;
* Automatically load the appropriate virtual environment (see
  {ref}`sec-environment-managers`) when running a workflow;
* Automatically run steps in parallel if their dependencies allow it;
* Support **dynamic workflows**: workflows that can be reused across many
  different inputs and outputs;
* Facilitate running steps in high-performance and distributed computing
  environments.

In software engineering contexts, workflow managers are also called **build
systems**.

:::{note}
A **task runner** is a simple workflow manager, in the sense that task runners
usually lack most of the features listed above. They can run a workflow, but
little else.

This simplicity makes task runners easy to learn and use. They can be helpful
for projects where the workflows are straightforward, but the commands for each
step are hard to remember or to type. On the other hand, if you're already
comfortable with a workflow manager, you might as well use that.
:::

[Make][], created in 1976, was the first workflow manager. It's still in
widespread use and remains popular today. Make uses a text configuration file
called a **makefile** to record workflows. While you could use Make for
research computing, it was designed with software engineering---specifically,
writing C code---in mind. It also has several limitations, rough edges, and
gotchas. Thus we recommend using newer tools that are intended for research
computing.

[Make]: https://en.wikipedia.org/wiki/Make_(software)

This reader covers the following following workflow managers:

* [Pixi][] is primarily an environment manager (see
  {ref}`sec-environment-managers`), but it can also provides basic workflow
  management features. The configuration for Pixi is written in [TOML][]. Pixi
  can run workflows for any tools or languages.
* [Snakemake][] is a workflow manager that originated in the Bioinformatics
  community, but has since become popular across a wide variety of disciplines.
  Snakemake combines some of the best features of Make with the flexibility of
  Python. The configuration for Snakemake is written in a superset of Python.
  Even if you're not a Python user, Snakemake is relatively easy to learn and
  can run workflows for other tools and languages.
* [targets][] is a workflow manager and R package. It works especially well if
  the steps in your workflows correspond to individual R functions. The
  configuration for targets is written in R. Although R is definitely the
  focus, targets also provides some support for other tools and languages.


[Pixi]: https://pixi.sh/latest/
[TOML]: https://toml.io/
[Snakemake]: https://snakemake.github.io/
[targets]: https://docs.ropensci.org/targets/

We recommend Pixi because it's easy to adopt (and doesn't require any
additional software) if you already use it as an environment manager. It's a
good choice for small projects or if you just want to try out a workflow
manager. That said, it provides fewer features than a dedicated workflow
manager. For instance, it doesn't provide a way to run tasks in parallel or in
distributed computing environments.

We recommend Snakemake as a general-purpose workflow manager. It's a mature,
stable tool and provides all of the workflow manager features listed at the
beginning of this chapter. It's also under active development to fix bugs and
add new features as computing methods evolve. Its popularity also means that
it's relatively easy to find help with online.

We recommend the targets package if you're an R user and want to avoid learning
or using any tools from outside of the R ecosystem. While the package provides
many features for R, it's quite limited for other workflows, and most
development is done by a single person. At DataLab, we rarely use targets for
our projects.

:::::{important}
To follow along with this and subsequent chapters, you'll need to install Pixi,
Snakemake, and the targets package on your computer:

::::{tab-set}

:::{tab-item} Pixi
Pixi is available for Windows, macOS, and Linux, and generally doesn't require
administrator privileges to install.

Install Pixi by following [the official instructions][pixi].
:::

:::{tab-item} Snakemake
Snakemake is available for Windows, macOS, and Linux. The official
documentation recommends using Pixi to install Snakemake globally:

```none
pixi global install snakemake -c conda-forge -c bioconda
```

If you prefer, you can instead use Pixi to install Snakemake on a per-project
basis. Add `bioconda` to the end of the `channels` list in `pixi.toml`, then
run:

```none
pixi add snakemake
```
:::

:::{tab-item} targets
The targets package is available for all platforms R supports. You can install
the package globally through R's built-in package manager:

```r
install.packages("targets")
```

If you prefer, you can instead use Pixi to install targets on a per-project
basis:
```none
pixi add r-targets
```
:::

::::
:::::

:::{note}
A few more examples of workflow managers are:

* [Airflow][]
* [Just][]
* [Luigi][]
* [Metaflow][]
* [Nextflow][]

[Airflow]: https://github.com/apache/airflow
[Just]: https://github.com/casey/just
[Luigi]: https://github.com/spotify/luigi
[Metaflow]: https://metaflow.org/
[Nextflow]: https://www.nextflow.io/

There are many lists of workflow managers online. For instance, see [this list
of workflow managers][awesome-workflows].

[awesome-workflows]: https://github.com/pditommaso/awesome-pipeline
:::
