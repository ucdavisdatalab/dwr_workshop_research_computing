# Building Workflows and Pipelines

_One 2-hour session_

A workflow is a series of steps that must be completed in order to achieve a
specific goal. Every project has workflows. For computing projects, typically
some of the steps are to run computer commands or code. 

In this workshop, you'll learn how to use workflow management software to
record the computational steps, so that they're easy to run with the correct
arguments and in the right order every time. Specifically, we'll cover the
workflow management features of [Pixi][], as well as the popular [Snakemake][]
workflow manager. This workshop also includes an optional unit on the
[targets][] package for R, an R-focused workflow manager. In the context of
software engineering, workflow managers are also known as build systems.

[Pixi]: https://pixi.sh/
[Snakemake]: https://snakemake.github.io/
[targets]: https://docs.ropensci.org/targets/

:::{admonition} Learning Goals
:class: note
After completing this workshop, learners should be able to:

+ Explain what workflows are
+ Explain what workflow managers are and why they're useful
+ List some popular tools for workflow management
+ Explain the relative strengths and weaknesses of different workflow managers
+ Create and organize project directories for projects
+ Use Pixi tasks to describe and run project workflows
+ Use Snakemake to describe and run project workflows

Part of this workshop is modular, and which units are covered is up to the
instructor. Each unit lists specific learning goals.
:::
