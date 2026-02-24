# #33 - subtraction of dimensionless quantities produce surprising result [Open]

> Username: correaa  
> Created at: 2018-10-05 07:07:45 UTC  
> Updated at: 2018-10-05 14:25:32 UTC  
> Url: https://github.com/boostorg/units/issues/33  

In a generic environment one needs to write generic function involving integer integrals.  
```  
template<class A, class B> auto f(A a, B b){return 1 - a/b;}  
```  
Feeding `f(1.,2.)` gives `0.5`.  
  
However if one feeds `f(1.*si::meter, 2.*si::meter)` insted one gets `1` and of type `int`.   
  
This is probably because the dimensionless quantities are implicitly convertible to their value type, which is a good feature. But for some reason in this case this `double` is converted to an `int` before doing the subtraction. The second implicit conversion in seems to be very strong in the language and I don't see a way to solve this in the library (please don't make the conversion `explicit`! that creates many other problems.).  
  
The only solution I found was to define a family of functions specialized for `int`.   
  
```  
namespace boost{  
namespace units{  
template<class S, class Y>  
inline  
quantity<BOOST_UNITS_DIMENSIONLESS_UNIT(S), Y>  
operator-(int i,  
    const quantity<BOOST_UNITS_DIMENSIONLESS_UNIT(S), Y>& q2)  
{  
    typedef quantity<BOOST_UNITS_DIMENSIONLESS_UNIT(S),Y> quantity_type;  
    return quantity_type::from_value(i - q2.value());  
}  
}}  
```  
  
The good news is that this is in principle only needed for int, the bad news is that one need to implement many functions, `(int - dimlessQ)`, `(dimlessQ - int)`, `(int + dimlessQ)`, `(dimlessQ + int)`, `(int * dimlessQ)`, `(dimlessQ * int)`, `(int / dimlessQ)`, `(dimlessQ / int)`.   
  
I would say this is a bug, but that can be controversial. What it is not controversial is that it is surpring because even for totally dimensinless arguments it will not behave like a `double`.  
  
Is there a workaround solution?

---

## Comment 1

> Username: correaa  
> Created at: 2018-10-05 07:29:08 UTC  
> Url: https://github.com/boostorg/units/issues/33#issuecomment-427272051  

Digging a little further I realize that the problem is mainly with pre-subtraction and pre-addition `int - dimlessQ` and `int + dimlessQ`. At the moment, multiplication, division and post-addition and post-substraction gives correct result. However there still a glitch that is that `dimlessQ + int` and `dimlessQ - int` return a double (or the underlying type of `dimlessQ`) instead of `dimlessQ`, which I expect is the expected result.  
  
So , in summary I think this is resolved by adding these operation to the boost units namespace:  
(Perhaps the only extra case is for `unsigned int`, `long`, `unsigned long`, etc)  
  
```  
namespace boost{  
namespace units{  
#if 1  
template<class S, class Y>  
inline  
quantity<BOOST_UNITS_DIMENSIONLESS_UNIT(S), Y>  
operator-(int i,  
    const quantity<BOOST_UNITS_DIMENSIONLESS_UNIT(S), Y>& q2)  
{  
    typedef quantity<BOOST_UNITS_DIMENSIONLESS_UNIT(S),Y> quantity_type;  
    return quantity_type::from_value(i - q2.value());  
}  
template<class S, class Y>  
inline  
quantity<BOOST_UNITS_DIMENSIONLESS_UNIT(S), Y>  
operator-(  
    const quantity<BOOST_UNITS_DIMENSIONLESS_UNIT(S), Y>& q1, int i)  
{  
    typedef quantity<BOOST_UNITS_DIMENSIONLESS_UNIT(S),Y> quantity_type;  
    return quantity_type::from_value(q1.value() - i);  
}  
template<class S, class Y>  
inline  
quantity<BOOST_UNITS_DIMENSIONLESS_UNIT(S), Y>  
operator+(int i,  
    const quantity<BOOST_UNITS_DIMENSIONLESS_UNIT(S), Y>& q2)  
{  
    typedef quantity<BOOST_UNITS_DIMENSIONLESS_UNIT(S),Y> quantity_type;  
    return quantity_type::from_value(i + q2.value());  
}  
template<class S, class Y>  
inline  
quantity<BOOST_UNITS_DIMENSIONLESS_UNIT(S), Y>  
operator+(  
    const quantity<BOOST_UNITS_DIMENSIONLESS_UNIT(S), Y>& q1, int i)  
{  
    typedef quantity<BOOST_UNITS_DIMENSIONLESS_UNIT(S),Y> quantity_type;  
    return quantity_type::from_value(q1.value() + i);  
}  
#endif  
}}  
```

---

## Comment 2

> Username: swatanabe  
> Created at: 2018-10-05 14:25:32 UTC  
> Url: https://github.com/boostorg/units/issues/33#issuecomment-427384022  

AMDG  
  
On 10/05/2018 01:07 AM, Alfredo Correa wrote:  
> In a generic environment one needs to write generic function involving integer integrals.  
> ```  
> template<class A, class B> auto f(A a, B b){return 1 - a/b;}  
> ```  
> Feeding `f(1.,2.)` gives `0.5`.  
>   
> However if one feeds `f(1.*si::meter, 2.*si::meter)` insted one gets `1` and of type `int`.   
>   
  
I can't reproduce this with VS2017.  What compiler are you using?  
  
In Christ,  
Steven Watanabe
