# #102 - flat_map::insert ambiguous with initializer list & pairs that need to convert [Closed]

> Username: Sairony  
> Created at: 2019-02-14 09:13:25 UTC  
> Updated at: 2021-01-01 22:51:30 UTC  
> Closed at: 2021-01-01 22:50:57 UTC  
> Url: https://github.com/boostorg/container/issues/102  

This still seems broken, there's an old trac issue [here](https://svn.boost.org/trac10/ticket/10071) about it. Are the trickery pokery with movable_value_type even needed on modern compilers? If not it would be nice if these overloads could be conditionally removed in these cases.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-01-01 22:51:30 UTC  
> Url: https://github.com/boostorg/container/issues/102#issuecomment-753400228  

Many thanks for the report and your huge patience.
