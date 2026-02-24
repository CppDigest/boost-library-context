# #122 - Incorrect std::equal usage triggers UB, detected by address sanitizer [Open]

> Username: erenon  
> Created at: 2020-02-21 15:01:08 UTC  
> Updated at: 2022-09-22 09:52:58 UTC  
> Url: https://github.com/boostorg/process/issues/122  

In environment.hpp, there are several std::equal calls like this:  
  
    std::equal(st1.begin(), st1.end(), *p)  
  
`st1` is a string, with user specified content. `p` is a pointer to a character (I surmise, as `auto` is used all over the place), and holds an existing key of `*this` in each iteration.  
This usage of equal is incorrect, as it yields to reading invalid data if `size(st1) > strlen(p)`.  
  
Address sanitizer (ASAN) catches the issue. To reproduce:  
  
```  
bp::environment e = boost::this_process:environment();  
e["foobarbazquxmux"] = "xyz";  
```  
  
The issue can be worked around by using the `set` member of the implementation directly:  
  
```  
/*bp::environment*/ auto e = boost::this_process:environment();  
e.set("foobarbazquxmux", "xyz");  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2020-05-20 13:29:53 UTC  
> Url: https://github.com/boostorg/process/issues/122#issuecomment-631474532  

I don't think that can happen, because the last valid character in `p` is a `NULL`. Thus the `equal` stops iterating that. ASAN wouldn"t know that, hence gives a false positive.

---

## Comment 2

> Username: jonesmz  
> Created at: 2020-05-20 17:24:06 UTC  
> Url: https://github.com/boostorg/process/issues/122#issuecomment-631614498  

ASAN does not analyse code based on static analysis. It modifies the compiled code with additional instructions that catch behavior at runtime. In this case, it would be allocating the memory range for this object with guard ranges on either side, and setting a trap instruction for programs attempting to read from either guard range.  
  
If ASAN is reporting that something is happening, then the thing it is reporting is happening. There is very very little room for false positives here.  
  
This issue is causing me pain.  
  
Even if the ASAN output is a false positive, it is still triggering on every ASAN run in my test environment. This produces a lot of undesired log output.  
  
How can we improve the boost process code to ensure this does not trigger ASAN output anymore?

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2020-05-20 17:51:44 UTC  
> Url: https://github.com/boostorg/process/issues/122#issuecomment-631628752  

Show me the error in the code. I don't know how ASAN works, but if it screws with null-terminators, I can't be expected to work around this.

---

## Comment 4

> Username: jonesmz  
> Created at: 2020-05-20 18:03:44 UTC  
> Url: https://github.com/boostorg/process/issues/122#issuecomment-631634949  

ASAN does not screw with null terminators.  
  
The bug is that a read is happening after the buffer. Which means after the null terminator.  
  
std::equal does not check for null-terminators.  
  
So if the str1 variable is larger than the string represented by p you're going to get an invalid read.  
  
If you don't like my fix here: https://github.com/boostorg/process/pull/141, that's fine. But it's still a bug.

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2020-05-20 18:36:10 UTC  
> Url: https://github.com/boostorg/process/issues/122#issuecomment-631651010  

How unless you put a null terminator in the middle of str1?  
  
```  
str1 = "foobar"  
*p = "foo"  
  
(*p[3]) == '\0' //valid memory access   
```  
  
Since there's a `'\0'` at position 4, `equal` will compare those, get a mismatch and stop iterating. How can you read after the buffer then?

---

## Comment 6

> Username: jonesmz  
> Created at: 2020-05-20 18:42:20 UTC  
> Url: https://github.com/boostorg/process/issues/122#issuecomment-631654596  

Are you able to reproduce the issue with the -fsanitize=address option added to your compiler? Works with GCC and Clang for most versions since e.g. 2017.

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2020-05-20 18:57:40 UTC  
> Url: https://github.com/boostorg/process/issues/122#issuecomment-631662512  

Why? What am I missing? Is it a false positive or not?

---

## Comment 8

> Username: jonesmz  
> Created at: 2020-05-20 19:00:05 UTC  
> Url: https://github.com/boostorg/process/issues/122#issuecomment-631663906  

I don't believe that it is a false positive, no.  
  
In my experience, ASAN has never given me a false positive. Further, the technical implementation of ASAN makes it very unlikely that a false positive could be happening.  
  
It's possible that there is a difference here between how you are expecting std::equal to have been implemented, and how the assembly is being generated. This might cause a load from outside of the buffer.

---

## Comment 9

> Username: jonesmz  
> Created at: 2020-05-20 19:00:54 UTC  
> Url: https://github.com/boostorg/process/issues/122#issuecomment-631664370  

@erenon   
  
Do you have a minimal reproduction? The code for my usage is very large and it may be difficult to provide a minimal reproduction.

---

## Comment 10

> Username: klemens-morgenstern  
> Created at: 2020-05-20 19:02:15 UTC  
> Url: https://github.com/boostorg/process/issues/122#issuecomment-631665163  

Then please explain to me how this can happen without you having a null terminator in the middle of `str1`.

---

## Comment 11

> Username: jonesmz  
> Created at: 2020-05-20 19:05:49 UTC  
> Url: https://github.com/boostorg/process/issues/122#issuecomment-631667077  

What is the actual type of p here?  
  
`auto p = this->_env_impl;`  
  
Is it char*?  
or char**?

---

## Comment 12

> Username: klemens-morgenstern  
> Created at: 2020-05-21 05:13:53 UTC  
> Url: https://github.com/boostorg/process/issues/122#issuecomment-631885409  

`char*` on windows, `char**` on posix.

---

## Comment 13

> Username: jonesmz  
> Created at: 2020-05-21 07:33:57 UTC  
> Url: https://github.com/boostorg/process/issues/122#issuecomment-631934203  

How can that possible work correctly on windows then?  
```  
char* p = this->_env_impl;  
auto st1 = key + ::boost::process::detail::equal_sign<Char>();  
while (*p != nullptr) // decltype(*p) == char  
{  
    if(std::equal(st1.begin(), st1.end(), *p)) // std::equal(std::string::iterator, std::string::iterator, char)  
        if(st1 == *p) // std::string == char  
            break;  
    p++;  
}  
```

---

## Comment 14

> Username: erenon  
> Created at: 2020-05-21 07:54:33 UTC  
> Url: https://github.com/boostorg/process/issues/122#issuecomment-631942959  

I have a very simple repro, and explanation:  
  
```  
int main()  
{  
  const std::string st1 = "foobarbaz";  
  const char* p = "qux";  
  
  const bool r = std::equal(st1.begin(), st1.end(), p);  
  
  return r;  
}  
```  
  
Compile and run with ASAN, it will detect global-buffer-overflow, READ of size 9. Here's why:  
  
The C++ standard (draft) says about equal (edited for brevity):  
  
> last2 be first2 + (last1 - first1) for the overloads with no parameter last2 or r2;  
> E *i == *(first2 + (i - first1)) ;  
> Returns: If last1 - first1 != last2 - first2, return false.  
Otherwise return true if E holds for every iterator i in the range [first1, last1) Otherwise, returns false.  
  
See more at: https://eel.is/c++draft/alg.equal  
  
I see nothing here (nor in the complexity requirements) that prevents the compiler to assume that the two ranges for the 3 arg overload are of the same size. Here's what GCC does:  
  
```  
      template<typename _Tp>  
        static bool  
        equal(const _Tp* __first1, const _Tp* __last1, const _Tp* __first2)  
        {  
          if (const size_t __len = (__last1 - __first1))  
            return !__builtin_memcmp(__first1, __first2, sizeof(_Tp) * __len);  
          return true;  
        }  
```  
https://code.woboq.org/gcc/include/c++/7.2.1/bits/stl_algobase.h.html#std::__equal  
  
It simply assumes the range length of the second range, and passes it to `__builtin_memcmp`. It will obviously turn to CPU instruction extensions, and batch the comparisons, thus reading invalid memory.  
  
My humble suggestions:  
 - Assuming `p` is `const char**`  
 - use `st1 == *p` instead of equal. If that is too C++, use strncmp  
 - use less `auto`, if possible  
  
Thanks!

---

## Comment 15

> Username: klemens-morgenstern  
> Created at: 2020-05-21 08:33:24 UTC  
> Url: https://github.com/boostorg/process/issues/122#issuecomment-631960710  

Oh ok, I didn"t know that equal assumes the length like this. Sorry about missing that.  
  
The `std::equal` is however a partial string match, a `strats_with`, not a full match. So p might be `FOO=BAR` and st1 is `FOO=` and it should yield true.

---

## Comment 16

> Username: erenon  
> Created at: 2020-05-21 08:47:59 UTC  
> Url: https://github.com/boostorg/process/issues/122#issuecomment-631966942  

in this case, wrapping `p` in a `string_view`, that has `starts_with` seems a cheap win.

---

## Comment 17

> Username: dchansen  
> Created at: 2021-06-25 10:27:16 UTC  
> Url: https://github.com/boostorg/process/issues/122#issuecomment-868401342  

If this fix could be implemented, it would be great. Not being able to use ASAN with boost process is quite problematic, and if you're unlucky with the memory allocator, this bug will result in a segmentation fault.

---

## Comment 18

> Username: jonesmz  
> Created at: 2021-06-25 16:05:09 UTC  
> Url: https://github.com/boostorg/process/issues/122#issuecomment-868669788  

Maybe if the project maintainers didn't close PRs without comment or explanation, we would have had this fixed over a year ago.

---

## Comment 19

> Username: pac-work  
> Created at: 2022-09-22 09:49:53 UTC  
> Url: https://github.com/boostorg/process/issues/122#issuecomment-1254789959  

@klemens-morgenstern Hello, I would like to ask if there are any plans to fix this UB? As you can see above (https://github.com/boostorg/process/issues/180), more people already reported this problem and I believe there are even more just like me silently waiting for the fix. Can we help somehow? Somebody above already stated you rejected theirs PR without explanation. So what is the current plan with regards to fixing this?

---

## Comment 20

> Username: klemens-morgenstern  
> Created at: 2022-09-22 09:52:57 UTC  
> Url: https://github.com/boostorg/process/issues/122#issuecomment-1254793398  

This is fixed in boost 1.80, the code now look like this;  
  
```cpp  
            if ((std::distance(st1.begin(), st1.end()) < len)  
                 && std::equal(st1.begin(), st1.end(), *p))  
```
