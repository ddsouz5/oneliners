Installing R from source, cd into tar download once extracted
    
    sudo ./configure --prefix=/opt/R/3.5.1 --enable-R-shlib
    sudo make
    sudo make install

Remove base libraries in root folder and install in user folder

    remove.packages(c("boot", "class", "cluster", "codetools", "KernSmooth", "lattice", "MASS", "Matrix", "mgcv", "nnet", "rpart", "spatial"), lib = "/opt/R/3.5.1/lib64/R/library")
    install.packages(c("boot", "class", "cluster", "codetools", "KernSmooth", "lattice", "MASS", "Matrix", "mgcv", "nnet", "rpart", "spatial"))

    remove.packages(c("nlme","survival"), lib = "/opt/R/3.5.1/lib64/R/library")
    install.packages(c("nlme","survival"))

Install previous versions of packages

    require(devtools)
    install_version("latticeExtra", version = "0.6-28", repos = "http://cran.us.r-project.org")

For cyutils
    
    library(flowCore)
    library(ggcyto)
    x <- read.FCS("~/236_myeloid_blood.fcs", transformation = T)
    autoplot(x, "Time", "Yb172Di", bins = 50)


Packages required

    install.packages(c("shiny", "shinydashboard", "shinyfiles", "mclust"))

See source code
    
    methods(autoplot)
    getAnywhere("autoplot.flowSet")

Try catch

    tryCatch(
        expr = {
            # Your code...
            # goes here...
            # ...
        },
        error = function(e){ 
            # (Optional)
            # Do this if an error is caught...
        },
        warning = function(w){
            # (Optional)
            # Do this if an warning is caught...
        },
        finally = {
            # (Optional)
            # Do this at the end before quitting the tryCatch structure...
        }
    )
 
* <https://stackoverflow.com/questions/12193779/how-to-write-trycatch-in-r>

High performance loops in R

    1.Initialize your data structures rather than growing them as you loop (avoid appending to a given structure; pre-allocate space instead and then access!)
    2.Take advantage of vectorized operations when possible and take the work outside of loops.
    3.Indexing vectors is faster than indexing dataframes. (small performance gain)

* <https://uoftcoders.github.io/studyGroup/lessons/r/loops/HighPerformanceLoops.html>

Timing code

    1. system.time(mean(1:1000000))
    2. 
        pmt <- proc.time()
        mean(1:1000000)
        proc.time() - pmt
    3. 
        microbenchmark(mean(1:1000000))




