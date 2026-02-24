# #246 Make log file counter representation locale-independent [Closed]

> Username: Grevozin  
> Created at: 2025-04-19 09:24:18 UTC  
> Updated at: 2025-04-19 15:17:05 UTC  
> Closed at: 2025-04-19 15:15:44 UTC  
> Url: https://github.com/boostorg/log/pull/246  

In our project using Boost.Log we had an issue with log rotation on Windows that the number and size of log files did not respect the corresponding limits (files were not deleted properly). Upon closer examination the following problem became apparent. The file, say, number 1234 was called something like:  
```  
20250512-124722.1 234.log  
```  
Note the space in `1 234`. This happened in locale `std::locale::global(".UTF-8")` on Windows. However, the `scan_for_files()` method that collects old logs, expected the number in "normal" `1234` format. This in turn lead to the fact that Boost.Log did not admit such logs with a space as his own, leaving them on disk forever if the application was restarted and needed to recollect them later with `scan_for_files()`.  
  
Similar problem is reproducible on Linux. For example, I tried to set global locale to `std::locale::global("en_US.UTF-8")`, and the same file becomes called:  
```  
20250512-124722.1,234.log  
```  
Again, not a bad name per se, but `scan_for_files()` is unable to parse the comma properly.  
  
This patch fixes the problem by using `std::to_string` and `std::to_wstring` to format numbers, so that the numbers always become contiguous digit sequences digestible by `scan_for_files()`. Alternative solutions considered:  
  
- Fix `scan_for_files()` itself to be able to parse numbers in arbitrary locale. Seems complicated and unneeded.  
- Use `.imbue(std::locale("C"))` on the `std::ostream` object that produces the string. This method works, but I was a bit concerned about its portability: is C locale surely present anywhere? It seems like using the functions from C++11 standard is a more surefire solution.

---

## Comment 1

> Username: Lastique  
> Created_at: 2025-04-19 10:26:43 UTC  
> Url: https://github.com/boostorg/log/pull/246#issuecomment-2816648890  

`std::to_string` also depends on the current locale, only the C locale (installed with [`std::setlocale`](https://en.cppreference.com/w/cpp/locale/setlocale)), as it is defined in terms of `std::sprintf`. A locale-free implementation would need to use something like Boost.Spirit.Karma or hand-rolled formatting. I'll look into it.

---

## Comment 2

> Username: Grevozin  
> Created_at: 2025-04-19 14:14:40 UTC  
> Url: https://github.com/boostorg/log/pull/246#issuecomment-2816724193  

I tried to do a quick check and seems like it doesn't depend in practice. Here is an example of dependence from `man 3 printf`:  
```  
For  some  numeric  conversions  a radix character ("decimal point") or thousands' grouping character is used.  
The actual character used depends on the LC_NUMERIC part of the locale.  (See setlocale(3).)   
The POSIX locale uses '.' as radix character, and does not have a grouping character.  Thus,  
  
           printf("%'.2f", 1234567.89);  
  
results in "1234567.89" in the POSIX locale, in "1234567,89" in the nl_NL locale, and in "1.234.567,89" in the da_DK locale.  
```  
Here the thousand separator only appears because of the usage of `'` option in `%'.2f` formatter:  
```  
'      For decimal conversion (i, d, u, f, F, g, G) the output is to be grouped with thousands' grouping characters if the locale information indicates any.  (See setlocale(3).)  
       Note that many versions of gcc(1) cannot parse this option and will issue a warning.  (SUSv2 did not include %'F, but SUSv3 added it.)  
```  
I didn't manage to induce any effect on `printf("%d", num)` or `std::to_string` by setting the locale, since there is no `'` option. This is probably what they mean on [cppreference](https://en.cppreference.com/w/cpp/string/basic_string/to_string):  
```  
     std::to_string relies on the current C locale for formatting purposes, and therefore concurrent calls to std::to_string from multiple threads may result in partial serialization of calls.  
        The results of overloads for integer types do not rely on the current C locale, and thus implementations generally avoid access to the current C locale in these overloads for both correctness and performance. However, such avoidance is not guaranteed by the standard.   
```  
So technically it depends on C locale since `printf` family of functions technically depends on C locale in some corner cases, but in reality the path of execution never uses that locale.

---

## Comment 3

> Username: Lastique  
> Created_at: 2025-04-19 15:17:04 UTC  
> Url: https://github.com/boostorg/log/pull/246#issuecomment-2816748135  

Thanks for the report, should be fixed now. I opted for manual formatting to ensure that locale is not involved.

---
