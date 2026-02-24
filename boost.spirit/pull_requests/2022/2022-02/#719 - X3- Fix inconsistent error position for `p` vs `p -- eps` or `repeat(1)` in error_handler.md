# #719 X3: Fix inconsistent error position for `p` vs `p >> eps` or `repeat(1)[p]` in error_handler [Open]

> Username: Kojoley  
> Created at: 2022-02-18 00:03:01 UTC  
> Updated at: 2025-09-25 13:55:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/719  

* Do not rely on iterator rollback on fail: Preparation to relax iterator rollback policy, which is also currently violated by not rolling back skipper almost everywhere, so this should fix postskip disabling.  
* Remove unneeded iterator rollback: Under the current iterator rollback policy rollbacks could be omitted in compound parser which don't advance iterator themselves because the called subparser is responsible for rollback on failure. In reality these rollbacks are undoing skipper work what renders as inconsistency in error handling position (`p` vs `p >> eps` or `repeat(1)[p]`).  
  
Fixes #712

---

## Comment 1

> Username: djowel  
> Created_at: 2022-02-18 00:20:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/719#issuecomment-1043669360  

This is a major move! Nice work @Kojoley.   
  
It would be impossible to determine the correctness by just looking at the code. So I suggest adding tests for each of the changes (if there are no such tests yet).

---

## Comment 2

> Username: Kojoley  
> Created_at: 2022-03-01 00:18:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/719#issuecomment-1054843110  

I was planning to add more tests to have better coverage, but now I do not know when I will be able to continue the PR.  
As you may know, Russia, my home country, has invaded Ukraine and currently is in an active war. I am very sad and depressed, millions of peaceful Ukrainians are suffering from inhumane decisions made by politicians which do not represent me and I had never chosen, but I still feel responsible and guilty. The only thing I could do now is to publicly say that I do not support the war with Ukraine and it should be stopped immediately.

---

## Comment 3

> Username: djowel  
> Created_at: 2022-03-01 00:57:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/719#issuecomment-1054864336  

> I was planning to add more tests to have better coverage, but now I do not know when I will be able to continue the PR. As you may know, Russia, my home country, has invaded Ukraine and currently is in an active war. I am very sad and depressed, millions of peaceful Ukrainians are suffering from inhumane decisions made by politicians which do not represent me and I had never chosen, but I still feel responsible and guilty. The only thing I could do now is to publicly say that I do not support the war with Ukraine and it should be stopped immediately.  
  
I have high regard and respect for you, @Kojoley. This message further affirms that. I applaud you for making a statement. Peace!

---

## Comment 4

> Username: cppljevans  
> Created_at: 2022-03-01 15:03:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/719#issuecomment-1055538343  

I certainly want to echo Joel's statement.  I applaud your brave stance,   
and especially, the Ukrainians.  
  
The world (except maybe just communist China and North Korea), condemn   
the invasion.  
  
Hopefully he's not unhinged as some news reports suggest :(((  
  
(BTW, sorry Joel for the duplicate send).  
  
On 2/28/2022 6:58 PM, Joel de Guzman wrote:  
>  
>     I was planning to add more tests to have better coverage, but now  
>     I do not know when I will be able to continue the PR. As you may  
>     know, Russia, my home country, has invaded Ukraine and currently  
>     is in an active war. I am very sad and depressed, millions of  
>     peaceful Ukrainians are suffering from inhumane decisions made by  
>     politicians which do not represent me and I had never chosen, but  
>     I still feel responsible and guilty. The only thing I could do now  
>     is to publicly say that I do not support the war with Ukraine and  
>     it should be stopped immediately.  
>  
> I have high regard and respect for you, @Kojoley   
> <https://github.com/Kojoley>. This message further affirms that. I   
> applaud you for making a statement. Peace!  
>  
> —  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/spirit/pull/719#issuecomment-1054864336>,   
> or unsubscribe   
> <https://github.com/notifications/unsubscribe-auth/AAEFBA5NDVI2QPVLBVVVLVLU5VTRZANCNFSM5OWHZTSQ>.  
> Triage notifications on the go with GitHub Mobile for iOS   
> <https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675>   
> or Android   
> <https://play.google.com/store/apps/details?id=com.github.android&referrer=utm_campaign%3Dnotification-email%26utm_medium%3Demail%26utm_source%3Dgithub>.   
>  
> You are receiving this because you are subscribed to this   
> thread.Message ID: ***@***.***>  
>

---

## Comment 5

> Username: saki7  
> Created_at: 2025-05-09 09:58:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/719#issuecomment-2865933317  

This is a great fix. We really need to get this merged. All we need to do is resolving the conflicts and adding some unit tests. At first glance, the implementation seems ok.

---

## Comment 6

> Username: saki7  
> Created_at: 2025-09-25 13:55:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/719#issuecomment-3334242242  

> https://github.com/boostorg/spirit/issues/833#issuecomment-3333749234  
>   
> It's been quite a while and I don't remember much, it's most likely the case https://github.com/boostorg/spirit/pull/719 was supposed to fix. I remember I almost finished the patch but haven't updated the PR. I can try to find and upload my latest version of that PR, but I'm not going to rebase and resolve merge conflicts that emerged from the code churn that has happened since then, sorry.  
  
@Kojoley Could you push the recent version? If you don't have time then feel free to push it as-is. I'm not entirely sure if it can be merged into feature-frozen X3, but your work is still important and I might be able to adopt it to the X4 codebase.

---
