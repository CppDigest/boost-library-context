# #80 - rvalue overload of backend() [Closed]

> Username: johnmcfarlane  
> Created at: 2018-08-28 06:40:49 UTC  
> Updated at: 2018-09-20 08:07:15 UTC  
> Closed at: 2018-09-18 08:20:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/80  

Forgive my incomplete knowledge of value categories and your library types, but might [`backend()`](https://github.com/boostorg/multiprecision/blob/61f4fd8778074cbd78bceee739b5076846b75b23/include/boost/multiprecision/number.hpp#L711) benefit from a third overload taking an rvalue reference?  
  
I've found these are useful for when the object is passed in in the form: `unwrap(some_object{})` and that in the body of the overloaded function, the returned value tends to be wrapped in a call to `std::forward<Backend>`. Without it, I suspect there are cases where a copy will be required where a move could be used instead. (However, I've not confirmed that this is best practice other than to ape some of the API of standard types such as `std::tuple`.)

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-09-02 17:48:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/80#issuecomment-417947350  

Might well be useful, let me experiment.  I assume you mean:  
  
```  
Backend&& backend()&&;  
```  
?

---

## Comment 2

> Username: johnmcfarlane  
> Created at: 2018-09-02 18:02:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/80#issuecomment-417948207  

Thanks. That's right, yes.

---

## Comment 3

> Username: johnmcfarlane  
> Created at: 2018-09-18 13:11:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/80#issuecomment-422387205  

thanks :)  
  
On Tue, 18 Sep 2018 at 09:20 jzmaddock <notifications@github.com> wrote:  
  
> Closed #80 <https://github.com/boostorg/multiprecision/issues/80> via  
> d434cf9  
> <https://github.com/boostorg/multiprecision/commit/d434cf992619e0db7b3745a39a9d2ed3240f749e>  
> .  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/multiprecision/issues/80#event-1851692227>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AAszN_MbSCmUl2xXUP3GYKMK1O9n9blaks5ucKzBgaJpZM4WPANe>  
> .  
>

---

## Comment 4

> Username: jzmaddock  
> Created at: 2018-09-20 08:07:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/80#issuecomment-423084367  

Just so you know - I only detected a couple of places in our code where that overload is called - and it made no difference in those particular cases - that's not to say it isn't the right thing to do though!  
Probably only useful if you're calling backend() in your own code as ours typically handles rvalue-move semantics before it gets to that point (though there may be an opportunity to remove casts / simplify some code by relying on these new overloads).
