# HierFabs
  A hierarchical Forward and Backward Stagewise (HierFabs) algorithm for identifying hierachical interaction of genomics data
 
  HierFabs uses coordinate descent with a fixed step size which consists of both forward and backward steps. At each step, the first-order Taylor's expansion is used to reflect the main part of the increment. 

# Installation

    #install Rtools 3.5 (http://cran.r-project.org/bin/windows/Rtools)
    #install.packages("devtools")
    #install.packages("Rcpp")
    library(devtools)
    install_github("Tesla-Xiao/HierFabs")

# Usage

   - [x] [HierFabs-manual](https://github.com/Tesla-Xiao/HierFabs/blob/master/inst/HierFabs-manual.pdf) ------------ Details of the usage of the package.
   
# Example

    library(HierFabs)

    set.seed(1)
    n = 500
    p = 100
    x = matrix(rnorm(n*p),n,p)
    eta = x[,1:4] %*% rep(1,4) + 3*x[,1]*x[,2] + 3*x[,1]*x[,4]
    y =  eta + rnorm(n)
    xtest = matrix(rnorm(n*p),n,p)
    eta.test = xtest[,1:4] %*% rep(1,4) + 3*xtest[,1]*xtest[,2] + 3*xtest[,1]*xtest[,4]
    ytest =  eta.test + rnorm(n)
    fit.gg.strong = HierFabs(x, y)
    y.pred.gg.s = predict(fit.gg.strong, xtest)
    mean((y.pred.gg.s-ytest)^2)
    
    ## Weak hierarchy
    fit.gg.weak = HierFabs(x, y, hier="weak")
    y.pred.gg.w = predict(fit.gg.strong, xtest)
    mean((y.pred.gg.w-ytest)^2)
    
    ## Cox model with Gene-Environment interactions
    pz = 10
    z = matrix(rnorm(n*pz),n,pz)
    eta.ge = x[,1] + x[,2] + z[,1] + z[,2] + 3*x[,1]*z[,1] + 3*x[,2]*z[,2]
    err = log(rweibull(n, shape = 1, scale = 1))
    y0 = exp(-eta.ge + err)
    cens = quantile(y0, 0.9)
    y.ge = pmin(y0, cens)
    status = 1 * (y0<=cens)
    ztest = matrix(rnorm(n*pz),n,pz)
    eta.ge.test = xtest[,1] + xtest[,2] + ztest[,1] + ztest[,2]
    eta.ge.test = eta.ge.test + 3*xtest[,1]*ztest[,1] + 3*xtest[,2]*ztest[,2]
    fit.ge.strong = HierFabs(x, y.ge, z, model="cox", status=status)
    y.pred.gg.s = predict(fit.ge.strong, xtest, ztest)
    mean((y.pred.gg.s-eta.ge.test)^2)
    
    ## Weak hierarchy
    fit.ge.weak = HierFabs(x, y.ge, z, model="cox", status=status, hier="weak")
    y.pred.ge.w = predict(fit.ge.weak, xtest, ztest)
    mean((y.pred.ge.w-eta.ge.test)^2)
    
# References

A General Framework for Identifying Hierachical Interaction of Genomics Data. Manuscript.

# Development
The R-package is developed by Xiao Zhang (zhangxiao_0422@163.com).