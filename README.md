# coding-practices

(<https://framverse.github.io/coding-practices/>)

Our evolving coding best practices document

## General practices

For any kind of substantial work involving more than one file, use Rprojects, the `here` package, and `renv` to make scripts easily shareable. The goal is that you can zip up a folder, send it to someone else, and they can run it without making any changes.

When developing a document that should present results to a general user, use Rmarkdown or Quarto to create a report that blends R code with explanations and graphics.

When code is likely to be re-used (e.g. not a one-off analysis), create a commented version with instructions on use. This should be stored somewhere accessible. Collin maintains the `snippets` [github repository](https://github.com/FRAMverse/snippets) for this kind of thing, or it could live in a Teams folder. It may also be appropriate to incorporate this code into an R package, or develop a new R package for this code. Converting code to packages is much more involved that storing a code snippet somewhere, but makes it much easier for incorporation into other code.

### Using fundamental tools

*Need to give directions for starting Rprojects, using the here package, using `renv`*

Code that is meant to be shared should not include a hard-coded setwd() or file paths based on the local machine directory structure. Function calls that require file paths should be relative, such that someone with a copy of the project directory can run the script without needing to change those file paths.

### Common project directory structure

When working across multiple projects, it can be helpful if each project has a similar file structure. Ty and Collin will discuss what that should be, but good foundation is Ty's approach, which has a `data/` and a `scripts/` subfolder. It may be helpful to also include a standardized readme with basic information (when project was started, what goal was, who was working on it). When Collin was working in an academic setting, he had a [code snippet](https://gist.github.com/cbedwards/7e64215e062c42da54dbd01626ef6a72) that he ran whenever starting a new project, which created his standardized folder structure and auto-populated a few key template files. We could think about writing something similar.

### Tips

-   using `if(interactive())` allows you to write code that behaves differently when being compiled for a report than when its being run interactively. This can be useful when developing parameterized reports.

## Style guide

(Ty's plan, Collin has regrets)

-   Snakecase for variable names. E.g. `chinook_landed_catch`.
-   `<-` for assignment rather than `=`

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

## Version control

When multiple people are collaborating on a project, it gets very important to be able to integrate changes in an intelligent way (emailing different versions of a zipped folder back and forth is not a good idea). Git and Github are the best tool for this, and Rstudio now supports using github to manage projects.

*Populate with links, basics of the work flow*
- branching and pull requests
- forking and pull requests
- happy git with R link

## Package development guidelines

- When in doubt, we should have more packages that are small and focused.
- Before developing something new, make sure there isn't an existing tool we can use (I'm looking at YOU/ME, Collin)

### Tips for clean checks

- functions should always have package prefixes, as in `dplyr::mutate()`
- If using dplyr calls, *(something about selection vs filtering, using quotes or `.data$` respectively. I need to look it up again)*

### Other tips

- The optional `inst/` folder of a package can hold misc files which are consistently accessible from the package functions. This allows us to develop packages to automate reporting -- we have a .qmd file in the `inst/` folder, and then a package in the function can copy that .qmd file to an appropriate folder (based on arguments), compile an html or pdf from the .qmd file, and then delete the .qmd file. See `TAMMsupport::tamm_report` for an example of this.