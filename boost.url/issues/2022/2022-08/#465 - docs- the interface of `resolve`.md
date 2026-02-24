# #465 - docs: the interface of `resolve` [Closed]

> Username: akrzemi1  
> Created at: 2022-08-27 09:55:25 UTC  
> Updated at: 2022-11-07 20:58:11 UTC  
> Closed at: 2022-11-07 20:58:11 UTC  
> Url: https://github.com/boostorg/url/issues/465  

This is in response to the request in Slack.  
  
First, The docs should list the precondiitons of the function:  
  
`&dest != &base` and `&dest != &ref`.  
  
Regarding the `result` type, because so many funcitons return it, there should be a section in the docs explaining the concept and introducing the notions:  
  
 - *`r` with success value `T`* - rerutn object of type `result<T>` where `r.has_value()` is `true`.  
 - *`r` with error condition `E`* - rerutn object of type `result<T>` where `r.has_value()` is `false` and `r.error() == E` is `true`.  
  
Then refer to this page form individual functions' descriptions.  
  
For `resolve`, specify all error condiions, which I think is one:  
  
`error::not_a_base` -- when `base.has_scheme()` is `false`.  
  
Third, in the prose do not say things like "The base URL __must__ satisfy the *absolute-URI* grammar.": it is either a precondition or an error condition.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-11-03 23:59:31 UTC  
> Url: https://github.com/boostorg/url/issues/465#issuecomment-1302805057  

is this resolved?

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-11-04 21:08:42 UTC  
> Updated at: 2022-11-04 21:08:52 UTC  
> Url: https://github.com/boostorg/url/issues/465#issuecomment-1304262154  

Yes. We already have a section about `result`. I created a PR with a small change to the `resolve` javadocs to include the error condition.
