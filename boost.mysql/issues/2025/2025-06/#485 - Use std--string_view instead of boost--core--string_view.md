# #485 - Use std::string_view instead of boost::core::string_view [Closed]

> Username: octopus-prime  
> Created at: 2025-06-15 08:24:29 UTC  
> Updated at: 2025-07-05 11:32:28 UTC  
> Closed at: 2025-06-26 09:36:42 UTC  
> Url: https://github.com/boostorg/mysql/issues/485  

Hi,  
  
`boost::core::string_view` has some problems.  
E.g. when using std::println() ->  
Compile errors or ugly output, e.g.  
```  
        std::println("Error: {}", err.what());  
        std::println("Client diagnostics: {}", err.get_diagnostics().server_message());  
```  
->  
```  
Error: er_no_db_error [mysql.common-server:1046]  
Client diagnostics: ['N', 'o', ' ', 'd', 'a', 't', 'a', 'b', 'a', 's', 'e', ' ', 's', 'e', 'l', 'e', 'c', 't', 'e', 'd']  
```  
  
Why does boost::mysql not use std::string_view if available?  
Does compile. Gives correct output.  
```  
        std::println("Error: {}", err.what());  
        std::println("Client diagnostics: {}", (std::string_view) err.get_diagnostics().server_message());  
```  
->  
```  
Error: er_no_db_error [mysql.common-server:1046]  
Client diagnostics: No database selected  
```  
  
Also see  
https://github.com/boostorg/core/issues/190  
https://github.com/boostorg/core/issues/167

---

## Comment 1

> Username: anarthal  
> Created at: 2025-06-15 12:55:34 UTC  
> Url: https://github.com/boostorg/mysql/issues/485#issuecomment-2973801473  

@pdimov does this happen because `core::string_view` doesn't have support for `std::format`? What would you think of adding such support? I'm not keen of introducing another config macro here, but I'd like to fix the OP's issue.

---

## Comment 2

> Username: pdimov  
> Created at: 2025-06-15 16:28:57 UTC  
> Url: https://github.com/boostorg/mysql/issues/485#issuecomment-2974201754  

I’ll look into this after the WG21 meeting.

---

## Comment 3

> Username: anarthal  
> Created at: 2025-06-15 17:06:01 UTC  
> Url: https://github.com/boostorg/mysql/issues/485#issuecomment-2974279687  

Thanks. Good luck with the meeting.

---

## Comment 4

> Username: pdimov  
> Created at: 2025-06-25 15:11:55 UTC  
> Url: https://github.com/boostorg/mysql/issues/485#issuecomment-3005154286  

Should be fixed by https://github.com/boostorg/core/commit/65337d1af8ac477f79427948da7aeb5ce3245afa.

---

## Comment 5

> Username: anarthal  
> Created at: 2025-06-26 09:36:42 UTC  
> Url: https://github.com/boostorg/mysql/issues/485#issuecomment-3007847243  

@octopus-prime with Peter's fix, you should be able to use `std::println` with `boost::core::string_view` and get the same results as with `std::string_view`. It will be released with the next Boost release (1.89). Thanks for reporting this behavior.  
  
If you're OK with it, I will close this issue now. Please feel free to open new issues if you find any other surprising behavior like this.  
  
Thanks,  
Ruben.

---

## Comment 6

> Username: libbooze  
> Created at: 2025-06-26 17:52:12 UTC  
> Url: https://github.com/boostorg/mysql/issues/485#issuecomment-3009317835  

Issue with this fix(and it is best fix possible for non `std::` `string_view`) is that it drags in` <format>` header, so I would suggest that if possible in the few releases library switches to C++17 as minimal version so it can use `std::string_view`.   
  
If you ask why do I care so much since anyway everybody who has `<format> `header will likely include <format> anyway so this change is free:  
  
1. `std::string_view `can include internal headers while `boost::core::string_view `must include "public" headers, so even without `<format>` include `boost::core::string_view `compiles slower than `std::string_view`.  
2. Many codebases consider `<format> `☢ because ABI issues(e.g. if faster thing becomes available fmt can implement it even if it breaks ABI), meaning they prefer fmt, I have actually worked in a company where we intentionally preferred to use fmt over format. People doing the same will now be forced to waste build time on format even they do not use it.  
  
On my machine build times of empty program with just includes:  
  
| includes  |   time(seconds) |   
|------------------------|------------------------------------|  
| string_view(std) |  0.15  |   
| string_view.hpp(boost)  | 0.33  |   
| string_view.hpp(boost) with format | 0.60 |   
  
  
disclaimer: I know compiling file with just includes is not realistic, but I do not have full mysql application to rebuild, of somebody has more realistic codebase and is willing to do build benchmark with the new version of `core::string_view ` I would be very happy to learn those numbers.

---

## Comment 7

> Username: anarthal  
> Created at: 2025-06-29 17:14:39 UTC  
> Url: https://github.com/boostorg/mysql/issues/485#issuecomment-3016877484  

I'm reluctant to break compatibility, but I see what you mean. I've opened https://github.com/boostorg/mysql/issues/489 as an intent to know how bad breaking C++11/C++14 would be. If you can, please comment on this issue, it'd be very helpful for me.

---

## Comment 8

> Username: libbooze  
> Created at: 2025-07-05 11:32:28 UTC  
> Url: https://github.com/boostorg/mysql/issues/485#issuecomment-3038809642  

> I'm reluctant to break compatibility, but I see what you mean. I've opened [#489](https://github.com/boostorg/mysql/issues/489) as an intent to know how bad breaking C++11/C++14 would be. If you can, please comment on this issue, it'd be very helpful for me.  
  
Hi, I am not a user of mysql so I will not be voting as I think that is not appropriate. My participation in this thread is product of 2 things:   
  
1. I filed one of bugs in core for string_view  
2. I have bad experience before with this issue(C++ standard having "huge" headers) where 3rd party libs get to suffer in compile time because they must include huge header even if they need something trivial from it.   
  
With regards to issue: I would presume most users do not bother to follow github of libraries they use. Maybe you could announce in next Boost release this is the last release to support C++11/14, and if users want to change your mind they should say so in the issue. Not ideal, but might increase visibility.
