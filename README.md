# coding-practices

(<https://framverse.github.io/coding-practices/>)

Our evolving coding best practices document


# Goals

The goal of these best practices is to act as a guideline to produce code and 
analyses that are highly transparent, transferable, reproducible and approachable.


<!-- ## General practices -->

<!-- Todo:
 - CBE: read other R best practices docs, adapt parts that make sense to us.
 - Populate / link directions on some of the suggestions (e.g., git, renv, etc)
-->

# Best practices

## Overview

Good coding practices make collaboration easier and faster, and reduce the frequency and consequences of bugs and problems. At least initially, adhering to good practices can feel like it add unnecessary steps that slow progress. In the long run, however, we find that these practices save time. Further, they increase the transparency of our code, which in turn increases the overall transparency of our work. 

Below, we outline best practices organized into related topics. When it can be done succinctly, we provide explanations for *why* the practices save time. After the guidelines, we include some short tutorials and examples to show *how* to implement some of the less obvious practices.

## Project management


For any kind of substantial work involving more than one file, use Rprojects, the `here` package, and `renv` to make scripts easily shareable. The goal is that you can zip up a folder, send it to someone else, and they can run any scripts without making any changes.

When developing a document to report results or findings to a general user, use Rmarkdown or Quarto to create a report that blends R code with explanations and graphics.

When a script is likely to be re-used (e.g. not a one-off analysis), create a commented version with instructions on use. This should be stored somewhere accessible. Collin Edwards maintains the `snippets` [github repository](https://github.com/FRAMverse/snippets) for this kind of thing, or it could live in a Teams folder. If code is likely to be useful to the team or others, it may also be appropriate to incorporate this code into an R package, or develop a new R package for this code. Converting code to packages is much more involved that storing a code snippet or re-useable script somewhere, but makes it much easier for incorporation into other code.

To make scripts easier to re-use, replace hard-coded specifics with variables that are defined at the top of the script. For example, if Collin wrote a script to read in the Mortalities table of a FRAM database and plot the landed catch for a specific fishery, he would probably initially write that script using the file name and fishery name wherever he needed it (e.g., `connect_fram_db("FramDBExample.Mdb")` and `data |> select(fishery_id == 19) |> ...`). To make this script easier to re-use, he could add lines of code near the top of the script, with

```r
file_use = "FramDBExample.Mdb"
fishery_use = 19
```

and then replace any hard-coded uses of the filename and fishery ID with those variables (e.g., `connect_fram_db(file_use)` and `data |> select(fishery_id == fishery_use) |> ...`). This makes it very easy to re-use for a different case -- simply update the lines defining `file_use` and `fishery_use`.

### Starting an R project 
### `here` package
The [here](https://here.r-lib.org/) package has similar functionality to base
R's `setwd` and `getwd` functions. The real benefits of the `here` package,
particularly the `here::here()` function, is to easily enable project-oriented
workflows that are easily shared. `here::here()` when used properly with a project
structure similar to the one outlined in this document allows for easier file path 
referencing in code along with the elimination of file path fiddling for someone
receiving the project.

Example:
```r
# Good
data <- read_csv(here::here('original_data/data.csv'))

# Bad
data <- read_csv('C:/Users/blah/Desktop/my_r_project/original_data.csv')
```

### Using `renv`

- Where to get it
- What it does
- Why you would want to use it

### Sharing an R project

- Bundling / zipping the project to be shared
- Why `here` and `renv` are important on the receiving end


#### Outside WDFW
Various collaborators have completely independent IT departments with their
own rules around the types and sizes of files they can receive through email.

Common reasons for blocked emails:

- `.html` and `.zip` files
- Size of the file too large

Most of the time these restrictions can be worked around via using a 3rd party
to host the file like an AWS S3 bucket or Google Drive. Due to the sensitive 
nature of some of the data being shared, the file should be deleted from the
3rd party when the receipt is verified. 

When `.zip` files are blacklisted by the recipient's IT department, an
alternative would be the `.7z` format from the [7-zip](https://www.7-zip.org/)
software.




#### Common project directory structure

When working across multiple projects, it can be helpful if each project has a similar file structure. Ty and Collin will discuss what that should be, but good foundation is Ty's approach, which has a `data/` and a `scripts/` subfolder. It may be helpful to also include a standardized readme with basic information (when project was started, what goal was, who was working on it). When Collin was working in an academic setting, he had a [code snippet](https://gist.github.com/cbedwards/7e64215e062c42da54dbd01626ef6a72) that he ran whenever starting a new project, which created his standardized folder structure and auto-populated a few key template files. We could think about writing something similar.


Draft file structure?
*CBE: slightly updated. I like to keep the raw data in a separate folder from where cleaned / intermediate data files live.*

```
project_folder
├── scripts
│   ├── data_clean.R # should save to `cleaned_data/`
│   └── analysis.R
├── original_data
│   ├── data.csv
│   └── more_data.xlsx
├── cleaned_data
│   ├── data_cleaned.csv
├── figures
│   └── some_figure.png
├── results
│   └── some_spreadsheet.xlsx
├── .gitignore
└── project_folder.Rproj
```

#### Databases
Security around database connections is an important concern. Under no circumstance
should any of the following be shared in a script or data:

-   Database login information like usernames or passwords
-   Database server locations like IP addressed or domain names

Ideally, database connections should be stored locally as an ODBC connection and 
referenced in within the script as a DSN.

```r
con <- DBI::dbConnect(dsn='<dsn_name>')
```



#### Tips

-   We have a [template YAML header](https://github.com/FRAMverse/snippets/blob/main/R/markdown-and-quarto/custom-yaml-header.Rmd) for quarto / rmarkdown files that includes some useful settings for readability, and gives a WDFW flavored header when combined with [this style guide](https://github.com/FRAMverse/snippets/blob/main/R/markdown-and-quarto/style.css).
-   using `if(interactive())` allows you to write code that behaves differently when being compiled for a report than when its being run interactively. This can be useful when developing parameterized reports.

## R Practices

- for maximum compatibility, use dashes rather than spaces or underscores in file names. $\LaTeX$, which is sometimes used as a part of Rmarkdown and Quarto documents, does not like spaces or underscores. This is most relevant when creating image files that may be loaded into reports. 

- Ensure that your code is reproducible by never saving / loading the environment. Scripts should include code to read in relevant files, and can save key objects for re-use later. In Rstudio, go to `Tools > Global Options` and in the `General` section, make sure that "Restore .Rdata into workspace on startup" is NOT checked, and make sure that "Save workspace to .Rdata on exit:" dropdown is set to "Never"

- Code that is meant to be shared should not include a hard-coded setwd() or file paths based on the local machine directory structure. Function calls that require file paths should be relative, such that someone with a copy of the project directory can run the script without needing to change those file paths.

- Ensure that figure titles are correct. When copy-pasting figure-generation code to make comparable figures for different parts of the data (e.g., different stocks or different fisheries), it's easy to accidentally leave old titles in place, leading to confusion. Consider using `paste()` or `glue()` with variable names or even r functions so that the figure title auto-updates appropriately.

```r
## "fragile" version of plotting an mtcar variable; copy-pasting and plotting a second variable requires careful updating of ggtitle()
dat.plot <- data |> 
    filter(fishery_title == "NT Area 10 Sport")
ggplot(dat.plot, aes(x = stock, y = AEQ))+
   geom_col()+
   ggtitle("Chinook AEQ of NT Area 10 Sport")+
   coord_flip()
   
## robust version:
fishery_plot <- "NT Area 10 Sport" ## define the fishery to plot in one place at the top
dat.plot <- data |> 
    filter(fishery_title == fishery_plot) ## use variable in our filter function 
ggplot(dat.plot, aes(x = stock, y = AEQ))+
   geom_col()+
   ggtitle(paste("Chinook AEQ of", fishery_plot))+ ## use paste and variable name
   coord_flip()
   
## alternative robust version:
dat.plot <- data |> 
    filter(fishery_title == "NT Area 10 Sport")
ggplot(dat.plot, aes(x = stock, y = AEQ))+
   geom_col()+
   ggtitle(paste("Chinook AEQ of", dat.plot$fishery_title[1]))+ ## obtain the fishery name directly from dat.plot
   coord_flip()
```

- When loading libraries, use `library()` rather than `require()`. Put all library calls at the top of the script, so that users immediately encounter errors if they have not yet installed relevant libraries. 

## Style guide

### Variable and Column Naming

Variables and columns of dataframes should be descriptive of their contents
while still being machine readable e.g. lacking all whitespace and special charaters. 

```r
# Good:
mark_rate <- tibble()
mortality.table <- read_csv('mortality_table.csv')

# Bad:
mr1.2 <- tibble()
`Mortality Table` <- read_csv('mortality_table.csv')
```
Often times column names imported into R from various sources have spaces,
special characters, capitalization, or are just bizarre. The `janitor` package's 
`clean_names()` function is a great automated solution to cleaning up dataframe 
names.

```r
data <- readr::read_csv(here::here('data/ugly_column_names.csv'))

data |>
  janitor::clean_names()
```
### Naming Conventions, Assignment Operators and Pipes

#### Naming Conventions
[Naming conventions](https://en.wikipedia.org/wiki/Naming_convention_(programming))
are an important part of understanding code, below are some common examples:

| Naming Convention    | Example           |
| -------------------- | ----------------- | 
| Snake Case           | big_red_dog       |
| Screaming Snake Case | BIG_REG_DOG       |
| Dot Case             | big.red.dog       |
| Camel Case           | bigRedDog         |
| Pascal Case          | BigRedDog         |

Although Hadley Wickham recommends snake case for R scripting, R has no 
official naming convention. When writing a script a naming convention
should be chosen and be consistently used throughout the documents
entirety.


#### Assignment Operators
There are a variety of assignment operators in the R scripting language,
`<-`, `=`, `<<-` as well as their directional reversals. The vast majority
of your assignments will either be `<-` or `=`, although essentially equal,
one should be chosen and used exclusively throughout the project.

#### Pipes

The original pipe `%>%` is a function of the `magittr` package. The 'native pipe'
`|>` was introduced in R 4.0. These two perform essentially the same function,
but with different placeholders which can lead to various errors in scripts
when mix. One pipe should be used in the document.



<!-- i think we should allow some flexibility, and go from there, thoughts? -->
<!--
=======
The following are good general practices, but specific style choices are often a matter of taste. Consistency is the most important part -- use the same style throughout your script.



-   Snakecase for variable names. E.g. `chinook_landed_catch`. *Using separators in variable names makes them easier to read. Using periods as separators becomes ambiguous when dealing with S3 methods*
-   Use `<-` for assignment rather than `=`. Always ensure there is a space before and after the assignment operator. *This helps with visually distinguishing the assignment `x <- 10` and the test `x < -10`.*
-   There's not cost to spreading code across more lines. When in doubt, break really long / complex lines into more, shorter lines; create intermediate variables if necessary. When using pipes, put each pipe operation on its own line.
-   We recommend using "Code > Reindent Code" (select all, then Ctrl-I) and "Code > Reformat Code" (select all, then Ctrl-shift-A) to make code easier to read
-   Avoid creating variables that share names with common functions (e.g., use `x_mean = mean(x)` instead of `mean = mean(X)`, and `cur_plot = ggplot(...` instead of  `plot = ggplot(...`).
-   Where possible, use names instead of numbers when indexing named vectors, dataframes, or lists. (e.g., `mtcars$cyl` or `mtcars[, cyl]` rather than `mtcars[, 2]`)
-->

## Visualization

We often need to create graphics to show aspects of the data. There is no one-size-fits-all solution to visualization, but here are some good practices. The following is technically agnostic to packages, but suggestions are centered on ggplot2-based approaches

-   Axes should have clear, interpretable labels
-   Colors should be easily distinguishable, including by folks with common forms of color vision deficiencies.
    -   The `viridis` [package](https://cran.r-project.org/web/packages/viridis/vignettes/intro-to-viridis.html) makes it very easy to create high-contrast accessible graphics.
    -   Viridis colors are often less visually pleasing for discrete variables. There are many other options available. One such can be found in the `RColorBrewer` [package](https://cran.r-project.org/web/packages/RColorBrewer/RColorBrewer.pdf); try `display.brewer.all(colorblindFriendly = TRUE)` to see some useful palettes.
    -   Collin often likes some combination of the colors [here](https://github.com/FRAMverse/snippets/blob/main/R/color-blind-palette.R) for discrete variables.
    -   The `colorBlindness` [package](https://cran.r-project.org/web/packages/colorBlindness/vignettes/colorBlindness.html) has the hugely useful `cvdPlot` function, which will display a ggplot2 object under different simulated color vision deficiencies to help check accessibility.
-   Text size should be large enough to read comfortably. When using ggplot, this can easily be achieved by using any of the built-in themes and including the optional argument `base_size`. A good starting point is `theme_bw(base_size = 16)`.
-   When there is complexity in interpreting a plot, this should be included in text associated with the plot. This is easy to do in Quarto or Rmarkdown, as we can add caveates or comments right below or above the associated R chunk. In quarto reports, an explicit figure caption can be added with `#| fig-cap: "caption contents go here"`.
-  Sometimes we work with timeseries data using day of year as a numeric (e.g., converting dates to values from 1 to 365). Plotting results on a doy scale makes them difficult to interpret; instead, we can use the [doy_2md() function here](https://github.com/FRAMverse/snippets/blob/main/R/doy_2md.R) to translate back. In it's simplest form, using this function just requires including a `scale_x_continuous(labels = doy_2md)` call in your ggplot layers.

## Creating custom functions

- Functions should have clear names, preferably involving a verb. This name should not be the same as common R functions (e.g., don't create a custom plotting function and call it `plot`)
- functions should not rely on objects in the global environment; if the function needs an object, ensure that the object is an argument for the function.
- Whenever possible, avoid writing functions that rely on side-effects, particularly creating new variables in the global environment (e.g., with `assign()`). If you need a function to create several objects, have the function return a list of those objects. (Note that file manipulation is an obvious exception to the general aim to avoid side-effects in functions; functions can read or write)
- When writing functions to create graphics, the user has much better control if the function creates and returns a gglot object instead of directly manipulating a graphics window using base R plotting functions. 
- For longer scripts, consider separating the code into multiple scripts and using `source()` to call them from a single main script. This can be especially effective for scripts that contain many custom function definitions -- move the functions to a separate script that gets `source()`ed at the top of the remaining code leads to a primary script that is easy to read, and a companion script that is just the definitions of functions.

## Version control

When multiple people are collaborating on a project, it gets very important to be able to integrate changes in an intelligent way (emailing different versions of a zipped folder back and forth is not a good idea). Git and Github are the best tool for this, and Rstudio now supports using github to manage projects.

*Populate with links, basics of the work flow*
- branching and pull requests
- forking and pull requests
- happy git with R link

## Package development guidelines

- When in doubt, we should have more packages that are small and focused.
- Before developing something new, make sure there isn't an existing tool we can use (I'm looking at YOU/ME, Collin)

#### Tips for clean checks

- functions should always have package prefixes, as in `dplyr::mutate()`
- If using dplyr calls, *(something about selection vs filtering, using quotes or `.data$` respectively. I need to look it up again)*

#### Other tips

- The optional `inst/` folder of a package can hold misc files which are consistently accessible from the package functions. This allows us to develop packages to automate reporting -- we have a .qmd file in the `inst/` folder, and then a package in the function can copy that .qmd file to an appropriate folder (based on arguments), compile an html or pdf from the .qmd file, and then delete the .qmd file. See `TAMMsupport::tamm_report` for an example of this.

# Appendix: help with implementation



