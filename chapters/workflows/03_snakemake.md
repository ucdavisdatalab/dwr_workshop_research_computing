# Snakemake

:::{admonition} Learning Goals
:class: note
After this lesson, you should be able to:

* Explain what Snakefiles are
* Explain what Snakemake rules are
* Create a Snakefile
:::

This chapter provides a brief introduction to using [Snakemake][] for workflow
management.

[Snakemake]: https://snakemake.github.io/


## Snakefiles

With Snakemake, you use a text file called a **snakefile** to configure the
workflows and steps in a project. The snakefile can be named `Snakefile` or
`snakefile`.

In the Snakefile, you'll add **rule** for each step that specifies what command
to run and other details. The syntax of the Snakefile is based on [Python][],
so indentation and spacing is important.

[Python]: https://www.python.org/

As a first example, let's create a Snakefile with a `hello` step that uses the
`echo` shell command to print the message `Hello, world!` (similar to what we
did with Pixi in {ref}`sec-creating-tasks`). Create a new empty project
directory:

```none
mkdir snake_project
cd snake_project
```

Then open `Snakefile` in your favorite text editor. Edit the file to look like
this:

```none
rule hello:
  shell:
    "echo 'Hello, world!'"
```

If you've used Python before, this syntax should look familiar (but don't worry
if you haven't). A Snakemake rule always begins with the keyword `rule`,
followed by a name for the rule and a colon `:`. The name of the rule can
contain letters, numbers, and underscores. All of the details of the rule go on
subsequent lines and must be indented 2 or 4 spaces.

Within a rule, the `shell` keyword specifies a shell command to run when
Snakemake runs the step the rule describes. The command must be indented 2 or 4
spaces and quoted with single or double quotes.

Once you've set up a Snakefile, you can run specific steps with the `snakemake`
command. Snakemake is designed from the ground up to take advantage of all of
your computer's CPU cores by running tasks in parallel whenever possible, so
you must also provide a `--cores` argument that specifies how many cores to
use. You can set `--cores all` to use all cores. So to run the `hello` step:

```none
snakemake --cores all hello
```

```none
Assuming unrestricted shared filesystem usage.
host: sei
Building DAG of jobs...
Using shell: /usr/bin/bash
Provided cores: 16
Rules claiming more threads will be scaled down.
Job stats:
job      count
-----  -------
hello        1
total        1

Select jobs to execute...
Execute 1 jobs...

[Wed May 20 15:18:51 2026]
localrule hello:
    jobid: 0
    reason: Rules with neither input nor output files are always executed.
    resources: tmpdir=/tmp
Hello, world!
[Wed May 20 15:18:51 2026]
Finished jobid: 0 (Rule: hello)
1 of 1 steps (100%) done
Complete log(s): /home/nick/snake_project/.snakemake/log/2026-05-20T151851.774908.snakemake.log
```

The output from Snakemake includes a variety of diagnostic information,
including which shell was used, how many cores were provided, which steps were
selected to run, and the path to a log file with all of the output from the
steps. It's a bit hard to see amidst all of the other output, but notice that
the step did in fact print `Hello, world!`.

:::{tip}
If you want to run an R, Python, or Julia script, use the `script` keyword
instead of the `shell` keyword. The `script` keyword requires a quoted path to
the script. For instance:

```none
rule run_script:
  script:
    "path/to/script.R"
```

Snakemake will automatically infer how to run the script from the extension
(`.R`, `.py`, or `.jl`). The `script` keyword also supports other languages;
see [the documentation][snakemake-docs] for details.
:::

You can use the argument `-n` (or `--dry-run`) to do a dry run, which will make
Snakemake print out the diagnostic information without actually running any
steps. In this case, you don't have to set the `--cores` argument:

```none
snakemake -n hello
```

```none
host: sei
Building DAG of jobs...
Job stats:
job      count
-----  -------
hello        1
total        1


[Wed May 20 15:27:44 2026]
rule hello:
    jobid: 0
    reason: Rules with neither input nor output files are always executed.
    resources: tmpdir=/tmp
Job stats:
job      count
-----  -------
hello        1
total        1

Reasons:
    (check individual jobs above for details)
    neither input nor output:
        hello
This was a dry-run (flag -n). The order of jobs does not reflect the order of execution.
```

You can use the `-l` (or `--list` or `--list-rules`) argument to make Snakemake
print a list of all of the rules in a project:

```none
snakemake -l
```

```none
hello
```

The list only shows descriptions for rules that include one. You can set a
description for a rule by putting a triple-quoted string on the lines
immediately after the `rule` keyword (in Python, this is called a
**docstring**). Edit the Snakefile to look like this:

```none
rule hello:
  """Print a hello message.
  """
  shell:
    "echo 'Hello, world!'"
```

Now try listing the rules again:

```none
snakemake -l
```

```none
hello (Print a hello message.)
```

The description is printed in parentheses after the name of each rule.

:::{tip}
Snakemake's standout feature is that Snakefiles are just an extension of
Python, so they can contain arbitrary Python code. In other words, you can
define Python variables and functions in a Snakefile and use them to help you
set up rules. See [the Snakemake documentation][snakemake-docs] for examples of
this.
:::

:::{seealso}
The [official Snakemake documentation][snakemake-docs] includes a tutorial and
details about many more features than we can cover here.
:::

[snakemake-docs]: https://snakemake.readthedocs.io/en/stable/


## Input & Output Files

Snakemake is designed to make tracking the relationships between steps in a
workflow as painless as possible. The best way to do this is by specifying the
input files and output files for each step. Once you do this, Snakemake will
automatically infer which steps depend on others.

Within a rule, you can use the `input` keyword to provide a list of input files
to the step and the `output` keyword to provide a list of output files from the
step. The paths in the list must be quoted and separated by commas.

Let's create two new rules to demonstrate how to set inputs and outputs. The
first rule, `save_message`, will save a message to a file called `message.txt`.
The second rule, `show_message`, will print the message in the file. Edit the
Snakefile to look like this:

```none
rule save_message:
  output:
    "message.txt"
  shell:
    "echo 'IMPORTANT MESSAGE' > message.txt"

rule show_message:
  input:
    "message.txt"
  shell:
    "cat message.txt"
```

Now try running the `show_message` task:

```none
snakemake --cores all show_message
```

```none
Assuming unrestricted shared filesystem usage.
host: sei
Building DAG of jobs...
Using shell: /usr/bin/bash
Provided cores: 16
Rules claiming more threads will be scaled down.
Job stats:
job             count
------------  -------
save_message        1
show_message        1
total               2

Select jobs to execute...
Execute 1 jobs...

[Wed May 20 16:04:56 2026]
localrule save_message:
    output: message.txt
    jobid: 1
    reason: Missing output files: message.txt
    resources: tmpdir=/tmp
[Wed May 20 16:04:56 2026]
Finished jobid: 1 (Rule: save_message)
1 of 2 steps (50%) done
Select jobs to execute...
Execute 1 jobs...

[Wed May 20 16:04:56 2026]
localrule show_message:
    input: message.txt
    jobid: 0
    reason: Rules with a run or shell declaration but no output are always executed.
    resources: tmpdir=/tmp
IMPORTANT MESSAGE
[Wed May 20 16:04:56 2026]
Finished jobid: 0 (Rule: show_message)
2 of 2 steps (100%) done
Complete log(s): /home/nick/snake_project/.snakemake/log/2026-05-20T160455.967729.snakemake.log
```

Snakemake correctly infers that in order to run `show_message`, it must first
run `save_message`. If `message.txt` exists, Snakemake will automatically skip
running `save_message`, unless the rule or its inputs have changed.

At this point, perhaps you can already see that Snakemake's `input` and
`output` keywords are more powerful than their counterparts in Pixi, but
there's more.

The `save_message` and `show_message` rules we wrote are slightly redundant,
because the input/output appears twice in each rule. First we list it after the
`input`/`output` keyword, and then we repeat it again in the shell command.
Whenever you write a string in a Snakemake rule, you use curly braces `{ }` in
the string to substitute a value from the rule's metadata. For instance, you
can write `{input}` in a string and Snakemake will replace this with the
name(s) of the input file(s). We can use this feature to make the Snakefile
less redundant. Edit the Snakefile to look like this:

```none
rule save_message:
  output:
    "message.txt"
  shell:
    "echo 'IMPORTANT MESSAGE' > {output}"

rule show_message:
  input:
    "message.txt"
  shell:
    "cat {input}"
```

Try running the `show_message` step again. You should see that the step still
runs and that Snakemake still infers that it depends on `save_message`.

When `input` or `output` is a list of multiple files, Snakemake automatically
concatenates the file names, with spaces in between, before replacing `{input}`
or `{output}`. You can use square brackets to get a single element from the
list by position (starting from 0). So `{input[0]}` is replaced with the first
file name in the input list.

:::{tip}
In an `input` or `output` list, curly braces indicate an **named wildcard**
rather than a substitution. You can use named wildcards to create steps that
generalize to many files.

For example, suppose you sometimes use the `magick` shell command to convert
images from JPEG to PNG format. You can use named wildcards to create a rule
that works with any JPEG file:

```none
rule convert:
  input:
    "{image}.jpeg"
  output:
    "{image}.png"
  shell:
    "magick {input} {output}"
```

Then you can run this step for a file, say `flower.jpeg`, with the this
command:

```
snakemake --cores all flower.png
```

Rather than specifying the desired rule's name, here we specify the desired
output. Snakemake automatically figures out which rule to use.

You can learn more about named wildcards in [the official
documentation][snakemake-docs].
:::


## Case Study: Davis Bike Counts, Part III

Let's try using Snakemake to manage the workflow in the project from
{ref}`sec-case-study-davis-bike-counts-part-i`. We'll create three rules (just
as we created three rules with Pixi):

* `clean_data`
* `fit_model`
* `visualize`

In the project directory, create a Snakefile and edit it to look like this:

```none
rule clean_data:
  """Clean the dataset.
  """
  input:
    "data/2020_davis_bikes.rds"
  output:
    "data/interim/2020_davis_bikes_clean.rds"
  shell:
    "pixi run R/01_clean.R"


rule fit_model:
  """Fit a model to the cleaned data.
  """
  input:
    "data/interim/2020_davis_bikes_clean.rds"
  output:
    "models/bikes_model.rds"
  shell:
    "pixi run R/02_model.R"


rule visualize:
  """Make a plot of the data and model predictions.
  """
  input:
    "data/interim/2020_davis_bikes_clean.rds",
    "models/bikes_model.rds"
  output:
    "figures/bikes_plot.png"
  shell:
    "pixi run R/03_plot.R"
```

:::{note}
The Snakefile in this example uses the `shell` keyword rather than the `script`
keyword to ensure that the scripts run in the environment set managed by Pixi
(hence `pixi run`). 

Snakemake has built-in support for automatically activating Conda environments
through its `conda` keyword, but support for Pixi environments is still being
developed. Know that it is a priority for the developers, many of whom are
enthusiastic Pixi users, and will likely be ready soon.
:::

Then, to run the entire analysis:

```none
snakemake --cores all visualize
```

```none
Assuming unrestricted shared filesystem usage.
host: sei
Building DAG of jobs...
Using shell: /usr/bin/bash
Provided cores: 16
Rules claiming more threads will be scaled down.
Job stats:
job           count
----------  -------
clean_data        1
fit_model         1
visualize         1
total             3

Select jobs to execute...
Execute 1 jobs...

[Wed May 20 18:51:03 2026]
localrule clean_data:
    input: data/2020_davis_bikes.rds
    output: data/interim/2020_davis_bikes_clean.rds
    jobid: 1
    reason: Missing output files: data/interim/2020_davis_bikes_clean.rds
    resources: tmpdir=/tmp
Read 'data/2020_davis_bikes.rds'
Wrote 'data/interim/2020_davis_bikes_clean.rds'
[Wed May 20 18:51:03 2026]
Finished jobid: 1 (Rule: clean_data)
1 of 3 steps (33%) done
Select jobs to execute...
Execute 1 jobs...

[Wed May 20 18:51:03 2026]
localrule fit_model:
    input: data/interim/2020_davis_bikes_clean.rds
    output: models/bikes_model.rds
    jobid: 2
    reason: Missing output files: models/bikes_model.rds; Input files updated by another job: data/interim/2020_davis_bikes_clean.rds
    resources: tmpdir=/tmp
Read 'data/interim/2020_davis_bikes_clean.rds'
Wrote 'models/bikes_model.rds'
[Wed May 20 18:51:03 2026]
Finished jobid: 2 (Rule: fit_model)
2 of 3 steps (67%) done
Select jobs to execute...
Execute 1 jobs...

[Wed May 20 18:51:03 2026]
localrule visualize:
    input: data/interim/2020_davis_bikes_clean.rds, models/bikes_model.rds
    output: figures/bikes_plot.png
    jobid: 0
    reason: Missing output files: figures/bikes_plot.png; Input files updated by another job: data/interim/2020_davis_bikes_clean.rds, models/bikes_model.rds
    resources: tmpdir=/tmp
Read 'data/interim/2020_davis_bikes_clean.rds'
Read 'models/bikes_model.rds'
Saving 7 x 7 in image
Wrote 'figures/bikes_plot.png'
[Wed May 20 18:51:04 2026]
Finished jobid: 0 (Rule: visualize)
3 of 3 steps (100%) done
Complete log(s):
/home/nick/foundry/datalab/teaching/research_computing/example-project/.snakemake/log/2026-05-20T185103.057255.snakemake.log
```

If you run the command again, Snakemake will automatically skip all of the
steps, since the outputs already exist.

You can also use `snakemake -l` to print the steps in the workflow:

```none
snakemake -l
```

```none
clean_data (Clean the dataset.)
fit_model (Fit a model to the cleaned data.)
visualize (Make a plot of the data and model predictions.)
```
