# #669 X3: Fix error_handler::get_line_start [Merged]

> Username: Garzet  
> Created at: 2021-04-24 07:09:34 UTC  
> Updated at: 2021-04-24 23:50:54 UTC  
> Merged at: 2021-04-24 23:24:14 UTC  
> Closed at: 2021-04-24 23:24:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/669  

When the first line of input would be an empty line, `error_handler` would  
fail to properly report an error due to a bug in `get_line_start` method.  
Corner case check has been added to fix this.  
  
Example:  
```  
std::string input = R"(  
        var x int = 1 ** 2;   
)";  
```  
Expected:  
```  
In line 2:  
Error! Expecting primary_expression here:  
       var x int = 1 ** 2;  
______________________^_  
```  
Produced:  
```  
In line 2:  
Error! Expecting primary_expression here:  
  
^_  
```

---

## Comment 1

> Username: djowel  
> Created_at: 2021-04-24 09:03:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/669#issuecomment-826061435  

Looks good to me. Thanks for the PR!

---

## Comment 2

> Username: Kojoley  
> Created_at: 2021-04-24 12:46:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/669#issuecomment-826087765  

Could you please provide a test or at least a reproducer? The solutions seems like a hack to me.

---

## Comment 3

> Username: Garzet  
> Created_at: 2021-04-24 20:20:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/669#issuecomment-826148343  

I've made changes to the `error_handler::get_line_start` so that is returns line start always, not only when the error occurred in the first line of the input.  
I've also added a test case to `test/x3/error_handler.cpp`.

---

## Review 4 [Commented]

> Username: Kojoley  
> Created_at: 2021-04-24 21:08:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/669#pullrequestreview-644067029  

📁 include/boost/spirit/home/x3/support/utility/error_reporting.hpp

```diff
 167 |+         } else {
 168 |+             return latest + 1;
 169 |+         }
```

> Username: Kojoley  
> Created_at: 2021-04-24 21:07:22 UTC  
> Updated_at: 2021-04-24 21:31:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/669#discussion_r619709981  

I still do not understand what is going on here. Could not it be simply:  
```cpp  
        if (latest != pos)  
            ++latest;  
        return latest;  
```

> Username: Kojoley  
> Created_at: 2021-04-24 21:28:03 UTC  
> Updated_at: 2021-04-24 21:31:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/669#discussion_r619712023  

But it is still be unsound.

---

📁 include/boost/spirit/home/x3/support/utility/error_reporting.hpp

```diff
 209 |         Iterator start = get_line_start(first, err_pos);
 202 |-         if (start != first)
 203 |-             ++start;
```

> Username: Kojoley  
> Created_at: 2021-04-24 21:07:32 UTC  
> Updated_at: 2021-04-24 21:31:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/669#discussion_r619710011  

This indeed should have been inside `get_line_start`.


---

## Review 5 [Commented]

> Username: Kojoley  
> Created_at: 2021-04-24 21:28:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/669#pullrequestreview-644068258  

📁 include/boost/spirit/home/x3/support/utility/error_reporting.hpp

```diff
 158 |         for (Iterator i = first; i != pos; ++i)
 159 |             if (*i == '\r' || *i == '\n')
 160 |                 latest = i;
```

> Username: Kojoley  
> Created_at: 2021-04-24 21:28:28 UTC  
> Updated_at: 2021-04-24 21:31:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/669#discussion_r619712061  

I am pretty sure that the only needed fix for the function is `latest = i;` -> `latest = ++i;`.


---

## Comment 6

> Username: Garzet  
> Created_at: 2021-04-24 21:35:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/669#issuecomment-826156591  

Indeed -.-, sorry for waisting your time...

---

## Comment 7

> Username: Kojoley  
> Created_at: 2021-04-24 21:41:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/669#issuecomment-826157122  

> Indeed -.-, sorry for waisting your time...  
  
You had spend your time dealing with the bugs in the library and tried to fix them. I appreciate it a lot.

---

## Comment 8

> Username: djowel  
> Created_at: 2021-04-24 23:50:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/669#issuecomment-826168350  

> > Indeed -.-, sorry for waisting your time...  
>   
> You had spend your time dealing with the bugs in the library and tried to fix them. I appreciate it a lot.  
  
Same here. And thanks for inspecting the PR, @Kojoley

---
