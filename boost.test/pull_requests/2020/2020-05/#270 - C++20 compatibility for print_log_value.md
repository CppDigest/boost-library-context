# #270 C++20 compatibility for print_log_value<> [Closed]

> Username: avikivity  
> Created at: 2020-05-06 15:49:41 UTC  
> Updated at: 2020-05-07 19:39:24 UTC  
> Closed at: 2020-05-07 16:27:53 UTC  
> Url: https://github.com/boostorg/test/pull/270  

operator<<(std::ostream&, const wchar_t*) has been removed from C++20 [1].  
Remove the corresponding print_log_value<> specialization.

---

## Comment 1

> Username: avikivity  
> Created_at: 2020-05-07 09:13:57 UTC  
> Url: https://github.com/boostorg/test/pull/270#issuecomment-625132454  

I see some automated checks failed, but as far as I can tell these failures are not related to my changes.

---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2020-05-07 15:28:17 UTC  
> Url: https://github.com/boostorg/test/pull/270#issuecomment-625325363  

The question is rather to know if this change is relevant: `print_log_value` is the entity that prints any type to the logger. Removing this would mean that we are not supporting anymore logging `wchar_t const*`.  
  
While the current implementation is incorrect (we just print the address of the string) it still does not prevent something like `BOOST_TEST(a == b)` to compile.   
  
This is a recurrent topic now, and it needs to be addressed. The "how" is unclear to me but my approach would not be the one you are proposing.

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2020-05-07 15:28:59 UTC  
> Url: https://github.com/boostorg/test/pull/270#issuecomment-625325791  

> I see some automated checks failed, but as far as I can tell these failures are not related to my changes.  
  
Yes this is also a recurrent topic :) I restarted some of those Appveyor is not yet working.

---

## Comment 4

> Username: avikivity  
> Created_at: 2020-05-07 16:27:53 UTC  
> Url: https://github.com/boostorg/test/pull/270#issuecomment-625360055  

> The question is rather to know if this change is relevant: `print_log_value` is the entity that prints any type to the logger. Removing this would mean that we are not supporting anymore logging `wchar_t const*`.  
>   
> While the current implementation is incorrect (we just print the address of the string) it still does not prevent something like `BOOST_TEST(a == b)` to compile.  
>   
> This is a recurrent topic now, and it needs to be addressed. The "how" is unclear to me but my approach would not be the one you are proposing.  
  
Actually the bug was already fixed in 8ac88c62dcc809d42daf8b6bef10f7adecc46dd1. I copied my hack without noticing that the code already changed. Sorry about the noise.

---

## Comment 5

> Username: raffienficiaud  
> Created_at: 2020-05-07 19:39:24 UTC  
> Url: https://github.com/boostorg/test/pull/270#issuecomment-625456785  

Thanks for the quick update!

---
