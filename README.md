# coding-practices

(<https://framverse.github.io/coding-practices/>)

Our evolving coding best practices document


# Goals

The goal of these best practices is to act as a guideline to produce code and 
analyses that are highly transparent, transferable, reproducible and approachable.


<!-- ## General practices -->

<!-- Todo:
 - Populate / link directions on some of the suggestions (e.g., git, renv, etc)
-->

# Best practices

## Overview

Good coding practices make collaboration easier and faster, and reduce the frequency and consequences of bugs and problems. At least initially, adhering to good practices can feel like it add unnecessary steps that slow progress. In the long run, however, we find that these practices save time. Further, they increase the transparency of our code, which in turn increases the overall transparency of our work. 

Below, we outline best practices organized into related topics. When it can be done succinctly, we provide explanations for *why* the practices save time. After the guidelines, we include some short tutorials and examples to show *how* to implement some of the less obvious practices.

## Non-coding

The following are important for coding and non-coding projects alike.

- When sharing files via email it's useful to have a consistent, informative version naming scheme. We recommend `filename_date_editorintials`.
- We encounter many issues when incorporating spreadsheet content into programming pipelines. Some of this stems from the difference between what is easily human-readable and what is easily machine-readable, and sometimes it may be appropriate to make the spreadsheets as human-readable as possible. However, some practices can make spreadsheets more machine-readable without affecting human readability. 
    - Ensure that headers are consistent in different files that are meant to be combined. This includes capitalization and the use of spaces. Copy-pasting from a template file is a good way to ensure exactly identical headers in this case.
    - Ensure that categories have a consistent name in a given column. For example, we have encountered data sheets with a "Yes/No" type column in which "Yes" is a mix of "Y", "Yes", "yes", "yes ", and "yees". When read into R or another language, these will be treated as five different categories instead of one. Consider using data validation in Excel to constrain user inputs to intended values. This can also be used to ensure that fields that should contain numbers do not end up with character strings.
    - For more suggestions on good spreadsheet practices, see [Broman and Woo 2018](https://doi.org/10.1080/00031305.2017.1375989)
    
## Data Sharing Guidance
Sharing what type of data and who it can be shared with is often confusing.
Generally sport data can be shared with everyone freely, while commercial data
has restrictions on who and *how* the data can be shared, for example under the
Magnuson-Stevens Act (MSA)

### Sport
Sport data can usually freely shared to the public, although there might be
restrictions around sharing charter fishing data via the MSA.

### Commerical
The MSA has to be considered when sharing commercial data, often the data has
to be aggregated in way to not specifically identify fishers. In the co-management
realm this is rarely and issue as much of the data is aggregated, but for sharing
with the pubic guidance should be requested through WDFWs Records Office.

### Treaty
Public requests for treaty data should be directed to the individual tribes
themselves or WDFW's Records Office. Tribal data can be shared freely with the 
data's respective tribe, sharing one tribes data with another tribe should be 
done under caution with the guidance of NWIFC staff.



## Project management

For any kind of substantial work involving more than one file, use Rprojects, the `here` package, and `renv` to make scripts easily shareable. The goal is that you can zip up a folder, send it to someone else, and they can run any scripts without making any changes.

When developing a document to report results or findings to a general user, use Rmarkdown or Quarto to create a report that blends R code with explanations and graphics.

When a script is likely to be re-used (e.g. not a one-off analysis) or if it is going to be shared, create a commented version with instructions on use. This should be stored somewhere accessible. Collin Edwards maintains the `snippets` [github repository](https://github.com/FRAMverse/snippets) for this kind of thing, or it could live in a Teams folder. If code is likely to be useful to the team or others, it may also be appropriate to incorporate this code into an R package, or develop a new R package for this code. Converting code to packages is much more involved that storing a code snippet or re-useable script somewhere, but makes it much easier for incorporation into other code.

To make scripts easier to re-use, replace hard-coded specifics with variables that are defined at the top of the script. For example, if Collin wrote a script to read in the Mortalities table of a FRAM database and plot the landed catch for a specific fishery, he would probably initially write that script using the file name and fishery name wherever he needed it (e.g., `connect_fram_db("FramDBExample.Mdb")` and `data |> select(fishery_id == 19) |> ...`). To make this script easier to re-use, he could add lines of code near the top of the script, with

```r
file_use = "FramDBExample.Mdb"
fishery_use = 19
```

and then replace any hard-coded uses of the filename and fishery ID with those variables (e.g., `connect_fram_db(file_use)` and `data |> select(fishery_id == fishery_use) |> ...`). This makes it very easy to re-use for a different case -- simply update the lines defining `file_use` and `fishery_use`.

#### Help I've been given a project that uses `{renv}`!

If someone has shared an Rproject that uses `{renv}`, you may find that it tells you that you don't have the appropriate packages installed,
and trying to install them the normal way may not work. Don't panic. `{renv}` handles packages separately, in a way that makes sure that you have exactly the same
version installed as whoever made the project. (The motivation for this is that packages can change over time, and sometimes functions will change how they work; if that's happened, code written for older versions of the package won't work as intended when using newer versions of the packages). 

To handle package installation for an Rproject using `{renv}`

- first, make sure that you have installed the `{renv}` package on your computer
- Second, run `renv::restore()` in the console. This will initiate the package installation process, which will ask for confirmation before installing. Once you accept, `{renv}` will then install the exact versions of the packages used by the project and associate those versions with the project. You should be good to go!

If you are working in the project and use previously unused packages (e.g., you add a `library()` call for a new package), you should use `renv::snapshot()` to update the `{renv}` system with the new package(s). That way when you share the project with someone else, your additional packages will be installed with `renv::restore()`.


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
software. Sometimes zipped files can successfully be emailed if the file name is changed to end in something else (e.g., `.zap`) and including instuctions to change the file name back.


#### Common project directory structure

When working across multiple projects, it can be helpful if each project has a similar file structure. Your needs for individual projects may vary, but the following project structure is often a good option (or at least a good starting point). 

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

The idea with this folder structure is that:

- The `scripts/` folder contains R scripts used in the project. 
- `original_data/` contains the data files provided for this project (but not data files that are generated or cleaned in this project)
- `cleaned_data/` contains any data files that are generated as part of this project (e.g., by cleaning and integrated data from `original_data/`), which can then be used for subsequent analyses in this project. The idea with this separation of data is that it makes it easier to ensure that original data files are *never* modified.
- `figures/` contains image objects created as a part of this project. Depending on the project, this folder may not be used, but sometimes it's appropriate to generate hundreds of figures programmatically (e.g. separate bar plots of fishery impacts for each stock).
- `results/` contains non-image objects created as a part of this project. For example, if a project synthesizes data and produces summary `.csv` or `.xlsx` files, they would go in `results/` 

To streamline giving new projects this folder structure, the `framrsquared` package [found here](https://github.com/FRAMverse/framrsquared) has the `initialize_project()` function. By default, this generates the folder structure above; optional arguments allow users to specify a different folder structure, copy template files for quarto documents, and initialize `renv`.

To reiterate, this project structure is not mandatory for good coding. It's simply a useful option.

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
-   using `if(interactive())` allows you to write code that behaves differently when being compiled for a report than when its being run interactively. This can be useful when developing parameterized reports, as the parameters will live in the YAML header, which is not run in interactive mode.

## R Practices

- for maximum compatibility, use dashes rather than spaces or underscores in file names. $\LaTeX$, which is sometimes used as a part of Rmarkdown and Quarto documents, does not like spaces or underscores. This is most relevant when creating image files that may be loaded into reports. 

- Ensure that your code is reproducible by never saving / loading the environment. Scripts should include code to read in relevant files, and can save key objects for re-use later. In Rstudio, go to `Tools > Global Options` and in the `General` section, make sure that "Restore .Rdata into workspace on startup" is NOT checked, and make sure that "Save workspace to .Rdata on exit:" dropdown is set to "Never"

- Code that is meant to be shared should not include a hard-coded setwd() or file paths based on the local machine directory structure. Function calls that require file paths should be relative, such that someone with a copy of the project directory can run the script without needing to change those file paths.

- When loading libraries, use `library()` rather than `require()`. Put all library calls at the top of the script, so that users immediately encounter errors if they have not yet installed relevant libraries.

- To improve transparency, give R scripts a header with your name, the date, and a brief explanation of the script's purpose. To streamline this process, consider adding a `header` [Rstudio snippet](https://rstudio.github.io/rstudio-extensions/rstudio_snippets.html). We have a template snippet [here](https://github.com/FRAMverse/snippets/blob/main/Rstudio/header-snippet.txt); you can update this with your own name and then add it to your Rstudio's snippets.

- When running simulations or other code in which the outcomes of a run can differ due to randomness, it can be difficult and frustrating for others to attempt to replicate your work (or replicate an error). One key tool is to use `set.seed()` at the beginning of a script. This will ensure that the randomness is repeated exactly every time the script is run. Note that since setting the seed prevents alternative random outcomes, it is unwise to do so when developing code, as your code will only ever represent one set of random outcomes.

- In rare cases, R packages will work only for 32 bit R or only for 64 bit R (historically, this was an issue for connecting to databases). Code that uses these packages will then only run on some computers, severely hampering our transparency and code sharing. Because of this, these packages should be avoided whenever reasonable. When there is no other option, there should be very clear commenting or documentation identifying this issue, so that users know immediately whether or not they will be able to run the code. If R functions exist for both 32 bit and 64 bit R but have different functions or syntax, consider supportinb both architectures by including an `if` statement; `.Machine$sizeof.pointer` will return 8 in 64-bit R, and 4 in 32-bit R. 


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
should be chosen and then used consistently throughout the entire document.


#### Assignment Operators

There are a variety of assignment operators in the R scripting language,
`<-`, `=`, `<<-` as well as their directional reversals. The vast majority
of your assignments will either be `<-` or `=`, although essentially equal,
one should be chosen and used exclusively throughout the project. In Rstudio, [ctrl][=] is a hotkey to create `<-`.

#### Pipes

The original pipe `%>%` is a function of the `magittr` package. The 'native pipe'
`|>` was introduced in R 4.0. These two perform essentially the same function,
but with different placeholders which can lead to various errors in scripts
when mix. One pipe should be used in the document. In Rstudio, [ctrl][shift][m] generates a pipe; you can set which type of pipe is generated in Tools > Global Options > Code, and check/uncheck the "Use native pipe operator..." box.


## Visualization

We often need to create graphics to show aspects of the data. There is no one-size-fits-all solution to visualization, but here are some good practices. The following is technically agnostic to packages, but suggestions are centered on ggplot2-based approaches

-   Axes should have clear, interpretable labels.
-   Colors should be easily distinguishable, including by folks with common forms of color vision deficiencies.
    -   The `viridis` [package](https://cran.r-project.org/web/packages/viridis/vignettes/intro-to-viridis.html) makes it very easy to create high-contrast accessible graphics.
    -   Viridis colors are often less visually pleasing for discrete variables. There are many other options available. One such can be found in the `RColorBrewer` [package](https://cran.r-project.org/web/packages/RColorBrewer/RColorBrewer.pdf); try `display.brewer.all(colorblindFriendly = TRUE)` to see some useful palettes.
    -   Collin often likes some combination of the colors [here](https://github.com/FRAMverse/snippets/blob/main/R/color-blind-palette.R) for discrete variables.
    -   The `colorBlindness` [package](https://cran.r-project.org/web/packages/colorBlindness/vignettes/colorBlindness.html) has the hugely useful `cvdPlot` function, which will display a ggplot2 object under different simulated color vision deficiencies to help check accessibility.
-   Text size should be large enough for others to read comfortably. In our experience, this always means making the font size seemly too large. When using ggplot, this can easily be achieved by using any of the built-in themes and including the optional argument `base_size`. A good starting point is `theme_bw(base_size = 16)`.
-   When there is some kind of nuance or complexity in interpreting a plot (e.g., an axis label can be misunderstood), this should be included in text associated with the plot. This is easy to do in Quarto or Rmarkdown, as we can add caveats or comments right below or above the associated R chunk. In quarto reports, an explicit figure caption can be added with `#| fig-cap: "caption contents go here"` in the associated R chunk.
-  Sometimes we work with timeseries data using day of year as a numeric (e.g., converting dates to values from 1 to 365). Plotting results on a doy scale makes them difficult to interpret; instead, we can use the [doy_2md() function here](https://github.com/FRAMverse/snippets/blob/main/R/doy_2md.R) to translate back. In it's simplest form, using this function just requires including a `scale_x_continuous(labels = doy_2md)` call in your ggplot layers.

- Ensure that figure titles are correct. When copy-pasting figure-generation code to make comparable figures for different parts of the data (e.g., different stocks or different fisheries), it's easy to accidentally leave old titles in place, leading to confusion. Consider using `paste()` or `glue()` with variable names or even r functions so that the figure title auto-updates appropriately.

```r
## "fragile" version; copy-pasting and plotting a different fishery requires careful updating of ggtitle()
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


## Creating custom functions

- Functions should have clear names, preferably based around a verb (e.g., `make_fishery_plot()`, not `fishery_plot()`). This name should not be the same as common R functions (e.g., don't create a custom plotting function and call it `plot`)
- functions should not rely on objects in the global environment. If the function needs an object, ensure that the object is an argument for the function.
- Whenever possible, avoid writing functions that rely on side-effects, particularly side effects that create new variables in the global environment or modify existing variables in the environment (e.g., with `assign()`). If you need a function to create several objects, have the function return a list of those objects. (Note that file manipulation is an key exception to the general aim to avoid side-effects in functions; it is often appropriate to have functions read or write files)
- When writing functions to create graphics, the user has much better control if the function creates and returns a ggplot object instead of directly manipulating a graphics window using base R plotting functions. 
- For longer scripts, consider separating the code into multiple scripts and using `source()` to call them from a single main script. This can be especially effective for scripts that contain many custom function definitions -- move the functions to a separate script that gets `source()`ed at the top of the remaining code leads to a primary script that is easy to read, and a companion script that is just the definitions of functions.

## Version control

When multiple people are collaborating on a project, it gets very important to be able to integrate changes in an intelligent way (emailing different versions of a zipped folder back and forth is not a good idea). Git and Github are the best tool for this, and Rstudio now supports using github to manage projects. 

### Setting up

Setting up Git and linking it to Rstudio is an involved task, and we recommend [Happy Git with R](https://happygitwithr.com/) as a resource.

### Getting started

At its simplest, git is a way to keep track of changes, and merge different, non-conflicting changes to the same documents. In this sense, you can think of it as a mix between dropbox and a Google document, but with more to learn but a lot more control and functionality. We recommend Chapter 20 of Happy Git with R for an overview. For simple tasks (e.g., working on your own project), a standard workflow is to pull (this makes sure your local version of the repository is up to date), and work in the repo. At good stopping points or key checkpoints in your work (you completed a specific task, or are stopping work on this project for the day), add any new files that were created, commit the repository, pull the remote to make sure you are up to date, and then push. See "Using git in Rstudio" for the terminal commands for this workflow.




### Using git in Rstudio

Once an Rproject is linked to a git repository, Rstudio will have a git button in the top menu (near the "go to file/function" field). Clicking this button, then "Commit" opens up an interface to create and control a git commit, then push it to the remote repo. However, often this interface is very slow/laggy when a project has many files. An alternative is to manage the commit in the "terminal" tab of the console window. Here you can type git commands, which are typically much faster for Rstudio to enact. Here is a typical commit process in the terminal, with explanations for each step.

`git add -A`
This adds any new files to tracking (ignoring files that are covered in `.gitignore`), so that they are included in the commit.
`git commit -a -m "commit message"`
This commits the current state of all tracked files. Replace the text in quote marks to an appropriate commit message. (e.g., "Addressing issue #4, modified intitialize_project() function").
`git pull`
This updates your local version with the remote version, in case someone else has made changes. If there are changes and they conflict with your changes, git will ask you to address those.
`git push`
This updates the remote version with your commit and its associated changes.

### Branching, forking, pull requests

*Populate with links, basics of the work flow*
- branching and pull requests
- forking and pull requests
- Adapt git use example from BDS coding practices.



### git switch

If you have started making changes to a git repository and realize before committing the changes that your work should really be on a new branch, you can use the following git commands to achieve this:

`git switch -c newbranchname` 

where "newbranchname" is replaced by an appropriate name for your new branch.

## Package development guidelines

- When in doubt, we should have more packages that are small and focused.
- Before developing something new, make sure there isn't an existing tool we can use (I'm looking at YOU-ME, Collin)


#### Tips for clean checks

- functions should always have package prefixes, as in `dplyr::mutate()`
- If using dplyr calls, *(something about selection vs filtering, using quotes or `.data$` respectively. I need to look it up again)*

#### Other tips

- The optional `inst/` folder of a package can hold misc files which are consistently accessible from the package functions. This allows us to develop packages to automate reporting -- we have a .qmd file in the `inst/` folder, and then a package in the function can copy that .qmd file to an appropriate folder (based on arguments), compile an html or pdf from the .qmd file, and then delete the .qmd file. See `TAMMsupport::tamm_report` for an example of this.

# Appendix: help with implementation



