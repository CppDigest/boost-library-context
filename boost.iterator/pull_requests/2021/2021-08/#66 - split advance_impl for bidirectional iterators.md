# #66 split advance_impl for bidirectional iterators [Closed]

> Username: joaquintides  
> Created at: 2021-08-31 15:08:08 UTC  
> Updated at: 2021-08-31 17:20:30 UTC  
> Closed at: 2021-08-31 17:20:30 UTC  
> Url: https://github.com/boostorg/iterator/pull/66  

Split according to signedness/unsignedness of Distance, as the checks "if (n >= 0)"  
and "while (n < 0)" produce type-limits warnings in GCC 4.6-7 when n is unsigned.

---

## Comment 1

> Username: Lastique  
> Created_at: 2021-08-31 15:57:17 UTC  
> Url: https://github.com/boostorg/iterator/pull/66#issuecomment-909366554  

I believe, the difference type [must be signed](http://eel.is/c++draft/iterators#iterator.requirements.general-1) for any conforming iterator, so this change is superfluous.

---

## Comment 2

> Username: joaquintides  
> Created_at: 2021-08-31 16:20:29 UTC  
> Updated_at: 2021-08-31 16:30:44 UTC  
> Url: https://github.com/boostorg/iterator/pull/66#issuecomment-909385965  

[`boost::advance`](https://github.com/boostorg/iterator/blob/develop/doc/advance.rst) does not require that its `Distance` argument be of the same type as the iterator difference type. For the record, neither does [`std::advance`](http://eel.is/c++draft/iterator.operations). The [libc++ implementation of `std::advance`](https://github.com/llvm/llvm-project/blob/6c0181c00f4ea0bcb7e4d05055412083b8270cc8/libcxx/include/__iterator/advance.h#L55-L66) also allows unsigned arguments.  
  
A common usage scenario where unsignedness is incurred is the following:  
```  
auto it=c.begin();  
boost::advance(it,c.size()/2); // move to the mid point of the container  
```  
(This is how I actually hit the problem.)

---

## Comment 3

> Username: joaquintides  
> Created_at: 2021-08-31 16:40:37 UTC  
> Url: https://github.com/boostorg/iterator/pull/66#issuecomment-909401862  

To add more context, the extension of `boost::advance` to allow for arguments other than the difference type was [made by you](https://github.com/boostorg/iterator/commit/5bfbfb771646187059438a404318d213525ccfbf#diff-b8ba8ed996376e51218c24245a5f0d4cc00f84eceb91c10157274a20701771a1) on July 2017, with the following rationale:  
  
_"Use a separate template parameter for distance in advance().  
This follows std::advance interface and also allows to use distance types other than iterator's difference_type (if the iterator supports that)."_

---

## Comment 4

> Username: Lastique  
> Created_at: 2021-08-31 16:43:25 UTC  
> Url: https://github.com/boostorg/iterator/pull/66#issuecomment-909404060  

"Other" doesn't necessarily mean "unsigned", but ok, fair enough. In that case, I'd rather disable the warning with a pragma.

---

## Comment 5

> Username: joaquintides  
> Created_at: 2021-08-31 16:46:21 UTC  
> Url: https://github.com/boostorg/iterator/pull/66#issuecomment-909406427  

This is also a possibility. Do you want to me to change the PR so as to use pragmas instead?

---

## Comment 6

> Username: Lastique  
> Created_at: 2021-08-31 16:51:11 UTC  
> Url: https://github.com/boostorg/iterator/pull/66#issuecomment-909410077  

Yes, please. Note that `#pragma push`/`pop` only appeared in gcc 4.6, so only enable the workaround in 4.6 and up.

---
