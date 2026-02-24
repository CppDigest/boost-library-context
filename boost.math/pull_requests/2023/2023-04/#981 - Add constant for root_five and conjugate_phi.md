# #981 Add constant for root_five and conjugate_phi [Closed]

> Username: mborland  
> Created at: 2023-04-27 10:09:00 UTC  
> Updated at: 2023-06-02 10:40:40 UTC  
> Closed at: 2023-05-31 19:43:23 UTC  
> Url: https://github.com/boostorg/math/pull/981  

Closes #980

---

## Comment 1

> Username: mborland  
> Created_at: 2023-04-28 09:24:23 UTC  
> Url: https://github.com/boostorg/math/pull/981#issuecomment-1527258971  

The only CI failure was a fail to clone. @gpeterhoff is this what you wanted? I called your psi `conjugate_phi`. Since that is not the first thing that comes to mind with psi I didn't want to name it that.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2023-04-28 14:00:56 UTC  
> Updated_at: 2023-04-28 14:01:39 UTC  
> Url: https://github.com/boostorg/math/pull/981#issuecomment-1527614759  

I feel a bit uneasy about this change. For one, we must remember that we can only occupy so much space in the users minds. `conjugate_phi` can of course be looked up in the docs and found to equal 1-phi, but I doubt anyone will figure this out just by reading the name. And I don't see many people searching for it either, and computing 1-phi on the fly or even at compile-time is so easy.  
  
As to root 5: I feel like this opens the floodgates to the square root of any prime. There needs to be some reason why root(5) shouldn't just be computed in a constexpr context rather than put as a long text string and lexically cast.

---

## Comment 3

> Username: gpeterhoff  
> Created_at: 2023-04-28 15:17:03 UTC  
> Url: https://github.com/boostorg/math/pull/981#issuecomment-1527722307  

Am 28.04.23 um 16:01 schrieb Nick:  
> I feel a bit uneasy about this change. For one, we must remember that we can only occupy so much space in the users minds. |conjugate_phi| can of course be looked up in the docs and found to equal 1-phi, but I doubt anyone will figure this out just by reading it. And I don't see many people searching for it either, and computing 1-phi on the fly is so easy.  
>   
> As to root 5: I feel like this opens the floodgates to the square root of any prime. There needs to be some reason why root(5) shouldn't just be computed in a constexpr context rather than put as a long text string and lexically cast.  
>   
> —  
> Reply to this email directly, view it on GitHub <https://github.com/boostorg/math/pull/981#issuecomment-1527614759>, or unsubscribe <https://github.com/notifications/unsubscribe-auth/A7OFJ6ZRIDM74KJBPBP5DYTXDPESJANCNFSM6AAAAAAXNUDJ3E>.  
> You are receiving this because you were mentioned.Message ID: ***@***.***>  
>   
  
See https://www.boost.org/doc/libs/1_82_0/libs/math/doc/html/math_toolkit/constants.html  
1) There are already root_two and root_three, so providing root_five is consistent.  
2) The (non constexpr) cases for boost::multiprecision must also be covered. For float/double/long double (and soon for std::floatN_t) the constants are already constexpr.

---
