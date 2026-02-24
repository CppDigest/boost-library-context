# #170 - `splice`, `sort` etc for `stable_vector` [Closed]

> Username: dmsteck  
> Created at: 2020-11-06 15:11:26 UTC  
> Updated at: 2021-04-19 20:10:41 UTC  
> Closed at: 2021-04-19 20:10:41 UTC  
> Url: https://github.com/boostorg/container/issues/170  

I wonder if `stable_vector` could benefit from `list`-like operations such as splicing, sorting, reversing etc. while preserving pointers and iterators. These would generally work by transforming the pointer vector and updating the "up" pointers.  
  
It might also be interesting to add a facility for swapping two nodes while preserving their respective pointers and iterators. (For `std::list`, this is usually done by splicing, but for `stable_vector` that could be prohibitively expensive as it would mean allocating a temporary.)  
  
Let me know if this sounds reasonable. Happy to cook up a PR.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-12-29 15:26:20 UTC  
> Url: https://github.com/boostorg/container/issues/170#issuecomment-752121058  

Looks interesting. Happy to review a PR ;-)

---

## Comment 2

> Username: igaztanaga  
> Created at: 2021-04-19 20:10:41 UTC  
> Url: https://github.com/boostorg/container/issues/170#issuecomment-822752496  

Closing bugs without recent activity. Please submit a PR in case you are still interested in the feature.
