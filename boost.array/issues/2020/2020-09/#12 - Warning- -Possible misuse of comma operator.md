# #12 - Warning: "Possible misuse of comma operator" [Open]

> Username: thomthom  
> Created at: 2020-09-16 14:54:36 UTC  
> Updated at: 2021-05-20 19:19:09 UTC  
> Url: https://github.com/boostorg/array/issues/12  

I'm seeing a warning being emitted by this library when I'm using Xcode 10.3:  
  
https://github.com/boostorg/array/blob/92f66a9f14ca1eef03b6028b01a1cab05e989991/include/boost/array.hpp#L185  
  
> Possible misuse of comma operator here  
  
![image](https://user-images.githubusercontent.com/192418/93354305-2410dd80-f83d-11ea-9c03-8f0a637bd472.png)  
  
Initially I thought of making a PR to address it, but I don't really understand the expression at that line. Why is `boost::throw_exception` followed by `, true` ?

---

## Comment 1

> Username: mclow  
> Created at: 2020-09-16 15:08:38 UTC  
> Url: https://github.com/boostorg/array/issues/12#issuecomment-693469886  

It's a false positive.  In C++11, there were severe limitations on what a constexpr function could do.  Effectively, they were limited to a single return statement.  
  
Breaking this down, it looks like this:  
```  
return i >= size ()  
   ? boost_throw_exception(...), true  
   : true;  
```  
  
The `, true` on the second line there is necessary, because otherwise the two halves of the ternary (`?:`) expression would have different types, and the compiler would emit an error for that. Try removing the `, true` and you will see what I mean.  
  
If you were writing this for C++14 and later, it would be clearer:  
```  
if (i >= size())  
    throw boost_throw_exception(...);  
return true;  
```  
  
but that won't compile for C++11

---

## Comment 2

> Username: thomthom  
> Created at: 2020-09-16 15:18:59 UTC  
> Url: https://github.com/boostorg/array/issues/12#issuecomment-693476670  

In a build with warnings as errors this would break the build. What would be a good way to mute these warnings? Use `#pragma`s to push/pop disable this specific warning before including the boost header?

---

## Comment 3

> Username: mclow  
> Created at: 2020-09-17 00:18:51 UTC  
> Url: https://github.com/boostorg/array/issues/12#issuecomment-693733536  

I don't know where that warning is coming from.  
Is it coming from the compiler? If so, what are the options passed to the compiler, and what's the full error message?

---

## Comment 4

> Username: mclow  
> Created at: 2020-09-17 00:23:36 UTC  
> Url: https://github.com/boostorg/array/issues/12#issuecomment-693734895  

This sample:  
```  
#include <boost/array.hpp>  
  
int main () {  
	boost::array<int, 5> arr;  
	return arr.size();  
	}  
```  
  
when compiled with Apple clang version 11.0.3 (clang-1103.0.32.62) as:  
`clang++ -Wall -Wextra -I $BOOST junk.cpp` gives no warnings, no errors

---

## Comment 5

> Username: thomthom  
> Created at: 2020-09-17 12:52:00 UTC  
> Url: https://github.com/boostorg/array/issues/12#issuecomment-694209928  

I can check when I'm back in office. But the warning I had to disable was `-Wcomma`.

---

## Comment 6

> Username: mclow  
> Created at: 2020-09-17 23:06:27 UTC  
> Url: https://github.com/boostorg/array/issues/12#issuecomment-694546420  

Sounds like a bug in `-Wcomma`.   (and I have verified that enabling that warning does throw a warning, with a completely incorrect "fix-it" suggestion)

---

## Comment 7

> Username: mclow  
> Created at: 2020-09-17 23:12:45 UTC  
> Url: https://github.com/boostorg/array/issues/12#issuecomment-694548221  

Submitted https://bugs.llvm.org/show_bug.cgi?id=47566 against clang.

---

## Comment 8

> Username: shreegilliorkar  
> Created at: 2021-05-20 19:19:08 UTC  
> Url: https://github.com/boostorg/array/issues/12#issuecomment-845409674  

Hey, i am Shree. New in open source contribution. My technical skills include C, C++, Python & Java. Can I please get some guidance for this project.
