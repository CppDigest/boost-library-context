# #13 Changes for Embarcadero C++ clang-based compilers, targeting Boost 1.74. Change __BORLANDC__ to BOOST_BORLANDC, which is defined in Boost conf… [Merged]

> Username: eldiener  
> Created at: 2020-04-08 22:22:30 UTC  
> Updated at: 2020-04-16 14:48:53 UTC  
> Merged at: 2020-04-16 14:48:53 UTC  
> Closed at: 2020-04-16 14:48:53 UTC  
> Url: https://github.com/boostorg/property_map/pull/13  

…ig for the Embarcadero non-clang-based compilers.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-04-10 13:26:48 UTC  
> Url: https://github.com/boostorg/property_map/pull/13#issuecomment-612027672  

# [Codecov](https://codecov.io/gh/boostorg/property_map/pull/13?src=pr&el=h1) Report  
> Merging [#13](https://codecov.io/gh/boostorg/property_map/pull/13?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/property_map/commit/ab086d43e09a221f65f9a701bb8fb02f105ebfce&el=desc) will **decrease** coverage by `3.56%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/property_map/pull/13/graphs/tree.svg?width=650&height=150&src=pr&token=IAO17unWyC)](https://codecov.io/gh/boostorg/property_map/pull/13?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #13      +/-   ##  
===========================================  
- Coverage    56.64%   53.07%   -3.57%       
===========================================  
  Files            6        5       -1       
  Lines          143      130      -13       
  Branches        49       47       -2       
===========================================  
- Hits            81       69      -12       
  Misses          17       17                
+ Partials        45       44       -1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/property_map/pull/13?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/property\_map/property\_map.hpp](https://codecov.io/gh/boostorg/property_map/pull/13/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9wZXJ0eV9tYXAvcHJvcGVydHlfbWFwLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/property\_map/vector\_property\_map.hpp](https://codecov.io/gh/boostorg/property_map/pull/13/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9wZXJ0eV9tYXAvdmVjdG9yX3Byb3BlcnR5X21hcC5ocHA=) | | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/property_map/pull/13?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/property_map/pull/13?src=pr&el=footer). Last update [ab086d4...18a9f72](https://codecov.io/gh/boostorg/property_map/pull/13?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-04-13 16:56:22 UTC  
> Url: https://github.com/boostorg/property_map/pull/13#issuecomment-612986860  

The commit does not have the correct email and credentials, which is why GitHub isn't hyperlinking it to the maintainer's account. This is caused by an incorrect local Git configuration. Use `git config` for the relevant settings to resolve.

---

## Comment 3

> Username: eldiener  
> Created_at: 2020-04-13 18:06:44 UTC  
> Url: https://github.com/boostorg/property_map/pull/13#issuecomment-613019357  

>   
>   
> The commit does not have the correct email and credentials, which is why GitHub isn't hyperlinking it to the maintainer's account. This is caused by an incorrect local Git configuration. Use `git config` for the relevant settings to resolve.  
  
Where in the commit does it show the email and credentials which you are talking about ?

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2020-04-13 18:17:27 UTC  
> Url: https://github.com/boostorg/property_map/pull/13#issuecomment-613024076  

![image](https://user-images.githubusercontent.com/1503976/79147154-5a22d080-7d78-11ea-9bfc-da0aed0f7b01.png)

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2020-04-13 18:18:36 UTC  
> Updated_at: 2020-04-13 18:18:47 UTC  
> Url: https://github.com/boostorg/property_map/pull/13#issuecomment-613024587  

Note the email associated with the commit, it doesn't match the email on your GitHub account:  
  
![image](https://user-images.githubusercontent.com/1503976/79147236-7888cc00-7d78-11ea-8e54-56c7a489616a.png)

---

## Comment 6

> Username: eldiener  
> Created_at: 2020-04-13 18:23:20 UTC  
> Url: https://github.com/boostorg/property_map/pull/13#issuecomment-613026726  

It is still a valid email account for me. Like many people I have more than one email address.

---

## Comment 7

> Username: mloskot  
> Created_at: 2020-04-13 18:56:16 UTC  
> Url: https://github.com/boostorg/property_map/pull/13#issuecomment-613041320  

@eldiener  
  
https://help.github.com/en/github/setting-up-and-managing-your-github-user-account/adding-an-email-address-to-your-github-account

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2020-04-13 19:29:51 UTC  
> Url: https://github.com/boostorg/property_map/pull/13#issuecomment-613056175  

Looks better now :)

---
