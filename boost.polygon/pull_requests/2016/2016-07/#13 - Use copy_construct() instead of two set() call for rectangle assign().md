# #13 Use copy_construct() instead of two set() call for rectangle assign() [Open]

> Username: plopresti  
> Created at: 2016-07-10 22:01:50 UTC  
> Updated at: 2016-07-26 23:36:47 UTC  
> Url: https://github.com/boostorg/polygon/pull/13  

This produces significantly better code when the rectangle representation is an immutable pair of points (lower left / upper right); e.g. SSE vectors. And there is no cost in speed for the normal case, since modern compilers are very good at copy elision.

---

## Comment 1

> Username: plopresti  
> Created_at: 2016-07-15 17:44:38 UTC  
> Url: https://github.com/boostorg/polygon/pull/13#issuecomment-233019162  

I realize my justification for this is basically "trust me". But trust me, this is a good change...  
  
OK OK. What would you like to see for justification? Thanks.

---

## Comment 2

> Username: asydorchuk  
> Created_at: 2016-07-16 09:05:40 UTC  
> Url: https://github.com/boostorg/polygon/pull/13#issuecomment-233121255  

Hi, I am on vacation right now. I am going to look at the changes on  
Monday, July 18th.  
  
On Jul 15, 2016 7:44 PM, "plopresti" notifications@github.com wrote:  
  
> I realize my justification for this is basically "trust me". But trust me,  
> this is a good change...  
>   
> OK OK. What would you like to see for justification? Thanks.  
>   
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> https://github.com/boostorg/polygon/pull/13#issuecomment-233019162, or mute  
> the thread  
> https://github.com/notifications/unsubscribe-auth/ABDj-ZXUqn-ktE0n16Y4cyksE_j4oS_hks5qV8cGgaJpZM4JI7Y4  
> .

---

## Comment 3

> Username: asydorchuk  
> Created_at: 2016-07-20 22:26:01 UTC  
> Url: https://github.com/boostorg/polygon/pull/13#issuecomment-234102931  

Thanks for your contribution! However it doesn't seem that this pull request solves any problem. That's why I am reluctant to merge it, cause there is no active development on the library and any such small changes potentially can break the existing functionality.

---

## Comment 4

> Username: plopresti  
> Created_at: 2016-07-26 23:36:47 UTC  
> Url: https://github.com/boostorg/polygon/pull/13#issuecomment-235438657  

I understand. Still, this makes a big performance difference in my application...  
  
What if I create a patch that preserves the existing behavior by default, but allows me to override the implementation? (I am thinking of changing y_r_assign to a template class and having it default to "true"; then I can specialize it to provide a more efficient "assign" function for my own class.)  
  
If this sounds vaguely acceptable, I will work up a pull request. Thanks.

---
