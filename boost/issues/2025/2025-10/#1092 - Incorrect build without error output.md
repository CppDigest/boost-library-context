# #1092 - Incorrect build without error output [Open]

> Username: Sneper-Breeze  
> Created at: 2025-10-20 19:20:02 UTC  
> Updated at: 2025-10-31 10:42:39 UTC  
> Url: https://github.com/boostorg/boost/issues/1092  

During the final build, everything is assembled and running, except boost_graphviz. Because of this, when building a project in which I use this library, an error appears - can't find the implementation for the boost::detail::graph::read_graphviz_new function. All other parts of the library are working, I checked

---

## Comment 1

> Username: nigels-com  
> Created at: 2025-10-31 10:42:39 UTC  
> Url: https://github.com/boostorg/boost/issues/1092#issuecomment-3472451197  

We'd probably need a lot more information about this.  
What version of boost, what OS, what toolchain.  Log file.
