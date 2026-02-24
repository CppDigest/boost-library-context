# #291 - Optional is set even though optional_parser fails [Closed]

> Username: andreasbuhr  
> Created at: 2025-11-14 13:41:32 UTC  
> Updated at: 2025-11-16 06:44:06 UTC  
> Closed at: 2025-11-16 06:39:36 UTC  
> Url: https://github.com/boostorg/parser/issues/291  

Hi,  
  
unfortunately, the repeat_parser suffers from the same issue as the seq_parser, as was reported in https://github.com/boostorg/parser/issues/279 and https://github.com/boostorg/parser/issues/285.  
  
In https://github.com/boostorg/parser/blob/647cec66831407742a6ad78582f2a9f3cd7d44d3/include/boost/parser/parser.hpp#L3200 , repeat_parser writes to an std::optional<> it is given, even if its own attribute type is nope.  
  
So the seq_parser gives an std::optional to some of its subparsers, even though this subparser has attribute type nope.  
Then one of the subparsers is a repeat parser, which writes something to that optional, because it was successful.  
  
Please fix repeat_parser to not touch the given attribute when its own attribute type is nope.  
Please fix seq_parser to not give any attribute to subparsers with attribute type nope.  
  
If I'll find some time, I'll create pull requests with reproducers.  
If I'll find some more time, I'll create pull requests with fixes.  
  
best,  
Andreas

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-11-15 22:27:36 UTC  
> Url: https://github.com/boostorg/parser/issues/291#issuecomment-3536984335  

The Boost release is in full swing right now.  If this doesn't get fixed very soon, it will not get fixed for months (until the next release).  I have been trying for a while now to reproduce this, but so far I cannot.  @andreasbuhr, if you have time, please post a small repro.

---

## Comment 2

> Username: andreasbuhr  
> Created at: 2025-11-16 06:20:16 UTC  
> Url: https://github.com/boostorg/parser/issues/291#issuecomment-3538039186  

give me three more hours ;-)

---

## Comment 3

> Username: andreasbuhr  
> Created at: 2025-11-16 06:26:37 UTC  
> Url: https://github.com/boostorg/parser/issues/291#issuecomment-3538057476  

https://github.com/boostorg/parser/pull/293 should prevent this from happening in any form.  
I implemented my request "Please fix seq_parser to not give any attribute to subparsers with attribute type nope." there.

---

## Comment 4

> Username: andreasbuhr  
> Created at: 2025-11-16 06:39:36 UTC  
> Url: https://github.com/boostorg/parser/issues/291#issuecomment-3538092844  

Sorry, my bad, this is fixed in develop. My repo was not up to date.

---

## Comment 5

> Username: tzlaine  
> Created at: 2025-11-16 06:44:06 UTC  
> Url: https://github.com/boostorg/parser/issues/291#issuecomment-3538105382  

That's a relief!  Thanks for getting back to me so quickly.
