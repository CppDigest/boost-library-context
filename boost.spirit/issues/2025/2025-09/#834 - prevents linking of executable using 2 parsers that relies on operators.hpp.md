# #834 - prevents linking of executable using 2 parsers that relies on operators.hpp [Closed]

> Username: rcoca  
> Created at: 2025-09-26 15:30:47 UTC  
> Updated at: 2025-09-26 19:19:09 UTC  
> Closed at: 2025-09-26 15:59:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/834  

https://github.com/boostorg/spirit/blob/82184fecd6e6979e44a539bb5ff0918724948c40/include/boost/spirit/home/support/utree/operators.hpp#L543

---

## Comment 1

> Username: saki7  
> Created at: 2025-09-26 15:59:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/834#issuecomment-3339348532  

Please provide minimal information of the expected behavior, the actual behavior, and the steps to reproduce.  
  
I'm not accepting this kind of apathetic attitude, because it is the second time you're opening this kind of issue (ref: #806). This is not the desired way of contributing to the open source project. Thanks for your understanding.

---

## Comment 2

> Username: rcoca  
> Created at: 2025-09-26 18:51:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/834#issuecomment-3340043522  

I'd say the whole description is in the title. Providing explicit template specialization in header would propagate to libraries.  
Possibly different ones.   
Which, when used to create an executable - would result in duplicate function definition.  
  
I'm unsure when this became personal. But it isn't.  
And I do understand, I'll figure it out myself, working around official boost build.

---

## Comment 3

> Username: rcoca  
> Created at: 2025-09-26 19:15:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/834#issuecomment-3340144525  

My bad, I was running an older version of boost. Appears fixed in this version - I just misread.  
Fixing commit says: [Missing inline keyword. Fixes #638]
