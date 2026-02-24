# #23 Fix write_json non-pretty print extra endl [Closed]

> Username: erichkeane  
> Created at: 2016-04-22 22:55:01 UTC  
> Updated at: 2023-11-18 00:05:37 UTC  
> Closed at: 2023-11-18 00:05:36 UTC  
> Url: https://github.com/boostorg/property_tree/pull/23  

According to bug #12149, the non-pretty version of write_json should not  
print the trailing endl.  This patch only prints the endl in the pretty  
case, and only does the flush component of the endl in the non-pretty  
version.  
  
Signed-off-by: Erich Keane erich.keane@intel.com

---

## Comment 1

> Username: erichkeane  
> Created_at: 2016-04-22 22:56:33 UTC  
> Url: https://github.com/boostorg/property_tree/pull/23#issuecomment-213618245  

Submitted in response to: https://svn.boost.org/trac/boost/ticket/12149

---

## Comment 2

> Username: ashtum  
> Created_at: 2023-11-17 09:00:15 UTC  
> Url: https://github.com/boostorg/property_tree/pull/23#issuecomment-1815981344  

@pdimov, this change may break user code that relies on the line break. Otherwise, it seems like a valid fix. should we merge?

---

## Comment 3

> Username: pdimov  
> Created_at: 2023-11-17 10:32:38 UTC  
> Url: https://github.com/boostorg/property_tree/pull/23#issuecomment-1816121222  

The Trac tickets about it are http://trac.cpp.al/trac10/ticket/12149 and http://trac.cpp.al/trac10/ticket/3828 and it looks like the trailing `endl` in the compact case was unintentional and a source of complaints, so this looks like the right fix.  
  
On the other hand, this is from 2016, so who knows :-)

---

## Comment 4

> Username: ashtum  
> Created_at: 2023-11-17 11:49:42 UTC  
> Updated_at: 2023-11-17 11:51:55 UTC  
> Url: https://github.com/boostorg/property_tree/pull/23#issuecomment-1816249939  

Nice, I didn't know we still had access to these tickets.  
So, I guess we can finally merge this patient PR.

---

## Comment 5

> Username: erichkeane  
> Created_at: 2023-11-17 14:47:32 UTC  
> Url: https://github.com/boostorg/property_tree/pull/23#issuecomment-1816559526  

Oh boy is this a blast from the past!  I recall this being important at the time to me, though I haven't used Property Tree for JSON in quite a while.

---

## Comment 6

> Username: pdimov  
> Created_at: 2023-11-17 16:05:18 UTC  
> Url: https://github.com/boostorg/property_tree/pull/23#issuecomment-1816692482  

Boost commit history is like a "who's who". :-)

---

## Comment 7

> Username: pdimov  
> Created_at: 2023-11-18 00:05:36 UTC  
> Url: https://github.com/boostorg/property_tree/pull/23#issuecomment-1817271680  

Applied in https://github.com/boostorg/property_tree/commit/71dd6583254dc9fd6c210c2bafd0936f85c6ab6f.

---
