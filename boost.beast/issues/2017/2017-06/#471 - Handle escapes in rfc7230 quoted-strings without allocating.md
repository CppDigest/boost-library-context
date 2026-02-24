# #471 - Handle escapes in rfc7230 quoted-strings without allocating [Closed]

> Username: vinniefalco  
> Created at: 2017-06-12 04:03:03 UTC  
> Updated at: 2022-10-12 01:53:44 UTC  
> Closed at: 2022-10-12 01:53:44 UTC  
> Url: https://github.com/boostorg/beast/issues/471  

There's a big fat `std::string` in `<beast/http/impl/rfc7230.ipp>`

---

## Comment 1

> Username: stale[bot]  
> Created at: 2018-04-24 17:52:36 UTC  
> Url: https://github.com/boostorg/beast/issues/471#issuecomment-384022634  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2022-10-11 17:56:48 UTC  
> Url: https://github.com/boostorg/beast/issues/471#issuecomment-1275069812  

it would be possible to use a string_view or sth, but the most efficient way is probably to reuse the string, so it ideally becomes one allocation.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-10-11 22:59:46 UTC  
> Url: https://github.com/boostorg/beast/issues/471#issuecomment-1275379063  

no one has complained

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2022-10-12 00:51:47 UTC  
> Url: https://github.com/boostorg/beast/issues/471#issuecomment-1275445148  

Sure #2539 seems easy enough though.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-10-12 00:55:59 UTC  
> Url: https://github.com/boostorg/beast/issues/471#issuecomment-1275447588  

is 2539 a breaking api change?

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2022-10-12 01:01:43 UTC  
> Url: https://github.com/boostorg/beast/issues/471#issuecomment-1275450949  

No, `unquote` is a private function used in one place
