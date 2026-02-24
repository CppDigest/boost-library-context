# #12 - Please remove dependency on Boost Python. [Closed]

> Username: grafikrobot  
> Created at: 2018-04-21 02:19:53 UTC  
> Updated at: 2018-10-15 04:03:24 UTC  
> Closed at: 2018-10-15 04:03:24 UTC  
> Url: https://github.com/boostorg/parameter/issues/12  

It seems ludicrous to me that Parameter depends on Python. In trying to deal with a modular Boost it causes the rather heavyweight BPL to be pulled in when using Parameters. For example it causes Boost Log to depend on BPL for no reason.  
  
Would it be possible to move the Python Parameter binding stuff to the BPL library instead?

---

## Comment 1

> Username: eldiener  
> Created at: 2018-04-21 04:02:05 UTC  
> Url: https://github.com/boostorg/parameter/issues/12#issuecomment-383265814  

Unless python.hpp is specifically being included by the user parameter itself does not depend on python AFAICS. Granted there is a Python test of the Python binding among the parameter tests. Still I can understand your wanting the Python binding to be in the Boost python library, but this will of course make the Boost python library "depend' on parameter.

---

## Comment 2

> Username: grafikrobot  
> Created at: 2018-04-21 04:30:51 UTC  
> Url: https://github.com/boostorg/parameter/issues/12#issuecomment-383266995  

True, if you don't `#include` anything you don't depend on anything. But that's a useless detail to a user who, for example, is using `bcp` to extract what they need when they want to use any of these libraries: accumulators, convert, flyweight, graph, heap, lockfree, log, msm, or signals2. All of which depend on parameter, yet none of them use python.  
  
And given that removing the python dependency would move parameter, and many of it's dependents, down many dependency levels.. I think having python depend on parameter is a winning alternative than the other way around.

---

## Comment 3

> Username: eldiener  
> Created at: 2018-04-21 10:01:22 UTC  
> Url: https://github.com/boostorg/parameter/issues/12#issuecomment-383282554  

I am all for moving the Python binding to the Python library if you want to do that. You might need PRs for both the parameter and python libraries for that. If you look at the python library it is not as if the python maintainer has been very keen to deal with PRs and Issues. I have no problem removing the Python support from parameter although I am hardly the official maintainer. But I am not going to be caught trying to get the python maintainer to do his job.

---

## Comment 4

> Username: solvingj  
> Created at: 2018-08-16 07:36:01 UTC  
> Url: https://github.com/boostorg/parameter/issues/12#issuecomment-413454062  

is there any consideration for either of the following alternative approaches:   
1.  extracting the python binding out to a separate module which depends on both parameter and python  
2.  having parameter be conditionally dependent in terms of compilation/include/linking like what is done with iostreams and bzip2/etc.

---

## Comment 5

> Username: grafikrobot  
> Created at: 2018-08-16 14:36:45 UTC  
> Url: https://github.com/boostorg/parameter/issues/12#issuecomment-413568025  

Option (1) would certainly be possible. Option (2) doesn't really solve it for packaging and `bcp` as that doesn't look at conditional usage. So given that PRs against Python are not likely to get attention.. I guess option (1) is the best one.

---

## Comment 6

> Username: solvingj  
> Created at: 2018-10-03 16:52:52 UTC  
> Url: https://github.com/boostorg/parameter/issues/12#issuecomment-426712782  

@eldiener would you be willing to consider working such a thing?

---

## Comment 7

> Username: pdimov  
> Created at: 2018-10-13 02:32:28 UTC  
> Url: https://github.com/boostorg/parameter/issues/12#issuecomment-429504348  

Not the worst idea in the world if you ask me. Let me know if you want a new repo created. :-)

---

## Comment 8

> Username: pdimov  
> Created at: 2018-10-14 15:35:13 UTC  
> Url: https://github.com/boostorg/parameter/issues/12#issuecomment-429636205  

https://github.com/boostorg/parameter_python  
  
Currently awaiting the Travis verdict before adding it as a submodule.

---

## Comment 9

> Username: pdimov  
> Created at: 2018-10-15 03:46:32 UTC  
> Url: https://github.com/boostorg/parameter/issues/12#issuecomment-429699955  

Done. You are welcome.
