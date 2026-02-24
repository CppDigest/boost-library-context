# #324 Lex: Fixed C2001 'newline in constant' warning in generated lexers [Merged]

> Username: Kojoley  
> Created at: 2017-12-14 18:54:10 UTC  
> Updated at: 2017-12-15 13:21:34 UTC  
> Merged at: 2017-12-15 13:21:31 UTC  
> Closed at: 2017-12-15 13:21:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/324  

Closes https://svn.boost.org/trac10/ticket/11540.  
  
Actually this should have been caught by `regression_matlib_generate_switch`  
test, but it is not complex enough to cover all cases. We do not have the  
coverage reports yet, but the fix is trivial so I think it is ok.

---

## Comment 1

> Username: hkaiser  
> Created_at: 2017-12-14 19:17:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/324#issuecomment-351808597  

Could you explain what you had in mind with this patch, please? From what I see you're now comparing against a multi-character literal - is that legal in the first place?

---

## Comment 2

> Username: hkaiser  
> Created_at: 2017-12-14 19:18:12 UTC  
> Updated_at: 2017-12-14 19:18:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/324#issuecomment-351808922  

Sorry, I take that back - I see now what you're doing.

---

## Review 3 [Approved]

> Username: hkaiser  
> Created_at: 2017-12-14 19:18:35 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/spirit/pull/324#pullrequestreview-83614965  

LGTM, thanks!

---
