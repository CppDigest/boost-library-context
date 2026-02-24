# #262 - Partially Applied Metafunctions [Closed]

> Username: ricejasonf  
> Created at: 2016-03-08 21:42:40 UTC  
> Updated at: 2016-06-14 07:28:20 UTC  
> Closed at: 2016-06-14 07:28:19 UTC  
> Url: https://github.com/boostorg/hana/issues/262  

Currently, I can partially apply a `hana::Metafunction` with `hana::partial` but with a couple of caveats.   
  
Eventhough the partially applied `hana::Metafunction` still takes `type`s as arguments and returns a `type`, it is no longer technically a `hana::Metafunction` itself (I don't think). The other issue is that it uses `hana::basic_tuple` under the hood which is kind of overkill when we know we are working strictly with types. A solution could probably use `hana::experimental::types`.  
  
Just an idea for an approach to naming, what if there was an internal project name **Mana** that was basically Hana for strictly compile-time values. Then the name could be `mana::partial`. :stuck_out_tongue:

---

## Comment 1

> Username: ldionne  
> Created at: 2016-06-14 04:24:31 UTC  
> Url: https://github.com/boostorg/hana/issues/262#issuecomment-225776636  

I do agree with you that (1) a partially applied Metafunction should probably be a Metafunction too, and (2) using a `basic_tuple` under the hood is overkill, since `experimental::types` would be sufficient and more efficient. However, I am concerned about adding complexity to the Functional part of Hana, which I want to see gone when Fit makes it into Boost.

---

## Comment 2

> Username: ricejasonf  
> Created at: 2016-06-14 07:28:19 UTC  
> Url: https://github.com/boostorg/hana/issues/262#issuecomment-225800968  

cool
