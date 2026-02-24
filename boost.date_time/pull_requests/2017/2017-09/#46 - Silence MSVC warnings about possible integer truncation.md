# #46 Silence MSVC warnings about possible integer truncation. [Merged]

> Username: Lastique  
> Created at: 2017-09-05 18:36:43 UTC  
> Updated at: 2017-10-24 21:16:57 UTC  
> Merged at: 2017-09-05 22:06:54 UTC  
> Closed at: 2017-09-05 22:06:54 UTC  
> Url: https://github.com/boostorg/date_time/pull/46  

The warnings are triggered by Boost.Thread tests.

---

## Comment 1

> Username: mclow  
> Created_at: 2017-09-06 14:53:12 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-327509233  

I'm not really convinced that this is the correct way to deal with this.  I'll be looking at this this weekend.  Maybe `add` and `subtract` should return `int_type`, rather than the type passed in.

---

## Comment 2

> Username: mclow  
> Created_at: 2017-09-06 15:32:54 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-327522411  

Looking a bit more closely, I suspect that the local variable `year` is the wrong type.

---

## Comment 3

> Username: Lastique  
> Created_at: 2017-09-06 15:52:58 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-327528704  

My understanding is that `add`/`subtract` should return a type that is at least as large as their argument because the return value is the overflow count, which can potentially be as large as the argument.  
  
The type of `year` kind of makes sense, too, given that this type defines the range of years supported by the library (i.e. you eventually want to pass this value as a year to the date constructor). So I wouldn't readily change that either.  
  
I think the issue is caused by the fact that the `f_` type, which is the number of months to add to a date, is `int`, which potentially allows to specify an offset beyond the supported years range (i.e. `INT_MAX` > `SHRT_MAX * 12`). I guess, we could change `f_` type to a `wrapping_int` of the range [`SHRT_MIN * 12`; `SHRT_MAX * 12`], but I'm not sure if that will be useful. But you won't protect yourself from overflows that way because there always exists a combination of a date and offset that will cause an overflow.  
  
I think a more viable approach here would be to detect overflows and produce a date with an infinity special value. But that can be considered as a breaking change.

---

## Comment 4

> Username: mclow  
> Created_at: 2017-09-06 15:53:02 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-327528728  

@Lastique, can you replace lines 77-78 with the following code:  
`const int year = wi.add(f_) + ymd.year;`  
  
and let me know if you get any warnings (or test failures)?  
Thanks.

---

## Comment 5

> Username: mclow  
> Created_at: 2017-09-06 15:58:38 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-327530535  

> The type of year kind of makes sense, too, given that this type defines the range of years supported by the library (i.e. you eventually want to pass this value as a year to the date constructor). So I wouldn't readily change that either.  
  
I could argue that the type of year should be whatever `ymd_type` takes in its' constructor.  
However, I think that we should just do the calculation with the types we have, and let the constructor deal with the "out of range" cases. The underlying date type knows its' valid range; this function shouldn't be trying to figure that out.

---

## Comment 6

> Username: Lastique  
> Created_at: 2017-09-06 16:12:51 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-327534870  

> I think that we should just do the calculation with the types we have, and let the constructor deal with the "out of range" cases. The underlying date type knows its' valid range; this function shouldn't be trying to figure that out.  
  
`date_type` constructor does not deal with "out of range" cases, it simply accepts `year_type` as the first argument (see date_time/date.hpp:71). So by passing `year` of type `int` you will just make the compiler perform an implicit cast (and, likely, a warning about possible value truncation), so you don't really solve the problem.  
  
I can agree that `month_functor` should probably rely more on the types defined by the date type used instead of `int` and `short`, but I think this code is the place that should define the behavior on overflow, if we want it to be defined. I'm not sure what is the library-wide policy wrt. overflows - are they defined and if yes, what is the outcome? Because, surely, this is not the only place in the library where date/time/duration can overflow.

---

## Comment 7

> Username: mclow  
> Created_at: 2017-09-06 16:28:46 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-327539407  

Looking at the gregorian stuff, the `year_type` is `greg_year` which is a `constrained_value`, which is set up to throw on out of range.  
See greg_year.hpp

---

## Comment 8

> Username: Lastique  
> Created_at: 2017-09-06 16:35:10 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-327541256  

Yes, but `greg_year` constructor accepts `unsigned short`. So an `int` can get truncated to an `unsigned short` that satisfies the range check.

---

## Comment 9

> Username: mclow  
> Created_at: 2017-09-06 16:37:53 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-327541971  

This works for me, too - but I'm not convinced that MSVC won't whine:  
  
    typedef typename ymd_type::year_type  year_type;  
    const year_type year = ymd.year + wi.add(f_);

---

## Comment 10

> Username: Lastique  
> Created_at: 2017-09-06 18:35:24 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-327574970  

I've created https://github.com/boostorg/date_time/pull/47 with what I think should be the way to handle overflows. Please, have a look at that PR and let me know if that approach is acceptable. It turned out a bit messy and certainly more complicated than just silencing the warning.

---

## Comment 11

> Username: mclow  
> Created_at: 2017-09-06 18:44:30 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-327577479  

Consider this code:  
  
	#include <boost/config.hpp>  
	#include <boost/date_time/gregorian/greg_year.hpp>  
  
	#include <iostream>  
  
	int main()  
	{  
		boost::gregorian::greg_year y(9990U);  
		std::cout << (unsigned short) y << '\n';  
	  
		for ( int i = 0; i < 20; ++i ) {  
			boost::gregorian::greg_year y2 = y + i;  
			std::cout << (unsigned short) y2 << '\n';  
			}  
	}  
  
It prints:  
  
	9990  
	9990  
	9991  
	9992  
	9993  
	9994  
	9995  
	9996  
	9997  
	9998  
	9999  
	10000  
	libc++abi.dylib: terminating with uncaught exception of type boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::gregorian::bad_year> >: Year is out of valid range: 1400..10000  
	Abort trap: 6

---

## Comment 12

> Username: Lastique  
> Created_at: 2017-09-06 18:51:08 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-327579292  

Does this throw:  
  
    boost::gregorian::greg_year y2 = y + 0x10000001;  
  
or  
  
    boost::gregorian::greg_year y3(0x100007E1);  
  
or  
  
    boost::gregorian::greg_date d1(0x100007E1, 1, 1);  
  
?

---

## Comment 13

> Username: mclow  
> Created_at: 2017-09-06 19:37:13 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-327590895  

No; no; and no.  
Clang warns on the second and third:  
  
    boost.cpp:23:33: warning: implicit conversion from 'int' to 'unsigned short' changes  
          value from 268437473 to 2017 [-Wconstant-conversion]  
  
Sounds like `constrained_value` needs some work (for the first one).

---

## Comment 14

> Username: Lastique  
> Created_at: 2017-09-06 20:08:43 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-327598691  

The warnings are not a proper protection against overflow. These code samples are quite obvious and involve a literal, so the overflow is easy to detect. The problematic code that was triggering warnings involves runtime values, and there is generally no reliable way to predict if the overflow will happen or not. Thus the compiler will either not emit the warning or will emit it unconditionally.  
  
I think that if we really want to define behavior for overflows (in which I'm not totally convinced) then there is a lot of work ahead, including tracking down places like this and adding runtime checks similar to https://github.com/boostorg/date_time/pull/47. It won't be pretty and there will be performance impact. OTOH, we could say that overflows are UB and maybe work towards minimizing the possibility of overflows, like std::chrono did.

---

## Comment 15

> Username: mclow  
> Created_at: 2017-09-06 20:15:15 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-327600288  

I think that #47 not the best approach; the responsibility for dealing with out-of-bounds dates (and the policies for dealing with them) belong in the date (or calendar) class. If someone *knows* (somehow) that all their dates are 'in-range', they shouldn't have to pay for the checks (and can do that by defining a date class that makes no checks (uses raw `int` or `unsigned short` instead of `constrained_value`)

---

## Comment 16

> Username: Lastique  
> Created_at: 2017-09-06 20:16:22 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-327600568  

Ok, makes sense.

---

## Comment 17

> Username: mclow  
> Created_at: 2017-09-06 20:16:49 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-327600678  

That doesn't address the "way out of bounds values being passed to the constructor" - which are then narrowed before the constructor can check them - but I see that as a different (smaller) problem.

---

## Comment 18

> Username: Lastique  
> Created_at: 2017-09-06 20:20:00 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-327601480  

#47 did not attempt to fix the problem in general, including users, but instead it intended to detect and handle overflows created by the library itself. I agree that a more general fix would be better, but it would be much more complex and potentially user-breaking.

---

## Comment 19

> Username: Lastique  
> Created_at: 2017-09-06 20:46:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-327608001  

Also, from a user's perspective, there's not much difference between  
  
    boost::gregorian::greg_date d1(10001, 1, 1);  
  
and  
  
    boost::gregorian::greg_date d2(0x100007E1, 1, 1);  
  
In both cases the year value exceeds the permitted range, so the behavior should be the same - an exception, as documented by the library. That the second case doesn't throw can be considered a bug, regardless of the place where the truncation happens.

---

## Comment 20

> Username: mclow  
> Created_at: 2017-09-06 21:17:04 UTC  
> Updated_at: 2017-09-06 21:17:12 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-327615105  

> That the second case doesn't throw can be considered a bug.  
  
Agreed.

---

## Comment 21

> Username: mclow  
> Created_at: 2017-09-06 21:19:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-327615617  

Of course, we also have a problem with:  
  
    boost::gregorian::greg_date d2(-63519, 1, 1);  
  
thanks to the same set of C conversion rules.

---

## Comment 22

> Username: Lastique  
> Created_at: 2017-09-12 16:53:58 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-328915473  

The test results are green, could this PR be merged to master, please?  
  
Any further improvements can be done later separately. Merging this PR will at least silence the warning until those improvements are implemented.

---

## Comment 23

> Username: mclow  
> Created_at: 2017-09-12 17:07:37 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-328919409  

I'll do that, and then immediately check in my (non-static cast) version. Then we can see if that fixes the problem as well.

---

## Comment 24

> Username: Lastique  
> Created_at: 2017-10-24 20:57:33 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-339128810  

MSVC-14.1 emits warnings now:  
  
```  
C:\src\boost\boost/date_time/adjust_functors.hpp(105): warning C4244: 'argument': conversion from 'int' to 'unsigned short', possible loss of data  
C:\src\boost\boost/date_time/adjust_functors.hpp(93): note: while compiling class template member function 'boost::gregorian::date_duration boost::date_time::month_functor<boost::gregorian::greg_durations_config::date_type>::get_neg_offset(const date_type &) const'  
        with  
        [  
            date_type=boost::gregorian::greg_durations_config::date_type  
        ]  
C:\src\boost\boost/date_time/date_duration_types.hpp(184): note: see reference to function template instantiation 'boost::gregorian::date_duration boost::date_time::month_functor<boost::gregorian::greg_durations_config::date_type>::get_neg_offset(const date_type &) const' being compiled  
        with  
        [  
            date_type=boost::gregorian::greg_durations_config::date_type  
        ]  
C:\src\boost\boost/date_time/date_duration_types.hpp(188): note: see reference to class template instantiation 'boost::date_time::month_functor<boost::gregorian::greg_durations_config::date_type>' being compiled  
C:\src\boost\boost/date_time/date_duration_types.hpp(187): note: while compiling class template member function 'boost::gregorian::date_duration boost::date_time::years_duration<boost::gregorian::greg_durations_config>::get_offset(const boost::gregorian::date &) const'  
C:\src\boost\boost/date_time/posix_time/date_duration_operators.hpp(77): note: see reference to function template instantiation 'boost::gregorian::date_duration boost::date_time::years_duration<boost::gregorian::greg_durations_config>::get_offset(const boost::gregorian::date &) const' being compiled  
C:\src\boost\boost/date_time/posix_time/date_duration_operators.hpp(77): note: see reference to class template instantiation 'boost::date_time::years_duration<boost::gregorian::greg_durations_config>' being compiled  
C:\src\boost\boost/date_time/adjust_functors.hpp(76): warning C4244: 'argument': conversion from 'int' to 'unsigned short', possible loss of data  
C:\src\boost\boost/date_time/adjust_functors.hpp(64): note: while compiling class template member function 'boost::gregorian::date_duration boost::date_time::month_functor<boost::gregorian::greg_durations_config::date_type>::get_offset(const date_type &) const'  
        with  
        [  
            date_type=boost::gregorian::greg_durations_config::date_type  
        ]  
C:\src\boost\boost/date_time/date_duration_types.hpp(189): note: see reference to function template instantiation 'boost::gregorian::date_duration boost::date_time::month_functor<boost::gregorian::greg_durations_config::date_type>::get_offset(const date_type &) const' being compiled  
        with  
        [  
            date_type=boost::gregorian::greg_durations_config::date_type  
        ]  
```

---

## Comment 25

> Username: Lastique  
> Created_at: 2017-10-24 21:16:57 UTC  
> Url: https://github.com/boostorg/date_time/pull/46#issuecomment-339134450  

I've created https://github.com/boostorg/date_time/pull/50 to fix this.

---
