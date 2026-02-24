# #621 - Images are missing in documentation [Open]

> Username: AndriyAstakhov  
> Created at: 2016-06-23 14:12:34 UTC  
> Updated at: 2018-02-25 12:04:15 UTC  
> Url: https://github.com/boostorg/compute/issues/621  

Images about preformance are missing in boost page documentation http://www.boost.org/doc/libs/1_61_0/libs/compute/doc/html/boost_compute/performance.html   
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2016-06-24 03:26:41 UTC  
> Url: https://github.com/boostorg/compute/issues/621#issuecomment-228249893  

Thanks for reporting this, I'll look into it. In the meantime, you can see the performance page with all the graphs here: http://boostorg.github.io/compute/boost_compute/performance.html

---

## Comment 2

> Username: keryell  
> Created at: 2016-08-09 13:18:28 UTC  
> Url: https://github.com/boostorg/compute/issues/621#issuecomment-238549579  

It may be related to another issue in the documentation configuration: it looks like the basic types (Core Library) are not "doxygened" on http://www.boost.org/doc/libs/1_61_0/libs/compute/doc/html/boost_compute/reference.html#boost_compute.reference.api_overview compared to http://boostorg.github.io/compute/boost_compute/reference.html#boost_compute.reference.api_overview

---

## Comment 3

> Username: Kojoley  
> Created at: 2018-02-25 12:04:15 UTC  
> Url: https://github.com/boostorg/compute/issues/621#issuecomment-368303842  

Images are only existing in `gh-pages` branch, they should be placed on master/develop so boost release can find them.
