# #502 Example: usage of distance formula [Merged]

> Username: adl1995  
> Created at: 2018-07-26 11:38:32 UTC  
> Updated at: 2019-11-02 13:28:23 UTC  
> Merged at: 2019-11-02 13:28:23 UTC  
> Closed at: 2019-11-02 13:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/502  

This pull request adds a simple example on how to use a formula (in this case, Karney's direct method).

---

## Comment 1

> Username: awulkiew  
> Created_at: 2019-10-31 17:31:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/502#issuecomment-548485945  

I think this PR should be cleaned up before merging. It seems to add code related to `karney_inverse` formula and strategy which AFAIU was merged recently with additional fixes. It adds many unnecessary merge commits, etc. I thought that GitHub should take it into account and show only the differing parts of the code? Am I missing something?

---

## Comment 2

> Username: vissarion  
> Created_at: 2019-11-01 08:25:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/502#issuecomment-548707059  

I agree. I asked Adeel to have a look. It seems that only the last 2  
commits are needed.  
  
On Thu, Oct 31, 2019, 19:31 Adam Wulkiewicz <notifications@github.com>  
wrote:  
  
> I think this PR should be cleaned up before merging. It seems to add code  
> related to karney_inverse formula and strategy which AFAIU was merged  
> recently with additional fixes. It adds many unnecessary merge commits,  
> etc. I thought that GitHub should take it into account and show only the  
> differing parts of the code? Am I missing something?  
>  
> —  
> You are receiving this because your review was requested.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/geometry/pull/502?email_source=notifications&email_token=AA35UTRHUGNGVMJ5QURNA7DQRMJAVA5CNFSM4FMGUXF2YY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOECYTWOI#issuecomment-548485945>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA35UTRUHJOKQEY2HOHIEKTQRMJAVANCNFSM4FMGUXFQ>  
> .  
>

---

## Comment 3

> Username: adl1995  
> Created_at: 2019-11-02 06:18:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/502#issuecomment-549015230  

I just rebased this branch against develop. In fact we only need a single commit in this PR.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2019-11-02 13:24:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/502#issuecomment-549043484  

@adl1995 Thanks!

---

## Review 5 [Approved]

> Username: vissarion  
> Created_at: 2019-11-02 13:27:47 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/502#pullrequestreview-310770720  

Thanks

---
