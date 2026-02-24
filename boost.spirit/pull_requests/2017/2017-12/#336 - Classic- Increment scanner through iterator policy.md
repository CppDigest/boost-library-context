# #336 Classic: Increment scanner through iterator policy [Merged]

> Username: Kojoley  
> Created at: 2017-12-19 17:09:17 UTC  
> Updated at: 2018-05-17 20:15:50 UTC  
> Merged at: 2017-12-20 23:53:08 UTC  
> Closed at: 2017-12-20 23:53:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/336  

Closes https://svn.boost.org/trac10/ticket/7371  
  
The trac ticket author was right and left unnoticed for 5 years.

---

## Comment 1

> Username: djowel  
> Created_at: 2017-12-19 22:40:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/336#issuecomment-352908565  

👍

---

## Comment 2

> Username: martinsjogren  
> Created_at: 2018-04-17 14:09:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/336#issuecomment-382006265  

Trying to upgrade to boost 1.67.0 I noticed that this change affects the arguments I get to an action.  
  
I.e. with  
    `parse("foo bar", strlit("foo") [action] >> strlit("bar"), space_p)`  
my action gets exactly "foo" as input, but with  
    `parse("foo bar", (strlit("fo") >> chlit('o')) [action] >> strlit("bar"), space_p)`  
the action actually gets "foo " as input.  
  
Obviously my real code is more complex, but the common thing is that the last thing before my action is a `>> chlit(X)` and it broke my code.  
  
Is this to be expected and something I just have to work around in my code, or is it something that could be fixed in spirit?

---

## Comment 3

> Username: Kojoley  
> Created_at: 2018-04-17 16:14:06 UTC  
> Url: https://github.com/boostorg/spirit/pull/336#issuecomment-382051676  

@martinsjogren No, it is not an expected behavior. Thanks for discovering and reporting the regression.  
@djowel I think cfe94349e220516b1c1a689273ee408790c815d3 (`fix-for-pr336` branch) should fix the problem, but it will be fine to simply revert the PR.

---

## Comment 4

> Username: djowel  
> Created_at: 2018-04-17 21:56:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/336#issuecomment-382162197  

OK. I'm fine with either approach. Looks like a regression alright.

---

## Comment 5

> Username: kleinj  
> Created_at: 2018-05-16 13:22:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/336#issuecomment-389516387  

We ran into the same issue when trying to upgrade to 1.67.0 (extra whitespace in identifiers, etc), breaking the parsing. https://github.com/boostorg/spirit/commit/cfe94349e220516b1c1a689273ee408790c815d3 referenced above fixes the regression for us. Will it be merged?

---

## Comment 6

> Username: djowel  
> Created_at: 2018-05-17 20:15:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/336#issuecomment-389996118  

Yes it will be merged.

---
