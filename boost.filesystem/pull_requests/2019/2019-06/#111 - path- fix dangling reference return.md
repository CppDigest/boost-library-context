# #111 path: fix dangling reference return [Closed]

> Username: CJBussey  
> Created at: 2019-06-19 09:05:41 UTC  
> Updated at: 2019-10-18 20:42:39 UTC  
> Closed at: 2019-06-19 12:09:49 UTC  
> Url: https://github.com/boostorg/filesystem/pull/111  

* issue #110

---

## Comment 1

> Username: Lastique  
> Created_at: 2019-06-19 12:10:02 UTC  
> Url: https://github.com/boostorg/filesystem/pull/111#issuecomment-503533639  

Thanks.

---

## Review 2 [Commented]

> Username: rcdailey  
> Created_at: 2019-10-18 13:35:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/111#pullrequestreview-303893841  

I'm having a hard time understanding why the original code was broken. Can someone explain?

---

## Comment 3

> Username: Lastique  
> Created_at: 2019-10-18 15:36:50 UTC  
> Url: https://github.com/boostorg/filesystem/pull/111#issuecomment-543801051  

```  
path const& p = path("abc") / "def"  
```  
  
`p` is left a dangling reference because although const references do bind to rvalue references, they don't extend the lifetime of the referred object in this case.

---

## Comment 4

> Username: rcdailey  
> Created_at: 2019-10-18 20:42:39 UTC  
> Url: https://github.com/boostorg/filesystem/pull/111#issuecomment-543935177  

So it's because of the unnamed temporaries. Makes sense. Thank you.  
  
On Fri, Oct 18, 2019, 10:36 AM Andrey Semashev <notifications@github.com>  
wrote:  
  
> path const& p = path("abc") / "def"  
>  
> p is left a dangling reference because although const references do bind  
> to rvalue references, they don't extend the lifetime of the referred object  
> in this case.  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/filesystem/pull/111?email_source=notifications&email_token=AANPU5QHZJ4NBB32H3T6E4TQPHJZNA5CNFSM4HZHOGNKYY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOEBU35WY#issuecomment-543801051>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AANPU5XVOL5747AJ23NV22LQPHJZNANCNFSM4HZHOGNA>  
> .  
>

---
