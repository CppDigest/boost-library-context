# #41 use std::addressof (or boost::addressof) to make serialization work w… [Closed]

> Username: alfC  
> Created at: 2016-05-11 03:21:24 UTC  
> Updated at: 2016-05-31 09:20:58 UTC  
> Closed at: 2016-05-29 17:29:06 UTC  
> Url: https://github.com/boostorg/serialization/pull/41  

…ith class with unary operator&()  
  
I have a class that overloads the unary operator&() the only way I was able to make it work was to modify the library.   
There may be a non intrusive way by creating a proxy return of operator& that can be casted to the right pointer or to the other use but requires a lot of hacking an unnecessary performance hit.

---

## Comment 1

> Username: robertramey  
> Created_at: 2016-05-29 17:29:06 UTC  
> Url: https://github.com/boostorg/serialization/pull/41#issuecomment-222372309  

won't this break builds for compilers pre-C++11 ?  Note that the library has taken great pains to preserve backward  compatibility and many, many users depend upon this.

---

## Comment 2

> Username: alfC  
> Created_at: 2016-05-29 23:03:11 UTC  
> Url: https://github.com/boostorg/serialization/pull/41#issuecomment-222388082  

What about using boost::addressof ? That should be compatible with pre-c++11. Or it can be effectively implemented as a one liner. Thanks, A  
  
> On May 29, 2016, at 10:29 AM, Robert Ramey notifications@github.com wrote:  
>   
> won't this break builds for compilers pre-C++11 ? Note that the library has taken great pains to preserve backward compatibility and many, many users depend upon this.  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 3

> Username: robertramey  
> Created_at: 2016-05-30 01:45:06 UTC  
> Url: https://github.com/boostorg/serialization/pull/41#issuecomment-222396711  

can you make the change and test it?  
  
On 5/29/16 4:03 PM, alfC wrote:  
  
> What about using boost::addressof ? That should be compatible with   
> pre-c++11. Or it can be effectively implemented as a one liner. Thanks, A  
>   
> > On May 29, 2016, at 10:29 AM, Robert Ramey   
> > notifications@github.com wrote:  
> >   
> > won't this break builds for compilers pre-C++11 ? Note that the   
> > library has taken great pains to preserve backward compatibility and   
> > many, many users depend upon this.  
> >   
> > —  
> > You are receiving this because you authored the thread.  
> > Reply to this email directly, view it on GitHub, or mute the thread.  
>   
> —  
> You are receiving this because you modified the open/close state.  
> Reply to this email directly, view it on GitHub   
> https://github.com/boostorg/serialization/pull/41#issuecomment-222388082,   
> or mute the thread   
> https://github.com/notifications/unsubscribe/AB4R3DCYCBerLSwHJRTRxibiMMAFBnpzks5qGhsvgaJpZM4Ibszj.  
  
##   
  
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 4

> Username: alfC  
> Created_at: 2016-05-31 00:28:55 UTC  
> Updated_at: 2016-05-31 00:29:01 UTC  
> Url: https://github.com/boostorg/serialization/pull/41#issuecomment-222569215  

Sure, I guess the best way is to use `boost::addressof`. I have tested it with my class and with my local fedora installation of boost, and works. Now I am willing to do a serious test it with the rest to boost/serialization/tests and add a new test. Sorry to ask what is the easiest way of running the existings boost/serialization/tests. So far I did `git clone ...`, what make tool and options  should I run to compile or run tests. My Fedora `bjam` doesn't recognize any of the `jam` files. Is there a tutorial somewhere that can be applied to the current directory structure of boost/serialization (as currently in git)?

---

## Comment 5

> Username: alfC  
> Created_at: 2016-05-31 09:20:58 UTC  
> Url: https://github.com/boostorg/serialization/pull/41#issuecomment-222636005  

Hi Robert,  
  
I made the modifications and added a test. I don't know if this is the way  
to do it, but I added a macro to restore the old code (DONT_USE_ADDRESSOF)  
  
I am learning how to use git and now I realize that I should have created  
branch instead of commiting to develop branch.  
Sorry if the requests turn out to be messy.  
  
If I figure out how to run all the existings test I will, so far it worked  
with my older code.  
  
Thanks,  
Alfredo  
  
On Sun, May 29, 2016 at 6:45 PM, Robert Ramey notifications@github.com  
wrote:  
  
> can you make the change and test it?  
>   
> On 5/29/16 4:03 PM, alfC wrote:  
>   
> > What about using boost::addressof ? That should be compatible with  
> > pre-c++11. Or it can be effectively implemented as a one liner. Thanks, A  
> >   
> > > On May 29, 2016, at 10:29 AM, Robert Ramey  
> > > notifications@github.com wrote:  
> > >   
> > > won't this break builds for compilers pre-C++11 ? Note that the  
> > > library has taken great pains to preserve backward compatibility and  
> > > many, many users depend upon this.  
> > >   
> > > —  
> > > You are receiving this because you authored the thread.  
> > > Reply to this email directly, view it on GitHub, or mute the thread.  
> >   
> > —  
> > You are receiving this because you modified the open/close state.  
> > Reply to this email directly, view it on GitHub  
> > <  
> > https://github.com/boostorg/serialization/pull/41#issuecomment-222388082>,  
> >   
> > or mute the thread  
> > <  
> > https://github.com/notifications/unsubscribe/AB4R3DCYCBerLSwHJRTRxibiMMAFBnpzks5qGhsvgaJpZM4Ibszj  
> > .  
>   
> ##   
>   
> Robert Ramey  
> www.rrsd.com  
> (805)569-3793  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> https://github.com/boostorg/serialization/pull/41#issuecomment-222396711,  
> or mute the thread  
> https://github.com/notifications/unsubscribe/ACMfYKjlUHI6fTrP03ue9Aux2STVyzT5ks5qGkEjgaJpZM4Ibszj  
> .  
  
##   
  
Alfredo

---
