# #22 - doc/html/tutorial/8.html doesn't show compile-time checking of value ranges. [Closed]

> Username: biocomp  
> Created at: 2017-02-18 08:58:15 UTC  
> Updated at: 2018-09-23 22:26:35 UTC  
> Closed at: 2018-08-14 22:11:21 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/22  

To be honest, I only read the tutorials so far, so I'm likely missing something, but when values are known at compile time, I'd think safe_numerics should be able to validate them with constexpr support.  
  
Is it just that tutorial is not showing that?   
Maybe it should given that it talks about performance?

---

## Comment 1

> Username: robertramey  
> Created at: 2017-02-18 18:08:53 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/22#issuecomment-280863878  

On 2/18/17 12:58 AM, Artem Tokmakov wrote:  
>  
> To be honest, I only read the tutorials so far, so I'm likely missing   
> something, but when values are known at compile time, I'd think   
> safe_numerics should be able to validate them with constexpr support.  
>  
it can and does!  This is not emphasized in the "Tutorials and   
Motivating Examples".  My major concern in this section is to confront   
the common view "Why is this even necessary?  Why not just use int,   
etc?".  This view is surprisingly (to me) common and I felt I had to   
make an effort to "sell" this concept of this library. Sort of like the   
insurance salesman who sells something that people don't think they   
really need - but probably do.  
>  
> Is it just that tutorial is not showing that?  
>  
If an expression involving ints, longs, etc can be calculated at compile   
time by the compiler, replacing the int, long with safe<int>,   
safe<long>, etc should preserve that.  The implementation is based on   
functions which are all constexpr. However, sometimes it's a little   
unclear how constexpr works.  
>  
> Maybe it should given that it talks about performance?  
>  
I'll consider adding more information about this subject.  
  
Note there is information in the reference section about   
safe_literal<N>.  Some details are still be worked on.  
  
So I see that there should be more information on this subject in the   
section "Eliminating Runtime Performance"  
  
Thanks for your observations and comments.  They are very helpful  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/robertramey/safe_numerics/issues/22>, or mute the   
> thread   
> <https://github.com/notifications/unsubscribe-auth/AB4R3NY6U7SWqbnIR1v1P_EiSaz1L2mIks5rdrKogaJpZM4MFDZ_>.  
>  
  
--   
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 2

> Username: biocomp  
> Created at: 2017-02-19 07:35:50 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/22#issuecomment-280902041  

I was just confused a little because the tutorial #8 is about performance (and because I was reading from start and did not get to "Eliminating Runtime Penalty", but doesn't do full compile-time checking. I was expecting to find it there. A "quick fix" would be to have a note somewhere in the tutorial that we can go further with safe_literal<>.  
  
Thanks!

---

## Comment 3

> Username: robertramey  
> Created at: 2017-04-08 22:57:14 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/22#issuecomment-292751444  

safe types are all constexpr compatible so they can be used in compile   
time expressions.  I have to think of a way to illustrate the possible   
utility of this in my problem/solution format of the tutorials  
  
  
On 2/18/17 12:58 AM, Artem Tokmakov wrote:  
>  
> To be honest, I only read the tutorials so far, so I'm likely missing   
> something, but when values are known at compile time, I'd think   
> safe_numerics should be able to validate them with constexpr support.  
>  
> Is it just that tutorial is not showing that?  
> Maybe it should given that it talks about performance?  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/robertramey/safe_numerics/issues/22>, or mute the   
> thread   
> <https://github.com/notifications/unsubscribe-auth/AB4R3NY6U7SWqbnIR1v1P_EiSaz1L2mIks5rdrKogaJpZM4MFDZ_>.  
>  
  
--   
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 4

> Username: robertramey  
> Created at: 2018-08-10 22:48:55 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/22#issuecomment-412225881  

I've expanded coverage and explanation of both safe_range as well as safe_literal.  But I notice they are not mentioned in the tutorial and I think that would be useful.  You mention tutorial #8 but I'm not sure which one you're referring to.  I might have shuffled around the tutorials so the #8 doesn't mean anything to me any more.  You might take a look a the most recent package and update you're suggestion.
