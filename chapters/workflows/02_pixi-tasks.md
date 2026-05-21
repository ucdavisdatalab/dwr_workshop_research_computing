# Pixi Tasks

:::{admonition} Learning Goals
:class: note
After this lesson, you should be able to:

* Describe how to use Pixi to run tasks
* Create a task in `pixi.toml`
:::


## Creating Tasks

:::{important}
This chapter assumes you're already familiar with how to use Pixi as an
environment manager. You'll likely be able to follow the examples even if
you're new to Pixi, but if you want a review, see the [Instaling Software with
Pixi](sec-installing-software-with-pixi) workshop reader.
:::

Pixi refers to steps in workflows as **tasks**. You can configure a project's
tasks through the project's `pixi.toml` file, which is in [TOML][] format.

[TOML]: https://toml.io/

:::{note}
Pixi generates `pixi.toml` automatically when when you initialize a project by
running `pixi init`. You can run this command to set up Pixi for a project even
if the project directory already contains other files.
:::

For a new project, `pixi.toml` looks something like this:

```toml
[workspace]
authors = ["Nick Ulle <nick.ulle@gmail.com>"]
channels = ["conda-forge"]
name = "my-project"
platforms = ["linux-64"]
version = "0.1.0"

[tasks]

[dependencies]
```

The file consists of key-value pairs (`key = value`) organized into named
tables (`[table name]`). The three initial tables are `workspace`, `tasks`, and
`dependencies`. There are also many optional tables that may be present for
some projects. We'll focus on the `tasks` table, since it's where you add
tasks.

Suppose we want to add a task to the project that prints the message `Hello,
world!`. There are many different ways to print a message, but we'll use the
shell command `echo`.  So the command we want to run is:

```none
echo 'Hello, world!'
```

We can add a task `hello` that runs this command by editing the `tasks` table
to look like this:

```toml
[tasks]
hello = { cmd = "echo 'Hello, world!'" }
```

The name of the task goes on the left-hand side of the equals sign. The name
can consist of letters, numbers, underscores, and dashes. Spaces and other
whitespace are not allowed. 

:::{tip}
An underscore `_` at the beginning of a task's name marks it as a hidden task.
Hidden tasks are omitted when someone uses Pixi to print a list of all of a
project's tasks.
:::

:::{caution}
Be careful not to give tasks names that conflict with other tools you use. For
instance, `python` is probably not a good name for a task.
:::

You can run a task with the `pixi run` command and the name of the task. So to
run the `hello` task, enter:

```none
pixi run hello
```

```
✨ Pixi task (hello): echo 'Hello, world!'
Hello, world!
```

Notice that Pixi prints out the name of the task and the command it will run
before actually running the task.

:::{tip}
Pixi assumes tasks run shell commands, although you can certainly use shell
commands to run code written in other languages, such as R and Python.

The shell command to run an R script is `Rscript` (not `R`) followed by the
path to the script:

```none
Rscript path/to/script.R
```

If you want to run R code that isn't in a script, you can use `Rscript -e`
followed by the quoted code. For instance:

```none
Rscript -e "message('Hello, world!')"
```

Similarly, the shell command to run a Python script is `python` followed by the
path to the script. If you want to run Python code that isn't in a script, you
can use `python -c` followed by the quoted code.

If you use a different programming language, check the documentation.
:::

You can use the argument `-n` (or `--dry-run`) to do a dry run, where Pixi will
print the information about a task without actually running it:

```none
pixi run -n hello
```

```none
🌵 Dry-run mode enabled - no tasks will be executed.

✨ Pixi task (hello): echo 'Hello, world!'
```

You can use `pixi task list` to print a list of tasks you can run, as well as
descriptions for (almost) all tasks defined in the project:

```none
pixi task list
```

```none
Tasks that can run on this machine:
-----------------------------------
hello

Task  Description
```

Notice that the `hello` task shows up, but there's no description listed. It's
up to whoever created the task to provide a description by setting the
`description` key on the task. Let's add a description to the `hello` task.
Edit the `tasks` table in `pixi.toml` to look like this:

```none
[tasks]
hello = { cmd = "echo 'Hello, world!'", description = "Print a hello message." }
```

The description for a task can be whatever you like, but generally it should be
short enough to print well in `pixi task list`.

Now that we've set a description for the task, try listing the tasks again:

```none
pixi task list
```

```none
Tasks that can run on this machine:
-----------------------------------
hello

Task   Description
hello  Print a hello message.
```

There's the description! Adding descriptions to tasks makes them easier to use
and remember.

:::{seealso}
The official Pixi documentation includes [a page about tasks][px-tasks]. It
explains many features that we don't cover here.

[px-tasks]: https://pixi.prefix.dev/latest/workspace/advanced_tasks/
:::


## Dependent Tasks

As we've seen, it's often the case that one step in a workflow depends on
another. You can describe this relationship for a Pixi task with the
`depends-on` key.

Let's add another task to our project, one that depends on the `hello` task.
We'll call the new task `bye`, and make it print the message `So long, and
thanks for all the fish!`. In `pixi.toml`, edit the `tasks` table so that it
becomes:

```toml
[tasks]
hello = { cmd = "echo 'Hello, world!'", description = "Print a hello message." }
bye = { cmd = "echo 'So long, and thanks for all the fish!'", depends-on = ["hello"] }
```

Then try running the new task:

```none
pixi run bye
```

```
✨ Pixi task (hello): echo Hello, world!: (Print a hello message.)
Hello, world!

✨ Pixi task (bye): echo 'So long, and thanks for all the fish!'
So long, and thanks for all the fish!
```

Pixi automatically runs the `hello` task before it runs the `bye` task because
of the dependency we specified with the `depends-on` key.

:::{tip}
You can put line breaks in the definition of a task to make it easier to read.
For instance, you can instead write the `tasks` table above as:

```none
[tasks]
hello = {
    cmd = "echo 'Hello, world!'",
    description = "Print a hello message."
}
bye = {
    cmd = "echo 'So long, and thanks for all the fish!'",
    depends-on = ["hello"]
}
```

The only drawback this has is that it might not work with very old versions of
Pixi, because this is a relatively new feature of TOML (added in TOML v1.1.0).
:::

As another example, we use these tasks in the project for this reader:

```toml
[tasks]
build = { cmd = "jupyter-book build .", description = "Build the reader." }
publish = { cmd = "ghp-import --no-jekyll --no-history --push _build/html", description = "Publish the reader to the `gh-pages` branch on GitHub." }
clean = { cmd = "rm -rf _build/", description = "Remove the build directory." }
rebuild = { depends-on = ["clean", "build"], description = "Remove the build directory and build the reader." }
```

The `build` task generates the reader from source files, the `publish` task
uploads the reader to the web, and the `clean` task deletes the built reader
(in case we want to rebuild it). The `rebuild` task is special: instead of a
command to run, it specifies that it `depends-on` the `clean` and `build`
tasks. So Pixi will run both of those tasks when you run `rebuild`.


## Output Files

Sometimes one step's output is the input to another. If you only use
`depends-on` to describe this relationship, then the first task will run every
time you run the second. If the output doesn't change, re-running the first
task every time is inefficient. It's better to save the output and only re-run
the first task when its code or inputs change.

You can use the `outputs` key to list the outputs of a task. Then Pixi will
automatically skip the task if its outputs exist and its code is unchanged.

To demonstrate this, let's create two new tasks. The first task,
`save_message`, will save a message to a file called `message.txt`. The second
task, `show_message`, will print the message in the file. Change the `tasks`
table in `pixi.toml` to look like this:

```toml
[tasks]
hello = { cmd = "echo 'Hello, world!'", description = "Print a hello message." }
bye = { cmd = "echo 'So long, and thanks for all the fish!'", depends-on = ["hello"] }

save_message = { cmd = "echo 'IMPORTANT MESSAGE' > message.txt", outputs = ["message.txt"] }
show_message = { cmd = "cat message.txt", depends-on = ["save_message"] }
```

The first time you run the `show_message` task, Pixi will first run
`save_message`:

```none
pixi run show_message
```

```none
✨ Pixi task (save_message): echo 'IMPORTANT MESSAGE' > message.txt

✨ Pixi task (show_message): cat message.txt
IMPORTANT MESSAGE
```

After that, with the `message.txt` file in place, Pixi will skip the
`save_message` task:

```none
pixi run show_message
```

```none
✨ Pixi task (save_message): echo 'IMPORTANT MESSAGE' > message.txt
Task 'save_message' can be skipped (cache hit) 🚀

✨ Pixi task (show_message): cat message.txt
IMPORTANT MESSAGE
```

Setting the `outputs` key can make your workflows much more efficient.

If you change a task's command or environment, Pixi will re-run the task even
if its outputs already exist, in case the outputs from the new command are
different. To see this, change the message in the `save_message` task, then
run the `show_message` task again.

:::{caution}
This feature is extremely new, so expect bugs (and improvements). The Pixi
documentation describes an `inputs` key alongside the `outputs` key, but as of
writing, it doesn't always work as described.
:::


## Case Study: Davis Bike Counts, Part II

Let's try using Pixi to manage the workflow in the project from
{ref}`sec-case-study-davis-bike-counts-part-i`. We'll create three tasks:

* `clean_data` will run the `01_clean.R` script to clean the dataset and save
  the cleaned data.
* `fit_model` wil run the `02_model.R` script to fit and save the linear
  regression model.
* `visualize` will run the `03_plot.R` script to make a plot of the data and
  model predictions and save the plot to a PNG file.

We'll use several of the features explained in the preceding sections. The
project already has a `pixi.toml` file for environment management. In the
project directory, edit the `tasks` table in `pixi.toml` to look like this:

```none
[tasks]
clean_data = {
  cmd = "R/01_clean.R",
  outputs = ["data/interim/2020_davis_bikes_clean.rds"],
  description = "Clean the dataset."
}
fit_model = {
  cmd = "R/02_model.R",
  depends-on = ["clean_data"],
  outputs = ["models/bikes_model.rds"],
  description = "Fit a model to the cleaned data."
}
visualize = {
  cmd = "R/03_plot.R",
  depends-on = ["fit_model"],
  outputs = ["figures/bikes_plot.png"],
  description = "Make a plot of the data and model predictions."
}
```

Then you can run the entire analysis with one command:

```none
pixi run visualize
```

```none
✨ Pixi task (clean_data): R/01_clean.R: (Clean the dataset.)
Read 'data/2020_davis_bikes.rds'
Wrote 'data/interim/2020_davis_bikes_clean.rds'

✨ Pixi task (fit_model): R/02_model.R: (Fit a model to the cleaned data.)
Read 'data/interim/2020_davis_bikes_clean.rds'
Wrote 'models/bikes_model.rds'

✨ Pixi task (visualize): R/03_plot.R: (Make a plot of the data and model predictions.)
Read 'data/interim/2020_davis_bikes_clean.rds'
Read 'models/bikes_model.rds'
Saving 7 x 7 in image
Wrote 'figures/bikes_plot.png'
```

If you run the task again, Pixi will automatically skip all of the tasks for
which the outputs already exist.

The new tasks make it easy for someone to run the entire workflow correctly.
There's no need to consult the numbers on the scripts and diligently run them
in order one-by-one.

The descriptions on the tasks also make it easy to see what the workflows in
the project are by running `pixi task list`:

```none
pixi task list
```

```none
Tasks that can run on this machine:
-----------------------------------
clean_data, fit_model, visualize

Task        Description
clean_data  Clean the dataset.
fit_model   Fit a model to the cleaned data.
visualize   Make a plot of the data and model predictions.
```

So with relatively little effort, we made a big improvement to how easy the
project is to use.
