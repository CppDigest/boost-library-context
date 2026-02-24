# #5 - Support safe numeric conversions from/to integral types to/from floating-point types [Closed]

> Username: LegalizeAdulthood  
> Created at: 2014-06-26 19:56:16 UTC  
> Updated at: 2018-09-23 22:26:35 UTC  
> Closed at: 2018-08-10 22:37:26 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/5  

As per a thread in comp.lang.c++.moderated when I naively attempted to crib code from this library to safely convert from long to double, I got unexpected results.  Someone pointed out to me that this library only announced supports for integral types and I was using a floating-point type.  D'oh!  
  
So I'd like to request a feature extension, or perhaps a similar library, that supports safe conversions back and forth between integral types and floating-point types.  Thanks :)

---

## Comment 1

> Username: robertramey  
> Created at: 2014-06-26 20:03:22 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/5#issuecomment-47272948  

Hmmm - I'd have to think about what that entails - I'm going to close this now as it's not so much an issue  (which I interpret as a bug) as a feature or extension request - May it would be better to leave a comment in the incubator.  Also I would be interested in knowing how it behaved.  Hopefully it would trap at compile time with a concept violation.  If not then THAT would be a bug since that is the intended behavior.

---

## Comment 2

> Username: LegalizeAdulthood  
> Created at: 2014-06-26 20:24:51 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/5#issuecomment-47275300  

I cribbed an excerpt from your library, so I wasn't using your library per-se.  
  
Hrm.... I was trying to find the usenet thread link on gmane, but I can't seem to get that site to respond right now.  
  
Here is a google groups link: https://groups.google.com/forum/#!topic/comp.lang.c++.moderated/9rK_Vh3UTkE

---

## Comment 3

> Username: johnmcfarlane  
> Created at: 2016-09-05 23:21:36 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/5#issuecomment-244820771  

A different case I found today does not trap at compile time:  
  
``` c++  
auto s = boost::numeric::safe<int8_t>{0};  
auto f = static_cast<float>(s);  
```  
  
compiles OK but seems to throw with the following output (Ubuntu 16.06 / clang 3.8):  
  
> unknown file: Failure  
> C++ exception with description "Value out of range for this safe type" thrown in the test body.Signal: SIGSEGV (Segmentation fault)  
  
Without floating-point conversion, there are many useful operations in fixed_point that won't work. (Sorry to get to this so late but re-writing my arithmetic operators took far longer than I had hoped.)

---

## Comment 4

> Username: robertramey  
> Created at: 2016-09-06 00:53:52 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/5#issuecomment-244826627  

John,  
  
Thanks for your help in checking this out.  This week I'll merge in your   
changes and make any other required changes.  
  
On 9/5/16 4:21 PM, John McFarlane wrote:  
  
> A different case I found today does not trap at compile time:  
>   
> auto  s = boost::numeric::safe<int8_t>{0};  
> auto  f =static_cast<float>(s);  
>   
> compiles OK but seems to throw with the following output (Ubuntu 16.06   
> / clang 3.8):  
>   
> ```  
> unknown file: Failure  
> C++ exception with description "Value out of range for this safe  
> type" thrown in the test body.Signal: SIGSEGV (Segmentation fault)  
> ```  
>   
> Without floating-point conversion, there are many useful operations in   
> fixed_point that won't work. (Sorry to get to this so late but   
> re-writing my arithmetic operators took far longer than I had hoped.)  
>   
> In my way of thinking, implicit conversion from floating to integer type   
> is a mistake. Conceptually it's wrong - floating and integer types are   
> used for different things - floating is more for measuring while   
> integers are for counting.  In addition, implicit conversion will   
> silently change the value which is another behavior I want to inhibit.    
> Finally, even the floating point value of 0.0 doesn't mean zero - but   
> rather some very small quantitity "near" zero.  So in my world  
  
safe<int8_t> s = 0.0f // should invoke error at compile time.  
  
The correct thing would be to use:  
  
#include <cmath>  
safe<int8_t> s = round(0.0f); // or floor, ceil, or trunc  
  
float f = safe<int8_t>{0} ;  // (or static_cast<float>(s) wouldn't   
produce incorrect result - but to me it it non-nonsensical so it's   
something I want to trap at compilation.  
  
I'll make the change so it traps at compile time.  
  
My reasoning above would suggest that the following should also trap at   
compile time:  
  
float f = safestd::int8_t;  
  
I don't know if I have that implemented or not - I'll check.  
  
Again, thanks again for your efforts here. I much appreciate it. I'm   
looking forward to meeting you at CPP con.  
  
Robert Ramey  
  
> —  
> You are receiving this because you modified the open/close state.  
> Reply to this email directly, view it on GitHub   
> https://github.com/robertramey/safe_numerics/issues/5#issuecomment-244820771,   
> or mute the thread   
> https://github.com/notifications/unsubscribe-auth/AB4R3K8ZG_mR8veLJUBK92NwBmucVeliks5qnKQBgaJpZM4CH-iq.  
  
##   
  
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 5

> Username: robertramey  
> Created at: 2016-09-09 06:00:29 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/5#issuecomment-245826687  

On 9/5/16 4:21 PM, John McFarlane wrote:  
  
> A different case I found today does not trap at compile time:  
>   
> auto s = boost::numeric::safe<int8_t>{0};  
> auto f = static_cast<float>(s);  
>   
> compiles OK but seems to throw with the following output (Ubuntu 16.06 /  
> clang 3.8):  
>   
> ```  
> unknown file: Failure  
> C++ exception with description "Value out of range for this safe  
> type" thrown in the test body.Signal: SIGSEGV (Segmentation fault)  
> ```  
>   
> Without floating-point conversion, there are many useful operations in  
> fixed_point that won't work. (Sorry to get to this so late but  
> re-writing my arithmetic operators took far longer than I had hoped.)  
  
I looked into this and found the problem.  The following code illustrates it  
  
```  
 float x = 0.0f;  
 float y = std::numeric_limits<float>::min();  
```  
  
the debugger displays:  
  
x   float   0   0  
y   float   0.0000000000000000000000000000000000000117549435   
0.0000000000000000000000000000000000000117549435  
  
so numeric_limits<float is not the value I expect> here.  
  
I'm using a recent version of clang with apple xcode.  
  
> —  
> You are receiving this because you modified the open/close state.  
> Reply to this email directly, view it on GitHub  
> https://github.com/robertramey/safe_numerics/issues/5#issuecomment-244820771,  
> or mute the thread  
> https://github.com/notifications/unsubscribe-auth/AB4R3K8ZG_mR8veLJUBK92NwBmucVeliks5qnKQBgaJpZM4CH-iq.

---

## Comment 6

> Username: johnmcfarlane  
> Created at: 2016-09-09 16:50:34 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/5#issuecomment-245971374  

Maybe lowest instead of min?  
  
On Thu, Sep 8, 2016, 11:00 PM Robert Ramey notifications@github.com wrote:  
  
> On 9/5/16 4:21 PM, John McFarlane wrote:  
>   
> > A different case I found today does not trap at compile time:  
> >   
> > auto s = boost::numeric::safe<int8_t>{0};  
> > auto f = static_cast<float>(s);  
> >   
> > compiles OK but seems to throw with the following output (Ubuntu 16.06 /  
> > clang 3.8):  
> >   
> > unknown file: Failure  
> > C++ exception with description "Value out of range for this safe  
> > type" thrown in the test body.Signal: SIGSEGV (Segmentation fault)  
> >   
> > Without floating-point conversion, there are many useful operations in  
> > fixed_point that won't work. (Sorry to get to this so late but  
> > re-writing my arithmetic operators took far longer than I had hoped.)  
>   
> I looked into this and found the problem. The following code illustrates it  
>   
> float x = 0.0f;  
> float y = std::numeric_limits<float>::min();  
>   
> the debugger displays:  
>   
> x float 0 0  
> y float 0.0000000000000000000000000000000000000117549435  
> 0.0000000000000000000000000000000000000117549435  
>   
> so numeric_limits<float is not the value I expect> here.  
>   
> I'm using a recent version of clang with apple xcode.  
>   
> > —  
> > You are receiving this because you modified the open/close state.  
> > Reply to this email directly, view it on GitHub  
> > <  
> > https://github.com/robertramey/safe_numerics/issues/5#issuecomment-244820771  
> > ,  
> > or mute the thread  
> > <  
> > https://github.com/notifications/unsubscribe-auth/AB4R3K8ZG_mR8veLJUBK92NwBmucVeliks5qnKQBgaJpZM4CH-iq  
> > .  
>   
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> https://github.com/robertramey/safe_numerics/issues/5#issuecomment-245826687,  
> or mute the thread  
> https://github.com/notifications/unsubscribe-auth/AAszN4JEVt4xN--M7uB1ePuFDhOqmm-fks5qoPX9gaJpZM4CH-iq  
> .

---

## Comment 7

> Username: robertramey  
> Created at: 2016-09-10 04:05:02 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/5#issuecomment-246087213  

On 9/9/16 9:50 AM, John McFarlane wrote:  
  
> Maybe lowest instead of min?  
> Right - good call. I had to spelunk through CPP reference to find this.    
> Who would guess this?  
  
! I'll fix this this weekend,  
  
Robert Ramey  
  
> On Thu, Sep 8, 2016, 11:00 PM Robert Ramey notifications@github.com   
> wrote:  
>   
> > On 9/5/16 4:21 PM, John McFarlane wrote:  
> >   
> > > A different case I found today does not trap at compile time:  
> > >   
> > > auto s = boost::numeric::safe<int8_t>{0};  
> > > auto f = static_cast<float>(s);  
> > >   
> > > compiles OK but seems to throw with the following output (Ubuntu   
> > > 16.06 /  
> > > clang 3.8):  
> > >   
> > > unknown file: Failure  
> > > C++ exception with description "Value out of range for this safe  
> > > type" thrown in the test body.Signal: SIGSEGV (Segmentation fault)  
> > >   
> > > Without floating-point conversion, there are many useful operations in  
> > > fixed_point that won't work. (Sorry to get to this so late but  
> > > re-writing my arithmetic operators took far longer than I had hoped.)  
> >   
> > I looked into this and found the problem. The following code   
> > illustrates it  
> >   
> > float x = 0.0f;  
> > float y = std::numeric_limits<float>::min();  
> >   
> > the debugger displays:  
> >   
> > x float 0 0  
> > y float 0.0000000000000000000000000000000000000117549435  
> > 0.0000000000000000000000000000000000000117549435  
> >   
> > so numeric_limits<float is not the value I expect> here.  
> >   
> > I'm using a recent version of clang with apple xcode.  
> >   
> > > —  
> > > You are receiving this because you modified the open/close state.  
> > > Reply to this email directly, view it on GitHub  
> > > <  
> >   
> > https://github.com/robertramey/safe_numerics/issues/5#issuecomment-244820771  
> >   
> > > ,  
> > > or mute the thread  
> > > <  
> >   
> > https://github.com/notifications/unsubscribe-auth/AB4R3K8ZG_mR8veLJUBK92NwBmucVeliks5qnKQBgaJpZM4CH-iq  
> >   
> > > .  
> >   
> > —  
> > You are receiving this because you commented.  
> > Reply to this email directly, view it on GitHub  
> >   
> > https://github.com/robertramey/safe_numerics/issues/5#issuecomment-245826687,  
> > or mute the thread  
> >   
> > https://github.com/notifications/unsubscribe-auth/AAszN4JEVt4xN--M7uB1ePuFDhOqmm-fks5qoPX9gaJpZM4CH-iq  
> > .  
>   
> —  
> You are receiving this because you modified the open/close state.  
> Reply to this email directly, view it on GitHub   
> https://github.com/robertramey/safe_numerics/issues/5#issuecomment-245971374,   
> or mute the thread   
> https://github.com/notifications/unsubscribe-auth/AB4R3LptGYQvpGwdEv3EEpnMW9hwt29nks5qoY5bgaJpZM4CH-iq.  
  
##   
  
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 8

> Username: robertramey  
> Created at: 2016-09-10 18:06:41 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/5#issuecomment-246126463  

OK - fixed, tested and checked in.  
  
Thanks again.  See at CPPCon  
  
Robert Ramey  
  
On 9/9/16 9:04 PM, Robert Ramey wrote:  
  
> On 9/9/16 9:50 AM, John McFarlane wrote:  
>   
> > Maybe lowest instead of min?  
> > Right - good call. I had to spelunk through CPP reference to find   
> > this.  Who would guess this?  
>   
> ! I'll fix this this weekend,  
>   
> Robert Ramey  
>   
> > On Thu, Sep 8, 2016, 11:00 PM Robert Ramey notifications@github.com   
> > wrote:  
> >   
> > > On 9/5/16 4:21 PM, John McFarlane wrote:  
> > >   
> > > > A different case I found today does not trap at compile time:  
> > > >   
> > > > auto s = boost::numeric::safe<int8_t>{0};  
> > > > auto f = static_cast<float>(s);  
> > > >   
> > > > compiles OK but seems to throw with the following output (Ubuntu   
> > > > 16.06 /  
> > > > clang 3.8):  
> > > >   
> > > > unknown file: Failure  
> > > > C++ exception with description "Value out of range for this safe  
> > > > type" thrown in the test body.Signal: SIGSEGV (Segmentation fault)  
> > > >   
> > > > Without floating-point conversion, there are many useful   
> > > > operations in  
> > > > fixed_point that won't work. (Sorry to get to this so late but  
> > > > re-writing my arithmetic operators took far longer than I had hoped.)  
> > >   
> > > I looked into this and found the problem. The following code   
> > > illustrates it  
> > >   
> > > float x = 0.0f;  
> > > float y = std::numeric_limits<float>::min();  
> > >   
> > > the debugger displays:  
> > >   
> > > x float 0 0  
> > > y float 0.0000000000000000000000000000000000000117549435  
> > > 0.0000000000000000000000000000000000000117549435  
> > >   
> > > so numeric_limits<float is not the value I expect> here.  
> > >   
> > > I'm using a recent version of clang with apple xcode.  
> > >   
> > > > —  
> > > > You are receiving this because you modified the open/close state.  
> > > > Reply to this email directly, view it on GitHub  
> > > > <  
> > >   
> > > https://github.com/robertramey/safe_numerics/issues/5#issuecomment-244820771  
> > >   
> > > > ,  
> > > > or mute the thread  
> > > > <  
> > >   
> > > https://github.com/notifications/unsubscribe-auth/AB4R3K8ZG_mR8veLJUBK92NwBmucVeliks5qnKQBgaJpZM4CH-iq  
> > >   
> > > > .  
> > >   
> > > —  
> > > You are receiving this because you commented.  
> > > Reply to this email directly, view it on GitHub  
> > >   
> > > https://github.com/robertramey/safe_numerics/issues/5#issuecomment-245826687,  
> > > or mute the thread  
> > >   
> > > https://github.com/notifications/unsubscribe-auth/AAszN4JEVt4xN--M7uB1ePuFDhOqmm-fks5qoPX9gaJpZM4CH-iq  
> > > .  
> >   
> > —  
> > You are receiving this because you modified the open/close state.  
> > Reply to this email directly, view it on GitHub   
> > https://github.com/robertramey/safe_numerics/issues/5#issuecomment-245971374,   
> > or mute the thread   
> > https://github.com/notifications/unsubscribe-auth/AB4R3LptGYQvpGwdEv3EEpnMW9hwt29nks5qoY5bgaJpZM4CH-iq.  
>   
> ##   
>   
> Robert Ramey  
> www.rrsd.com  
> (805)569-3793  
  
##   
  
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 9

> Username: robertramey  
> Created at: 2018-08-10 22:37:26 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/5#issuecomment-412224172  

I believe that I've addressed all issues raised here.  Let me know if I failed to get it right.  Thanks for all your help.
