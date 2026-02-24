# #94 - Tricky line number tracking [Closed]

> Username: jefftrull  
> Created at: 2020-06-03 19:19:43 UTC  
> Updated at: 2020-08-16 18:54:29 UTC  
> Closed at: 2020-08-16 18:54:29 UTC  
> Url: https://github.com/boostorg/wave/issues/94  

In [a recent Twitter post](https://twitter.com/ericniebler/status/1252660334545866752?s=20), @ericniebler asks  
> what should be the token sequence emitted by the preprocessor for this program:  
```c++  
#define FOO(X) __LINE__ BAR  
#define BAR(X) __LINE__   
FOO(X)  
   (Y)  
```  
> GCC says: "3 3" but clang and msvc say "3 4". Who's right?  
  
Sadly, Wave reports "3 1".

---

## Comment 1

> Username: jefftrull  
> Created at: 2020-06-03 19:21:40 UTC  
> Url: https://github.com/boostorg/wave/issues/94#issuecomment-638409352  

Some good discussion on the GCC bug tracker [here](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=94535)

---

## Comment 2

> Username: hkaiser  
> Created at: 2020-06-08 00:14:19 UTC  
> Url: https://github.com/boostorg/wave/issues/94#issuecomment-640301378  

Some investigation shows that wave uses the position information of the `__LINE__` token where it was seen during parsing (see: https://github.com/boostorg/wave/blob/develop/include/boost/wave/util/cpp_macromap.hpp#L1613, `curr_token` represents the original `__LINE__`). This works well as long as `__LINE__` itself is not used as part of the replacement list for a macro.   
  
This has to be changed such that any `__LINE__` token resolves to the position where the macro containing the `__LINE__` is expanded from.

---

## Comment 3

> Username: hkaiser  
> Created at: 2020-06-08 00:18:55 UTC  
> Updated at: 2020-06-08 00:20:29 UTC  
> Url: https://github.com/boostorg/wave/issues/94#issuecomment-640302069  

Also, http://www.open-std.org/jtc1/sc22/wg14/www/docs/n1911.htm has a nicely convoluted test case:  
```cpp  
  #line 500  
  #define MAC(a,b) a,b,__LINE__  
  
  #line 1000  
 int j[] = { __LINE__, __\  
LINE\  
__,  
  
  #line 2000  
  #line __\  
LINE\  
__  
  __LINE__,  
  
  #line 3000  
  #line \  
\  
\  
__LINE__\  
\  
\  
/**/  
  __LINE__,  
  
  MAC(__LINE__,__LINE__),  
  
  MAC(  
    __LINE__,  
    __LINE__  
  ),  __LINE__,  
  
  M\  
A\  
C\  
\  
(\  
\  
__\  
LINE\  
__,\  
__LINE__\  
\  
),  
  __LINE__,  
  
  M\  
A\  
C\  
(  
__\  
LINE\  
__,  
__LINE__  
),  
  __LINE__,  
  999 };  
```  
  
The new wording specifying the expected behavior for multi-line macros containing `__LINE__` can be found here: http://www.open-std.org/jtc1/sc22/wg14/www/docs/n2322.htm

---

## Comment 4

> Username: jefftrull  
> Created at: 2020-06-15 20:40:06 UTC  
> Url: https://github.com/boostorg/wave/issues/94#issuecomment-644376983  

Is it possible we have the same issue with `__FILE__` and `__INCLUDE_LEVEL__`?

---

## Comment 5

> Username: jefftrull  
> Created at: 2020-06-15 20:47:42 UTC  
> Url: https://github.com/boostorg/wave/issues/94#issuecomment-644380899  

OK so I tried a simple testcase:  
  
```c++  
#define theline __LINE__  
  
// padding  
  
static int foo = theline ;  
```  
This actually produces 5, the correct answer. Is it possible the situation is more complex?  
Also (to answer my own question) we seem to do the right thing - at least for simple cases - with the other two macros.

---

## Comment 6

> Username: hkaiser  
> Created at: 2020-06-15 20:56:02 UTC  
> Url: https://github.com/boostorg/wave/issues/94#issuecomment-644384735  

> Is it possible we have the same issue with __FILE__ and __INCLUDE_LEVEL__?  
  
Hmmm, could be - if they are used in macros that are defined in a different file from where the macro is used...

---

## Comment 7

> Username: jefftrull  
> Created at: 2020-06-16 01:48:17 UTC  
> Url: https://github.com/boostorg/wave/issues/94#issuecomment-644481333  

Looking at this some more... I think it must be a tricky thing with rescanning. I find that it's actually _quite hard_ to make Wave do the wrong thing. Maybe this only happens because the first macro `FOO` evaluates to the name of a second, function-like macro. If `BAR` is not a function-like macro it does the right thing. For example:  
```c++  
#define FOO(X) __LINE__ BAR  
#define BAR __LINE__   
FOO(X)  
  (Y)  
```  
produces  
```  
3 3  
(Y)  
```

---

## Comment 8

> Username: jefftrull  
> Created at: 2020-06-27 23:25:57 UTC  
> Url: https://github.com/boostorg/wave/issues/94#issuecomment-650648269  

Just spent some quality time with gdb comparing the behavior of the passing (BAR is object-like) vs failing (BAR is function-like) cases. What seems to be happening is that the context's idea of the current line number, `main_pos.line`, is briefly set to 1 by [this line](https://github.com/boostorg/wave/blob/ee0a7c14c5b06e4ce490cd76d6c12fb082033135/include/boost/wave/util/cpp_iterator.hpp#L867) and restored to either 4 or 5 by [this line](https://github.com/boostorg/wave/blob/ee0a7c14c5b06e4ce490cd76d6c12fb082033135/include/boost/wave/util/cpp_iterator.hpp#L658). This is true in both cases.  
  
The big difference between the cases is that in the object-like macro case the line number is sampled (that is, `__LINE__` is expanded) _before_ the line is changed, and in the function-like macro it is sampled in the brief period when it is 1.  
  
Does this suggest anything?

---

## Comment 9

> Username: hkaiser  
> Created at: 2020-06-28 01:18:14 UTC  
> Url: https://github.com/boostorg/wave/issues/94#issuecomment-650662634  

> Does this suggest anything?  
  
Not right away :/  
  
The whole macro replacement process is intrinsically recursive, while `act_token` and `act_pos` are relatively global and 'on top' of the recursion. I wouldn't be surprised if the code handled those incorrectly .

---

## Comment 10

> Username: jefftrull  
> Created at: 2020-06-28 20:06:33 UTC  
> Url: https://github.com/boostorg/wave/issues/94#issuecomment-650815001  

I've reduced the failure a little more. This also produces the wrong answer:  
```c++  
#define FOO something BAR  
#define BAR(X) __LINE__   
#pragma wave trace(enable)  
FOO(Y)  
#pragma wave trace(disable)  
```  
The critical features appear to be:  
1. `BAR` is a function-type macro  
2. There is a token preceding `BAR` in the definition of `FOO`  
  
It's the presence of that token (`something`, here) with a location of line 1 that confuses Wave long enough to report `__LINE___` as 1. It comes off the pending queue and briefly sets the global position accordingly.

---

## Comment 11

> Username: jefftrull  
> Created at: 2020-07-16 21:08:38 UTC  
> Url: https://github.com/boostorg/wave/issues/94#issuecomment-659673965  

I've been studying the code for a while and had something of an epiphany today. The place where the `act_pos` value is set briefly to the "wrong" value (the location of the actual `__LINE__` instead of its expansion point) is somewhat unusual in that both it and `act_token` are updated at the same time. `act_token` is set at many points, but `act_pos` in only two. And if I remove the "problem" location, leaving only one, this case behaves correctly _and so do all the unit tests_.  
  
In short, how would you feel about deleting [this line](https://github.com/boostorg/wave/blob/85366343968e93bcafa77900d640ae3ebde7fb9a/include/boost/wave/util/cpp_iterator.hpp#L867)?

---

## Comment 12

> Username: jefftrull  
> Created at: 2020-07-16 21:13:15 UTC  
> Url: https://github.com/boostorg/wave/issues/94#issuecomment-659676267  

I may have spoken too soon. The new output from Wave, given this change, is `3 2` which is closer but still wrong.

---

## Comment 13

> Username: hkaiser  
> Created at: 2020-07-16 22:14:08 UTC  
> Url: https://github.com/boostorg/wave/issues/94#issuecomment-659703796  

> In short, how would you feel about deleting [this line](https://github.com/boostorg/wave/blob/85366343968e93bcafa77900d640ae3ebde7fb9a/include/boost/wave/util/cpp_iterator.hpp#L867)?  
  
Do it! ;-)
