
<!-- README.md is generated from README.Rmd. Please edit that file -->

# pactaCore

The goal of pactaCore is run the core steps of the [PACTA
methodology](https://2degrees-investing.org/resource/pacta/) with a
single command, in a reproducible way.

## Installation

You can install the development version from
[GitHub](https://github.com/) with:

``` r
# install.packages("devtools")
devtools::install_github("2DegreesInvesting/pactaCore")
```

## Setup

As usual, use the pactaCore package with `library()`. Here we also use
the fs package for convenient file system operations.

``` r
library(pactaCore)
library(fs)
```

A good setup for a pacta project looks like this (we’ll do it with
`create_pacta()` but you may do it by hand):

``` r
pacta <- path_home("pacta_tmp")
example_inputs <- dir_ls(
  system.file("extdata", package = "pactaCore"),
  regexp = "Input"
)
create_pacta(pacta, input_paths = example)

dir_tree(pacta, all = TRUE)
#> /home/mauro/pacta_tmp
#> ├── .env
#> ├── input
#> │   ├── TestPortfolio_Input.csv
#> │   └── TestPortfolio_Input_PortfolioParameters.yml
#> └── output
```

``` r
#> path/to/private/pacta-data/
#> ├── ...
#> ...
```

-   The .env file locates the input/, output/, and pacta-data/
    directories, e.g.:

``` r
env <- path(pacta, ".env")
readLines(env)
#> [1] "PACTA_OUTPUT=/home/mauro/pacta_tmp/output"
#> [2] "PACTA_INPUT=/home/mauro/pacta_tmp/input"  
#> [3] "PACTA_DATA=/home/mauro/git/pacta-data"
```

-   The input/ directory must contain portfolio files like
    [TestPortfolio\_Input.csv](https://github.com/2DegreesInvesting/pactaCore/blob/master/working_dir/20_Raw_Inputs/TestPortfolio_Input.csv),
    and parameters files like
    [TestPortfolio\_Input\_PortfolioParameters.yml](https://github.com/2DegreesInvesting/pactaCore/blob/master/working_dir/10_Parameter_File/TestPortfolio_Input_PortfolioParameters.yml).

-   The output/ directory typically starts empty.

-   The pacta-data/ directory is private. With permission get if from
    GitHub with:

``` bash
# From the terminal
git clone git@github.com:2DegreesInvesting/pacta-data.git
```

## Usage

Use `run_pacta()` with your environment file.

``` r
run_pacta(env)
```

Note the output/ directory is now populated with results:

``` r
dir_tree(pacta)
#> /home/mauro/pacta_tmp
#> ├── input
#> │   ├── TestPortfolio_Input.csv
#> │   └── TestPortfolio_Input_PortfolioParameters.yml
#> └── output
#>     └── working_dir
#>         ├── 00_Log_Files
#>         │   └── TestPortfolio_Input
#>         ├── 10_Parameter_File
#>         │   └── TestPortfolio_Input_PortfolioParameters.yml
#>         ├── 20_Raw_Inputs
#>         │   └── TestPortfolio_Input.csv
#>         ├── 30_Processed_Inputs
#>         │   └── TestPortfolio_Input
#>         │       ├── audit_file.csv
#>         │       ├── audit_file.rda
#>         │       ├── bonds_portfolio.rda
#>         │       ├── coveragegraph.json
#>         │       ├── coveragegraphlegend.json
#>         │       ├── coveragetextvar.json
#>         │       ├── emissions.rda
#>         │       ├── equity_portfolio.rda
#>         │       ├── fund_coverage_summary.rda
#>         │       ├── invalidsecurities.csv
#>         │       ├── invalidsecurities.json
#>         │       ├── overview_portfolio.rda
#>         │       ├── portfolio_weights.json
#>         │       └── total_portfolio.rda
#>         ├── 40_Results
#>         │   └── TestPortfolio_Input
#>         │       ├── Bonds_results_company.rda
#>         │       ├── Bonds_results_map.rda
#>         │       ├── Bonds_results_portfolio.rda
#>         │       ├── Equity_results_company.rda
#>         │       ├── Equity_results_map.rda
#>         │       └── Equity_results_portfolio.rda
#>         └── 50_Outputs
#>             └── TestPortfolio_Input
```

<details>

For each corresponding `<pair-name>`, the portfolio and parameter files
must be named `<pair-name>_Input.csv` and
`<pair-name>_Input_PortfolioParameters.yml`, respectively. For example:

-   This pair is valid: `a_Input.csv`,
    `a_Input_PortfolioParameters.yml`.

-   This pair is invalid: `a_Input.csv`,
    `b_Input_PortfolioParameters.yml`.

In the parameter files, whatever values you give to `portfolio_name_in`
and `investor_name_in` will populate the columns `portfolio_name` and
`investor_name` of some output files. For example:

-   A parameter file:

``` r
readLines(example_inputs[[2]])
#> [1] "default:"                                      
#> [2] "    parameters:"                               
#> [3] "        portfolio_name_in: TestPortfolio_Input"
#> [4] "        investor_name_in: Test"                
#> [5] "        peer_group: pensionfund"               
#> [6] "        language: EN"                          
#> [7] "        project_code: CHPA2020"                
#> [8] ""
```

-   A few rows of some relevant output files and columns:

``` r
paths <- fs::dir_ls(pactaCore:::results_path(pacta))
datasets <- lapply(paths, readRDS)
```

``` r
lapply(datasets, function(x) head(x[c("portfolio_name", "investor_name")]))
#> $`/home/mauro/pacta_tmp/output/working_dir/40_Results/TestPortfolio_Input/Bonds_results_company.rda`
#> # A tibble: 6 × 2
#>   portfolio_name      investor_name
#>   <chr>               <chr>        
#> 1 TestPortfolio_Input Test         
#> 2 TestPortfolio_Input Test         
#> 3 TestPortfolio_Input Test         
#> 4 TestPortfolio_Input Test         
#> 5 TestPortfolio_Input Test         
#> 6 TestPortfolio_Input Test         
#> 
#> $`/home/mauro/pacta_tmp/output/working_dir/40_Results/TestPortfolio_Input/Bonds_results_map.rda`
#> # A tibble: 6 × 2
#>   portfolio_name      investor_name
#>   <chr>               <chr>        
#> 1 TestPortfolio_Input Test         
#> 2 TestPortfolio_Input Test         
#> 3 TestPortfolio_Input Test         
#> 4 TestPortfolio_Input Test         
#> 5 TestPortfolio_Input Test         
#> 6 TestPortfolio_Input Test         
#> 
#> $`/home/mauro/pacta_tmp/output/working_dir/40_Results/TestPortfolio_Input/Bonds_results_portfolio.rda`
#> # A tibble: 6 × 2
#>   portfolio_name      investor_name
#>   <chr>               <chr>        
#> 1 TestPortfolio_Input Test         
#> 2 TestPortfolio_Input Test         
#> 3 TestPortfolio_Input Test         
#> 4 TestPortfolio_Input Test         
#> 5 TestPortfolio_Input Test         
#> 6 TestPortfolio_Input Test         
#> 
#> $`/home/mauro/pacta_tmp/output/working_dir/40_Results/TestPortfolio_Input/Equity_results_company.rda`
#> # A tibble: 6 × 2
#>   portfolio_name      investor_name
#>   <chr>               <chr>        
#> 1 TestPortfolio_Input Test         
#> 2 TestPortfolio_Input Test         
#> 3 TestPortfolio_Input Test         
#> 4 TestPortfolio_Input Test         
#> 5 TestPortfolio_Input Test         
#> 6 TestPortfolio_Input Test         
#> 
#> $`/home/mauro/pacta_tmp/output/working_dir/40_Results/TestPortfolio_Input/Equity_results_map.rda`
#> # A tibble: 6 × 2
#>   portfolio_name      investor_name
#>   <chr>               <chr>        
#> 1 TestPortfolio_Input Test         
#> 2 TestPortfolio_Input Test         
#> 3 TestPortfolio_Input Test         
#> 4 TestPortfolio_Input Test         
#> 5 TestPortfolio_Input Test         
#> 6 TestPortfolio_Input Test         
#> 
#> $`/home/mauro/pacta_tmp/output/working_dir/40_Results/TestPortfolio_Input/Equity_results_portfolio.rda`
#> # A tibble: 6 × 2
#>   portfolio_name      investor_name
#>   <chr>               <chr>        
#> 1 TestPortfolio_Input Test         
#> 2 TestPortfolio_Input Test         
#> 3 TestPortfolio_Input Test         
#> 4 TestPortfolio_Input Test         
#> 5 TestPortfolio_Input Test         
#> 6 TestPortfolio_Input Test
```

</details>
