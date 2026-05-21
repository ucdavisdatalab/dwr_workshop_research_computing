# Unit A: The targets Package

:::{admonition} Learning Goals
:class: note
After this lesson, you should be able to:

* Explain what the targets package does
* Configure a `_targets.R` file
:::

This unit provides a very brief example of using the [targets][] package for
workflow management in R.

[targets]: https://docs.ropensci.org/targets/

## Introduction

The targets package uses an R script, `_targets.R`, to configure the workflows
and steps in a project. You can use the package's `use_targets` function to
generate a template version of `_targets.R`.

Within `_targets.R`, you use a list at the end of the file to define the steps
in the project's workflows. Each step must be provided via a call to the
`tar_target` function. The first argument to `tar_target` is the name of the
step (without quotes). The second argument to `tar_target` is the R code to run
for the step, which can refer to outputs from other steps.

There are a two ideas that are key to understanding the targets package:

* It assumes that each step corresponds to a separate function defined in the
  project's R code. By default, the package will search for code in the `R/`
  subdirectory.
* It automatically saves the R output from every step. There's no need to save
  outputs to files unless you want to share those files. The output is saved in
  a special `_targets/` subdirectory in the project directory.

Keep these in mind as you go through the following example.


## Case Study: Davis Bike Counts, Part IV

Let's try using the target package to manage the workflow in the project from
{ref}`sec-case-study-davis-bike-counts-part-i`. To get started, run R in the
project directory, load the targets package, and call the `use_targets`
function:

```r
use_targets()
```

This will create a `_targets.R` file in the project directory.

To configure the project workflow, open the `_targets.R` file and edit the list
at the end of the file to look like this:

```r
# Replace the target list below with your own:
list(
  tar_target(file, "data/2020_davis_bikes.rds", format = "file"),
  tar_target(source_bikes, read_bike_data(file)),
  tar_target(clean_bikes, clean_bike_data(source_bikes)),
  tar_target(bikes, make_bike_features(clean_bikes)),
  tar_target(model, fit_bike_model(bikes)),
  tar_target(plot, plot_bike_model(bikes, model))
)
```

Each call to `tar_target` defines a separate step in the workflow. The first
call to `tar_target` describes the `data/2020_davis_bikes.rds` file, and does
not carry out any action. Each of the remaining calls to `tar_target` describe
a call to a single function in the project's R scripts.

After setting up the `_targets.R` file, you can call the package's
`tar_manifest` function to get a data frame with information about all of the
steps in the workflow:

```r
tar_manifest()
```

```none
# A tibble: 6 × 2
  name         command
  <chr>        <chr>
1 file         "\"data/2020_davis_bikes.rds\""
2 source_bikes "read_bike_data(file)"
3 clean_bikes  "clean_bike_data(source_bikes)"
4 bikes        "make_bike_features(clean_bikes)"
5 model        "fit_bike_model(bikes)"
6 plot         "plot_bike_model(bikes, model)"
```

:::{tip}
You can also call the package's `tar_visnetwork` function to view the project's
workflow as a graph.
:::


To run a step in the workflow, use the package's `tar_make` function. If you
call the function without any arguments, the package will try to run every step
in the workflow. You can also set the first argument to the names of the steps
you want to run. Try running the `plot` step:

```r
tar_make(plot)
```

```none
+ file dispatched
✔ file completed [0ms, 4.02 kB]
+ source_bikes dispatched
✔ source_bikes completed [0ms, 4.02 kB]
+ clean_bikes dispatched
✔ clean_bikes completed [11ms, 3.85 kB]
+ bikes dispatched
✔ bikes completed [1ms, 3.88 kB]
+ model dispatched
✔ model completed [3ms, 61.21 kB]
+ plot dispatched
✔ plot completed [15ms, 244.39 kB]
✔ ended pipeline [285ms, 6 completed, 0 skipped]
```

After running the workflow, you can call the `tar_read` function to materialize
the output from any step. So to display the plot, run:

```r
tar_read(plot)
```

Similarly, to get or display the fitted model, run:

```r
tar_read(model)
```

```none
Call:
lm(formula = count ~ date * site * pandemic, data = bikes)

Coefficients:
                (Intercept)                         date
                 -95997.742                        5.313
                  sitethird                 pandemicTRUE
                  51762.400                    90878.360
             date:sitethird            date:pandemicTRUE
                     -2.859                       -5.022
     sitethird:pandemicTRUE  date:sitethird:pandemicTRUE
                 -29280.107                        1.653

```

This way you can access outputs from any step in the workflow.


:::{seealso}
For more about how to use the targets package, see [the official
documentation][targets-docs].
:::

[targets-docs]: https://books.ropensci.org/targets/
