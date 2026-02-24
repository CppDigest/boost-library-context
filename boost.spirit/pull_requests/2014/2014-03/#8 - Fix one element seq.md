# #8 Fix one element seq [Merged]

> Username: redbaron  
> Created at: 2014-03-25 00:01:46 UTC  
> Updated at: 2014-06-14 07:51:19 UTC  
> Merged at: 2014-04-01 02:25:52 UTC  
> Closed at: 2014-04-01 02:25:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/8  

If in a sequence only one element produces attribute, then make whole  
sequence to produce that attribute,rather than wrapping it into  
fusion::deque. Not doing so breaks alternative<> parser, when such  
sequences are on left and right side of it, because it then generates  
variant attribute over fusion:deque<> attributes, which is not  
what it is supposed to do. Plus it breaks traits::move_to when both  
source and destination are single element fusions sequences. Hence this  
fix to put everything back to normal for this edge case.

---
