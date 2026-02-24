# #60 - Licensing question for math.hpp [Closed]

> Username: mablanchard  
> Created at: 2021-03-25 13:18:00 UTC  
> Updated at: 2021-04-18 12:11:00 UTC  
> Closed at: 2021-04-18 12:11:00 UTC  
> Url: https://github.com/boostorg/intrusive/issues/60  

In [`math.hpp`](https://github.com/boostorg/intrusive/blob/develop/include/boost/intrusive/detail/math.hpp#L158), the `floor_log2` functions implementation seems to be [copied from a Stack Overflow thread](https://stackoverflow.com/questions/11376288/fast-computing-of-log2-for-64-bit-integers) (the SO thread is linked in the Boost source file). If I understand the [Stack Overflow licensing notice](https://stackoverflow.com/help/licensing) correctly, that code is licensed under [CC BY-SA 3.0](https://creativecommons.org/licenses/by-sa/3.0/) (snippet initially posted on Jul 9 2012). I'm not a jurist (at all!) but couldn't that be incompatible with Boost.Intrusive being licensed under Boost Software License? Not sure how it can be mitigated if it is though...

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-03-26 09:04:07 UTC  
> Url: https://github.com/boostorg/intrusive/issues/60#issuecomment-808054403  

I think this was discussed in the mailing list and no clear conclusion was reached but it seems that the safest bet is to replace that code. I'll maintain this issue open to make sure this is not forgotten.
