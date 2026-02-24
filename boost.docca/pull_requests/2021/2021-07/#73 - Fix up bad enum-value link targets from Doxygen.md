# #73 Fix up bad enum-value link targets from Doxygen [Closed]

> Username: evanlenz  
> Created at: 2021-07-09 06:04:40 UTC  
> Updated at: 2021-09-06 14:57:26 UTC  
> Closed at: 2021-09-06 14:57:26 UTC  
> Url: https://github.com/boostorg/docca/pull/73  

Fix #70

---

## Comment 1

> Username: evanlenz  
> Created_at: 2021-08-30 01:39:26 UTC  
> Url: https://github.com/boostorg/docca/pull/73#issuecomment-907944672  

Is there anything in particular that's holding up this fix?

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2021-08-30 02:16:19 UTC  
> Url: https://github.com/boostorg/docca/pull/73#issuecomment-907959798  

Good question. @madmongo1 ? Does this fix the enums?

---

## Comment 3

> Username: madmongo1  
> Created_at: 2021-09-03 12:39:09 UTC  
> Url: https://github.com/boostorg/docca/pull/73#issuecomment-912506648  

In the documentation of `doc/html/beast/ref/boost__beast__http__message/result/overload1.html` the `status::unknown` does not link to anything (it is not emitted as a hyperlink).  
  
However, tracking through to the base class documentation at `doc/html/beast/ref/boost__beast__http__header/result/overload1.html`, `status::unknown` is emitted as a hyperlink to `doc/html/beast/ref/boost__beast__http__status.html` (which seems correct).  
  
Is this the intended behaviour?  
@evanlenz

---

## Comment 4

> Username: madmongo1  
> Created_at: 2021-09-03 12:45:18 UTC  
> Url: https://github.com/boostorg/docca/pull/73#issuecomment-912510277  

![missing-link](https://user-images.githubusercontent.com/5172425/132007239-464c7e89-75f6-49b9-aec6-0b9f3143decc.png)

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2021-09-03 14:38:44 UTC  
> Url: https://github.com/boostorg/docca/pull/73#issuecomment-912589092  

@madmongo1 it looks like you don't have the boost docbook templates installed

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2021-09-03 14:40:39 UTC  
> Url: https://github.com/boostorg/docca/pull/73#issuecomment-912590497  

It seems like it used to work and now it doesn't?

---

## Comment 7

> Username: madmongo1  
> Created_at: 2021-09-03 14:52:58 UTC  
> Url: https://github.com/boostorg/docca/pull/73#issuecomment-912598723  

> @madmongo1 it looks like you don't have the boost docbook templates installed  
  
I take it that this is the reason for the lack of formatting?   
I've often wondered about that.

---

## Comment 8

> Username: evanlenz  
> Created_at: 2021-09-03 17:37:33 UTC  
> Url: https://github.com/boostorg/docca/pull/73#issuecomment-912700607  

> It seems like it used to work and now it doesn't?  
  
That is indeed what it seems. But I will try to reproduce. It should produce a link (the right one!).

---

## Comment 9

> Username: evanlenz  
> Created_at: 2021-09-03 17:46:41 UTC  
> Updated_at: 2021-09-03 17:47:28 UTC  
> Url: https://github.com/boostorg/docca/pull/73#issuecomment-912705899  

@madmongo1 It works for me. When I switch docca to branch issue70-link-to-wrong-enum-page and run `b2 clean` followed by `b2` in the libs/beast/doc directory, it produces the result I expect:  
  
![image](https://user-images.githubusercontent.com/604974/132046640-0387fb8a-3b6b-4973-9793-4bd2702a3660.png)  
  
The "status::unknown" link takes me to this page: `doc/html/beast/ref/boost__beast__http__status.html`

---

## Comment 10

> Username: madmongo1  
> Created_at: 2021-09-03 17:49:05 UTC  
> Url: https://github.com/boostorg/docca/pull/73#issuecomment-912706998  

I rebased develop onto the pr branch before running the test. I wonder if that’s relevant.  
In any case, will retest.

---

## Comment 11

> Username: evanlenz  
> Created_at: 2021-09-03 17:57:35 UTC  
> Url: https://github.com/boostorg/docca/pull/73#issuecomment-912711895  

It might be! The link might be getting disappeared somehow from the more recent fix. I'm just speculating though.  
  
This is a case where the XSLT visualization/debug option would come in very handy! :-) @grisumbras

---

## Comment 12

> Username: evanlenz  
> Created_at: 2021-09-03 18:07:40 UTC  
> Url: https://github.com/boostorg/docca/pull/73#issuecomment-912717889  

I tried rebasing develop onto the pr branch as well just now, and it didn't make a difference (still works for me).

---

## Comment 13

> Username: madmongo1  
> Created_at: 2021-09-03 18:23:13 UTC  
> Url: https://github.com/boostorg/docca/pull/73#issuecomment-912726539  

Ok great. Will clean and rebuild

---

## Comment 14

> Username: madmongo1  
> Created_at: 2021-09-06 14:54:03 UTC  
> Url: https://github.com/boostorg/docca/pull/73#issuecomment-913707218  

Confirmed correct on second attempt.

---

## Comment 15

> Username: madmongo1  
> Created_at: 2021-09-06 14:57:26 UTC  
> Url: https://github.com/boostorg/docca/pull/73#issuecomment-913709206  

rebased to develop

---
