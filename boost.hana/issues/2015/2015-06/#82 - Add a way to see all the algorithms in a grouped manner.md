# #82 - Add a way to see all the algorithms in a grouped manner. [Open]

> Username: ldionne  
> Created at: 2015-06-05 00:36:38 UTC  
> Updated at: 2017-06-24 16:55:56 UTC  
> Url: https://github.com/boostorg/hana/issues/82  

In the documentation, we should have a group for algorithms (or something like that) to allow one to just see a list of the algorithms, without having to search through all the concepts.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-08 22:44:44 UTC  
> Url: https://github.com/boostorg/hana/issues/82#issuecomment-110164868  

Looks like this can't be done with Doxygen. Variables and functions can't appear in multiple groups, which would be needed to document the algorithms from both a "Concepts" group and an "Algorithms" group. At least, there's the Cheat sheet and the alphabetical index, which can provide some help. It won't make it in time for the review, but I'm leaving this open because I think it is an important improvement.

---

## Comment 2

> Username: ldionne  
> Created at: 2017-06-09 04:19:47 UTC  
> Url: https://github.com/boostorg/hana/issues/82#issuecomment-307291398  

@m-j-w In #233 you said  
  
> Oh, and I'd suggest adding a group 'Algorithms' to `hana.hpp`  
>  
> ```c++  
> //! @defgroup group-algorithms Algorithms  
> //! Algorithms provided by the library for the various data types and structures.  
> ```  
>  
> and then adding a  
>  
> ```c++  
> //! @ingroup group-algorithms  
> ```  
>  
> to all the algorithms like -- well like all of them.  
>  
> This should then add another summary to the 'Reference documentation' section giving another helpful overview.  
>  
> Perhaps some subdivision whether they work on sequences or scalars might also be useful.  
  
For some reason I completely jumped over that comment two years ago, but I would really like to implement that. I've been trying out what you suggest, but Doxygen says:  
  
```  
[...]/include/boost/hana/fwd/accessors.hpp:36: warning: Member accessors found in multiple @ingroup groups! The member will be put in group group-Struct, and not in group group-algorithms  
```  
  
Would you happen to remember how you achieved that result? I know it's been more than a year, so no worries if you don't remember!

---

## Comment 3

> Username: m-j-w  
> Created at: 2017-06-24 12:20:41 UTC  
> Updated at: 2017-06-24 12:21:02 UTC  
> Url: https://github.com/boostorg/hana/issues/82#issuecomment-310835363  

Unfortunately, I don't have my local version from back then anymore, since I'm now just using the version distributed with boost. But I believe to remember that it 'just worked' after tweaking a bit the doxygen settings... Sorry not be of much help here.

---

## Comment 4

> Username: ldionne  
> Created at: 2017-06-24 16:55:56 UTC  
> Url: https://github.com/boostorg/hana/issues/82#issuecomment-310850489  

It's okay!
