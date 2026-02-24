# #776 style: GDB printers [Merged]

> Username: alandefreitas  
> Created at: 2023-07-27 19:50:54 UTC  
> Updated at: 2024-02-21 00:55:37 UTC  
> Merged at: 2023-08-15 16:33:41 UTC  
> Closed at: 2023-08-15 16:33:41 UTC  
> Url: https://github.com/boostorg/url/pull/776  

I have slowly developed these pretty printers over time as I needed help debugging the most complex details of Boost.Url (in other words, there was no extra work involved in this). They can now complement the Boost.Url natvis file already provided by the library.  
  
To use the printers, users can create or modify their `.gdbinit` file to contain the following:  
  
```python  
python  
import sys  
# Make GDB see the directory with the printers  
sys.path.insert(0, '/path/to/boost/url/extra/gdb')  
# Import the function that registers the printers with GDB  
from boost_url_printers import register_boost_url_printers  
# Effectively register the printers  
register_boost_url_printers()  
end  
```  
  
(This is all documented)  
  
so that in  
  
```cpp  
auto r = parse_uri("https://username:password@example.com:8080/path/to/resource?query_param=value#section");  
```  
  
this  
  
![image](https://github.com/boostorg/url/assets/5369819/28407902-8246-458b-8c4d-99fd7632b5f0)  
  
becomes  
  
![image](https://github.com/boostorg/url/assets/5369819/b352fe5f-5775-4093-a929-bd2cf45d4239)  
  
This could save users a lot of time trying to figure out why a string parsed but the result of `encoded_X` is not what they are expecting.  
  
Developers can also register the printers with the `dev` option set to `True`.   
  
```python  
register_boost_url_printers(dev=True)  
```  
  
In this mode, the fields will represent the internal offsets for all parts of the URLs in the underlying data structure and represent the substrings at each of these offsets.  
  
![image](https://github.com/boostorg/url/assets/5369819/bf6d9713-0662-4b27-a896-fc94456d9593)  
  
This is useful when developing algorithms that need to transform the URL.  
  
When a `pct_string_view` contains visible pct-encoded characters, their printers will include both representations so there's no need to reason about the difference between the encoded and decoded versions or check ascii tables.  
  
```python  
auto r = parse_uri("https://us%65rnam%65:password@%65xampl%65.com:8080/path/to/r%65sourc%65?qu%65ry_param=valu%65#s%65ction");  
```  
  
![image](https://github.com/boostorg/url/assets/5369819/b6d1c1c5-cdda-4653-8bdd-be7c9b50ded6)

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-07-27 20:12:38 UTC  
> Url: https://github.com/boostorg/url/pull/776#issuecomment-1654494657  

GCOVR code coverage report [https://776.url.prtest2.cppalliance.org/gcovr/index.html](https://776.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://776.url.prtest2.cppalliance.org/genhtml/index.html](https://776.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://776.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://776.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-07-27 20:17:30 UTC  
> Updated_at: 2023-07-27 20:22:05 UTC  
> Url: https://github.com/boostorg/url/pull/776#issuecomment-1654505791  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/776?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#776](https://app.codecov.io/gh/boostorg/url/pull/776?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1de7b0f) into [develop](https://app.codecov.io/gh/boostorg/url/commit/516e0093c55271a6ec9b9f271292fc29bcd586cd?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (516e009) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/776/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/776?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #776   +/-   ##  
========================================  
  Coverage    97.27%   97.27%             
========================================  
  Files          156      156             
  Lines         8565     8565             
========================================  
  Hits          8332     8332             
  Misses         233      233             
```  
  
  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/776?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/776?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [516e009...1de7b0f](https://app.codecov.io/gh/boostorg/url/pull/776?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-07-27 20:30:05 UTC  
> Url: https://github.com/boostorg/url/pull/776#issuecomment-1654526131  

GCOVR code coverage report [https://776.url.prtest2.cppalliance.org/gcovr/index.html](https://776.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://776.url.prtest2.cppalliance.org/genhtml/index.html](https://776.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://776.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://776.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---
