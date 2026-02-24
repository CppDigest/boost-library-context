# #62 - Rendering of TeX equations in documentation [Open]

> Username: dhardy  
> Created at: 2019-06-21 08:51:43 UTC  
> Updated at: 2019-12-17 19:28:42 UTC  
> Url: https://github.com/boostorg/random/issues/62  

Some boost documentation uses TeX formulae, e.g. for the PDFs of distributions. Unfortunately these equations are rendered at a fixed low resolution, making the terms in some of them quite difficult to read (especially where the equations appear inline and contain things like `\e ^ \frac ...`). Examples (under "Description"):  
  
https://www.boost.org/doc/libs/1_69_0/doc/html/boost/random/lognormal_distribution.html  
https://www.boost.org/doc/libs/1_69_0/doc/html/boost/random/poisson_distribution.html  
  
Please at least increase the rendering size. Other options might be to move these equations out-of-line (thus increasing size) or using mathjax.
