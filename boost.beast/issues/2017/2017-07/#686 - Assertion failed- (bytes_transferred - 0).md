# #686 - Assertion failed: (bytes_transferred > 0) [Closed]

> Username: mikea  
> Created at: 2017-07-28 00:22:48 UTC  
> Updated at: 2017-07-31 21:46:19 UTC  
> Closed at: 2017-07-28 23:29:35 UTC  
> Url: https://github.com/boostorg/beast/issues/686  

I upgraded beast from old 61 to 89 and now it fails with this assertion regularly on our channel:  
  
Assertion failed: (bytes_transferred > 0), function operator(), file /Users/mike/.conan/data/Beast/89/satorivideo/master/package/def3ba7be14694f242b67c577db3871951dd33cd/include/boost/beast/websocket/impl/read.ipp, line 507.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-28 00:31:40 UTC  
> Url: https://github.com/boostorg/beast/issues/686#issuecomment-318523528  

Investigating...

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-07-28 01:22:33 UTC  
> Url: https://github.com/boostorg/beast/issues/686#issuecomment-318530388  

We have studied the code generating the assert and determined that it contains a defect. Specifically, when an incoming final frame has length zero, the read algorithm does not terminate. This defect is fixed in **version 90** please try it and confirm that it resolves your problem. Thank you for the bug report!  
See: https://github.com/boostorg/beast/pull/687

---

## Comment 3

> Username: mikea  
> Created at: 2017-07-28 16:34:34 UTC  
> Url: https://github.com/boostorg/beast/issues/686#issuecomment-318701636  

@vinniefalco I don't think the #687 was merged.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-07-28 16:59:57 UTC  
> Url: https://github.com/boostorg/beast/issues/686#issuecomment-318707839  

@mikea It is part of https://github.com/boostorg/beast/pull/690 and I am waiting for you to try it to confirm that it fixes your issue before merging. Thanks!

---

## Comment 5

> Username: mikea  
> Created at: 2017-07-28 19:58:45 UTC  
> Url: https://github.com/boostorg/beast/issues/686#issuecomment-318748110  

Testing a change is problematic for lots of reasons (package management). I  
can easily verify it once it gets to the main repo though.  
On Fri, Jul 28, 2017 at 09:59 Vinnie Falco <notifications@github.com> wrote:  
  
> @mikea <https://github.com/mikea> It is part of #690  
> <https://github.com/boostorg/beast/pull/690> and I am waiting for you to  
> try it to confirm that it fixes your issue before merging. Thanks!  
>  
> —  
> You are receiving this because you were mentioned.  
>  
>  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/686#issuecomment-318707839>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AAAMpDx8_GwmhJ3F6Wl3RUmkY6iibxYHks5sShOPgaJpZM4Ol_iT>  
> .  
>  
--   
Mike

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-07-28 20:01:41 UTC  
> Url: https://github.com/boostorg/beast/issues/686#issuecomment-318748732  

>I can easily verify it once it gets to the main repo though.  
  
If I merge it to the **develop** branch will that be accessible for you?  
  
Thanks

---

## Comment 7

> Username: mikea  
> Created at: 2017-07-28 23:28:18 UTC  
> Url: https://github.com/boostorg/beast/issues/686#issuecomment-318784212  

Yes  
  
On Fri, Jul 28, 2017 at 13:01 Vinnie Falco <notifications@github.com> wrote:  
  
> I can easily verify it once it gets to the main repo though.  
>  
> If I merge it to the *develop* branch will that be accessible for you?  
>  
> Thanks  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/686#issuecomment-318748732>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AAAMpAdQAVhnqusA_8Ul4zodtF7GoFfjks5sSj4ogaJpZM4Ol_iT>  
> .  
>  
--   
Mike

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-07-28 23:33:06 UTC  
> Url: https://github.com/boostorg/beast/issues/686#issuecomment-318784738  

Merged to **develop**

---

## Comment 9

> Username: mikea  
> Created at: 2017-07-31 21:46:18 UTC  
> Url: https://github.com/boostorg/beast/issues/686#issuecomment-319205956  

I have updated to version 95 and I don't see this assertion anymore. Thanks.
