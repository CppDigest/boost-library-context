# #92 - [doc] Add how to combine adaptors to the quickstart documentation [Open]

> Username: gnzlbg  
> Created at: 2016-02-09 15:22:58 UTC  
> Updated at: 2016-03-06 23:43:39 UTC  
> Url: https://github.com/boostorg/hof/issues/92  

In Quickstart/Adaptors it is shown how to use infix and pipable. It is not shown that two adaptors can be combined, e.g. `auto sum = pipable_adaptor<infix_adaptor<sum_f>>();`It is just a quickstart but that is meaningful information at the cost of 1-2 extra lines.
