# #159 - Can't erase on vector<cobalt::promise<void>> [Open]

> Username: rotrida  
> Created at: 2024-03-11 21:29:08 UTC  
> Updated at: 2024-05-15 10:50:53 UTC  
> Url: https://github.com/boostorg/cobalt/issues/159  

I'm using a vector or a deque to wait for my promises to complete.  
  
`std::deque<boost::cobalt::promise<void>> session_promisses;`  
  
When I try to erase,  
  
`session_promisses.erase(session_promisses.begin() + session_index);`  
  
I get the following error:  
  
use of deleted function ‘boost::cobalt::detail::promise_receiver<void>& boost::cobalt::detail::promise_receiver<void>::operator=(const boost::cobalt::detail::promise_receiver<void>&)’  
[build]    92 |   receiver_ = std::move(lhs.receiver_);

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-03-15 10:30:39 UTC  
> Url: https://github.com/boostorg/cobalt/issues/159#issuecomment-1999368944  

It looks like the promises are currently not assignable. I'll fix this, but that won't make it into boost before 1.86. In the meantime a list should work.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2024-05-02 00:40:52 UTC  
> Url: https://github.com/boostorg/cobalt/issues/159#issuecomment-2089344202  

Actually made it into the last release, can you verify the current boost release solved the issue?

---

## Comment 3

> Username: rotrida  
> Created at: 2024-05-03 13:42:36 UTC  
> Url: https://github.com/boostorg/cobalt/issues/159#issuecomment-2093050659  

Will check soon. I'm using boost via conan. As soon as the release is  
available there.  
  
Thanks.  
  
On Thu, 2 May 2024 at 01:41, Klemens Morgenstern ***@***.***>  
wrote:  
  
> Actually made it into the last release, can you verify the current boost  
> release solved the issue?  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/cobalt/issues/159#issuecomment-2089344202>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AKTWNTUYUPWQUMAIRHYXHPTZAGDSTAVCNFSM6AAAAABERCO2WWVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDAOBZGM2DIMRQGI>  
> .  
> You are receiving this because you authored the thread.Message ID:  
> ***@***.***>  
>

---

## Comment 4

> Username: rotrida  
> Created at: 2024-05-09 16:36:40 UTC  
> Url: https://github.com/boostorg/cobalt/issues/159#issuecomment-2103015089  

@klemens-morgenstern,  
  
I believe the fix isn't on boost 1.85. The error is still happening.

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2024-05-10 01:21:47 UTC  
> Url: https://github.com/boostorg/cobalt/issues/159#issuecomment-2103682512  

Seems I didn't merge in time. That's a shame.

---

## Comment 6

> Username: rotrida  
> Created at: 2024-05-15 10:50:52 UTC  
> Url: https://github.com/boostorg/cobalt/issues/159#issuecomment-2112182989  

No worries. Let's check on the next one.  
  
Thanks again!  
  
On Fri, 10 May 2024 at 02:22, Klemens Morgenstern ***@***.***>  
wrote:  
  
> Seems I didn't merge in time. That's a shame.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/cobalt/issues/159#issuecomment-2103682512>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AKTWNTTGCBMWCOEAJKT5UP3ZBQOMBAVCNFSM6AAAAABERCO2WWVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDCMBTGY4DENJRGI>  
> .  
> You are receiving this because you authored the thread.Message ID:  
> ***@***.***>  
>
