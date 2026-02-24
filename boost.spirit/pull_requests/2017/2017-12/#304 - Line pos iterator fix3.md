# #304 Line pos iterator fix3 [Merged]

> Username: octopus-prime  
> Created at: 2017-12-01 12:09:57 UTC  
> Updated at: 2017-12-07 10:41:15 UTC  
> Merged at: 2017-12-04 13:01:51 UTC  
> Closed at: 2017-12-04 13:01:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/304  

This is the fix for #305 "bug in line_pos_iterator".  
Based on #40 "Fixing line_pos_iterator column numbering bug and get_current_line bug." from @tomoki

---

## Comment 1

> Username: djowel  
> Created_at: 2017-12-02 04:42:30 UTC  
> Url: https://github.com/boostorg/spirit/pull/304#issuecomment-348667953  

looks good to me. is there a similar one in x3?

---

## Comment 2

> Username: octopus-prime  
> Created_at: 2017-12-02 11:37:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/304#issuecomment-348686281  

No, there is no similar one in x3.  
And in my opinion there is no need for a similar one in x3.  
`line_pos_iterator` is just an iterator adapter.  
No dependencies to x3, qi, etc. But it can be used from all there, like `istream_iterator` too.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2017-12-02 16:15:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/304#issuecomment-348702146  

> is there a similar one in x3?  
  
Yes, it is.  
https://github.com/boostorg/spirit/blob/79995f7e8fb00809745e99fd979d2cacc5a6a980/include/boost/spirit/home/x3/support/utility/error_reporting.hpp#L97-L190  
https://github.com/boostorg/spirit/blob/79995f7e8fb00809745e99fd979d2cacc5a6a980/include/boost/spirit/home/x3/support/utility/testing.hpp#L242-L247

---

## Comment 4

> Username: djowel  
> Created_at: 2017-12-02 21:58:06 UTC  
> Url: https://github.com/boostorg/spirit/pull/304#issuecomment-348723128  

when we get to clean up the code base. it would be good to unify the common parts of x3 and qi. fixing two similar but not 100% identical code is rather intractable.

---
