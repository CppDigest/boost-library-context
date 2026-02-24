# #13 - Cannot initialize safe<unsigned> from floating-point [Closed]

> Username: johnmcfarlane  
> Created at: 2016-09-05 06:26:59 UTC  
> Updated at: 2018-09-23 22:26:35 UTC  
> Closed at: 2018-08-10 22:37:49 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/13  

The following compiles:  
  
``` c++  
auto a = boost::numeric::safe<int>{0.};  
```  
  
But this doesn't:  
  
``` c++  
auto b = boost::numeric::safe<unsigned>{0.};  
```  
  
This happens under Clang 3.8.0 and GCC 5.4.0. The GCC error is:  
  
```  
In file included from /usr/include/c++/5/bits/move.h:57:0,  
from /usr/include/c++/5/bits/stl_pair.h:59,  
from /usr/include/c++/5/utility:70,  
from /usr/include/c++/5/tuple:38,  
from /home/john/fixed_point/include/sg14/type_traits.h:17,  
from /home/john/fixed_point/include/sg14/auxiliary/safe_integer.h:13,  
from /home/john/fixed_point/src/test/fixed_point_safe_int.cpp:7:  
/usr/include/c++/5/type_traits: In instantiation of ‘struct std::make_unsigned<double>’:  
.../safe_numerics/include/safe_compare.hpp:72:52:   required from ‘static constexpr bool boost::numeric::safe_compare::detail::less_than<true, false>::invoke(const T&, const U&) [with T = double; U = unsigned int]’  
.../safe_numerics/include/safe_compare.hpp:86:23:   required from ‘constexpr bool boost::numeric::safe_compare::less_than(const T&, const U&) [with T = double; U = unsigned int]’  
.../safe_numerics/include/interval.hpp:359:32:   required from ‘constexpr boost::logic::tribool boost::numeric::operator<(const boost::numeric::interval<R>&, const boost::numeric::interval<T>&) [with T = double; U = unsigned int]’  
.../safe_numerics/include/safe_base_operations.hpp:82:34:   required from ‘constexpr Stored boost::numeric::safe_base<Stored, Min, Max, P, E>::validated_cast(const T&) const [with T = double; Stored = unsigned int; Stored Min = 0u; Stored Max = 4294967295u; P = boost::numeric::native; E = boost::numeric::throw_exception]’  
.../safe_numerics/include/safe_base_operations.hpp:120:23:   required from ‘constexpr boost::numeric::safe_base<Stored, Min, Max, P, E>::safe_base(const T&) [with T = double; Stored = unsigned int; Stored Min = 0u; Stored Max = 4294967295u; P = boost::numeric::native; E = boost::numeric::throw_exception]’  
/home/john/fixed_point/src/test/fixed_point_safe_int.cpp:10:43:   required from here  
/usr/include/c++/5/type_traits:1757:62: error: invalid use of incomplete type ‘class std::__make_unsigned_selector<double, false, false>’  
{ typedef typename __make_unsigned_selector<_Tp>::__type type; };  
                                                              ^  
```

---

## Comment 1

> Username: johnmcfarlane  
> Created at: 2016-09-18 15:37:10 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/13#issuecomment-247854708  

With latest changes, `auto a = boost::numeric::safe<int>{0.};` also now throws. And I think the exception message is wrong:  
  
> conversion of integer to float loses precision

---

## Comment 2

> Username: robertramey  
> Created at: 2016-09-18 18:11:35 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/13#issuecomment-247863533  

well certainly the error message is wrong.  
  
actually I think this should fail at compile time .... But then it would   
conflict with the "drop-in replacement" requirement so I'll have to ask   
about this.  
  
The really correct way to deal with this would be to use  
  
auto = a = boost::numeric::safe<int>{std::round(0.));  
  
anyway - I'll consider this.  
  
btw - I hate auto.  
  
Robert Ramey  
  
On 9/18/16 8:37 AM, John McFarlane wrote:  
  
> With latest changes, |auto a = boost::numeric::safe<int>{0.};| also   
> now throws. And I think the exception message is wrong:  
>   
> ```  
> conversion of integer to float loses precision  
> ```  
>   
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub   
> https://github.com/robertramey/safe_numerics/issues/13#issuecomment-247854708,   
> or mute the thread   
> https://github.com/notifications/unsubscribe-auth/AB4R3MuJB9fI2Ba-ZzLSdqGXZjYeNNsaks5qrVqmgaJpZM4J0xgb.  
  
##   
  
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 3

> Username: johnmcfarlane  
> Created at: 2016-09-19 00:05:20 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/13#issuecomment-247892796  

Surely an implicit conversion should fail at compile time and an explicit one should perform a range check at run-time:  
  
``` c++  
boost::numeric::safe<unsigned> a = -1.0;  // compiler error: implicit conversion  
boost::numeric::safe<unsigned> b {-1.0};  // throws exception  
```

---

## Comment 4

> Username: robertramey  
> Created at: 2016-09-19 00:43:47 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/13#issuecomment-247894750  

On 9/18/16 5:05 PM, John McFarlane wrote:  
  
> Surely an implicit conversion should fail at compile time and an   
> explicit one should perform a range check at run-time:  
>   
> boost::numeric::safe<unsigned> a = -1.0;// compiler error: implicit conversio  
> boost::numeric::safe<unsigned> b {-1.0};// throws exception  
> these should both fail at compile time.  This should be possible using   
> the constexpr magic.  
  
float x = -1.0  
boost::numeric::safe<unsigned> a = x; // compiler error: implicit conversio  
boost::numeric::safe<unsigned> b {x}; // throws exception  
  
these should both fail compile time - I'm still thinking about it.  
  
To me assigning a float to a int is an error because it changes the value.  
  
The right way to convert a float to an integer type is to use lround which  
explicit while all variations of asignment/copy are implicit changes.  
  
I'm still undecided on this now though  
  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub   
> https://github.com/robertramey/safe_numerics/issues/13#issuecomment-247892796,   
> or mute the thread   
> https://github.com/notifications/unsubscribe-auth/AB4R3MA4FDCVK7Vxrobp1WjexnFgf0wIks5qrdHAgaJpZM4J0xgb.  
  
##   
  
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 5

> Username: robertramey  
> Created at: 2018-08-10 22:37:45 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/13#issuecomment-412224225  

I believe that I've addressed all issues raised here.  Let me know if I failed to get it right.  Thanks for all your help.
