# #54 - Long paths result in nonportable tar files [Open]

> Username: justinmcgrath  
> Created at: 2022-04-18 21:42:26 UTC  
> Updated at: 2023-12-15 12:01:09 UTC  
> Url: https://github.com/boostorg/odeint/issues/54  

This isn't really a bug, but the CRAN guidelines for R packages do not like paths with long file names since technically they are not supported by the tar format, which has a 100 byte limit. Most (or all?) modern tar implementations don't have a problem with this, so the complaint is annoying, but I was wondering if there is a sensible way to shorten some of the paths in Boost.  
  
The two that cause a problem for me in particular are these:   
"boost/numeric/odeint/stepper/generation/generation_controlled_adams_bashforth_moulton.hpp"  
"boost/numeric/odeint/stepper/generation/generation_runge_kutta_cash_karp54_classic.hpp"  
  
The "generation" portion is repetitive, so perhaps that could be removed from the file names?  
  
Sorry for the irritating complaint.  
  
EDIT: For clarification, the paths in boost are not longer than 100 bytes, but since they're part of an R package, the full path name is longer.

---

## Comment 1

> Username: justinmcgrath  
> Created at: 2023-12-11 20:17:20 UTC  
> Url: https://github.com/boostorg/odeint/issues/54#issuecomment-1850821381  

We are not the only ones with this issue too. There's another R package that uses Boost, and the names need to be shortened for that as well: https://github.com/eddelbuettel/bh.

---

## Comment 2

> Username: mborland  
> Created at: 2023-12-15 11:12:00 UTC  
> Url: https://github.com/boostorg/odeint/issues/54#issuecomment-1857702794  

Once I get the CI system setup I can fix this for you. Are the paths short enough with just the redundant "generation" removed?

---

## Comment 3

> Username: justinmcgrath  
> Created at: 2023-12-15 12:01:08 UTC  
> Url: https://github.com/boostorg/odeint/issues/54#issuecomment-1857774364  

As of yesterday, there's a chance CRAN may increase this limit, so this may not be needed after all. I'll post here when I know their decision.
