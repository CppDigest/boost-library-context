# #105 Fix usage of deprecated headers [Merged]

> Username: Lastique  
> Created at: 2020-05-11 16:31:35 UTC  
> Updated at: 2020-07-28 01:13:57 UTC  
> Merged at: 2020-05-11 16:59:37 UTC  
> Closed at: 2020-05-11 16:59:37 UTC  
> Url: https://github.com/boostorg/range/pull/105  

This silences warnings about including deprecated headers `boost/detail/iterator.hpp` and `boost/bind.hpp`.

---

## Comment 1

> Username: Lastique  
> Created_at: 2020-06-23 19:35:51 UTC  
> Url: https://github.com/boostorg/range/pull/105#issuecomment-648375217  

Please, merge to master.

---

## Comment 2

> Username: glenfe  
> Created_at: 2020-06-23 22:29:16 UTC  
> Url: https://github.com/boostorg/range/pull/105#issuecomment-648461557  

@neilgroves are you planning on merging develop to master? If not I'll merge just this commit to master because it's needed in the 1.74 release.

---

## Comment 3

> Username: neilgroves  
> Created_at: 2020-06-23 22:45:31 UTC  
> Url: https://github.com/boostorg/range/pull/105#issuecomment-648466689  

Merging develop to master seems like the best approach to me. Feel free to  
merge 105 if you want it done before I get merged tomorrow.  
  
Regards,  
Neil  
  
  
On Tue, 23 Jun 2020, 23:29 Glen Fernandes, <notifications@github.com> wrote:  
  
> @neilgroves <https://github.com/neilgroves> are you planning on merging  
> develop to master? If not I'll merge just this commit to master because  
> it's needed in the 1.74 release.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/range/pull/105#issuecomment-648461557>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABTIJ2QDKJWMVDLHTSVN6X3RYEUETANCNFSM4M6B36HQ>  
> .  
>

---

## Comment 4

> Username: neilgroves  
> Created_at: 2020-06-26 19:54:37 UTC  
> Url: https://github.com/boostorg/range/pull/105#issuecomment-650369574  

Hi,  
  
I completed the merge of Boost.Range develop -> master and it has cycled a  
few builds in the test matrix looking okay. Please let me know if this is  
insufficient to address any issues you have.  
  
Regards,  
Neil  
  
<http://www.avg.com/email-signature?utm_medium=email&utm_source=link&utm_campaign=sig-email&utm_content=webmail>  
Virus-free.  
www.avg.com  
<http://www.avg.com/email-signature?utm_medium=email&utm_source=link&utm_campaign=sig-email&utm_content=webmail>  
<#DAB4FAD8-2DD7-40BB-A1B8-4E2AA1F9FDF2>  
  
On Tue, 23 Jun 2020 at 23:29, Glen Fernandes <notifications@github.com>  
wrote:  
  
> @neilgroves <https://github.com/neilgroves> are you planning on merging  
> develop to master? If not I'll merge just this commit to master because  
> it's needed in the 1.74 release.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/range/pull/105#issuecomment-648461557>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABTIJ2QDKJWMVDLHTSVN6X3RYEUETANCNFSM4M6B36HQ>  
> .  
>

---

## Comment 5

> Username: jefftrull  
> Created_at: 2020-07-28 01:10:07 UTC  
> Url: https://github.com/boostorg/range/pull/105#issuecomment-664717966  

Judging by my own experience and bug #106 this has not made it into 1.74 beta.

---

## Comment 6

> Username: jefftrull  
> Created_at: 2020-07-28 01:13:57 UTC  
> Url: https://github.com/boostorg/range/pull/105#issuecomment-664719171  

I spoke too soon. That bug (and my issues) are for a different set of deprecated headers within range itself.

---
