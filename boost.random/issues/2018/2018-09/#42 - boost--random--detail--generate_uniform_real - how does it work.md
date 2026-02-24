# #42 - boost::random::detail::generate_uniform_real - how does it work? [Closed]

> Username: drauch  
> Created at: 2018-09-20 19:42:11 UTC  
> Updated at: 2018-09-22 11:10:50 UTC  
> Closed at: 2018-09-22 11:10:50 UTC  
> Url: https://github.com/boostorg/random/issues/42  

I'm currently studying the generate_uniform_real implementation detail on https://github.com/boostorg/random/blob/develop/include/boost/random/uniform_real_distribution.hpp and I have a question about the following lines:  
  
    template<class Engine, class T>  
    T generate_uniform_real(  
        Engine& eng, T min_value, T max_value,  
        boost::mpl::false_  /** is_integral<Engine::result_type> */)  
    {  
        for(;;) {  
            typedef T result_type;  
            // What if eng() generated a value >0 and eng.min is <0, isn't it possible that this results in positive infinity?  
            result_type numerator = static_cast<T>(eng() - (eng.min)());  
            // Again, what if eng.max is >0 and eng.min is <0 (e.g., numeric_limits<float>::max and ::lowest), wouldn't that run into positive infinity?  
            result_type divisor = static_cast<T>((eng.max)() - (eng.min)());  
            [...]  
        }  
    }  
  
Hopefully someone can answer my questions why this isn't a problem for the uniform distribution.

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-09-20 20:00:52 UTC  
> Url: https://github.com/boostorg/random/issues/42#issuecomment-423313697  

AMDG  
  
On 09/20/2018 01:42 PM, drauch wrote:  
> I'm currently studying the generate_uniform_real implementation detail on https://github.com/boostorg/random/blob/develop/include/boost/random/uniform_real_distribution.hpp and I have a question about the following lines:  
>   
>     template<class Engine, class T>  
>     T generate_uniform_real(  
>         Engine& eng, T min_value, T max_value,  
>         boost::mpl::false_  /** is_integral<Engine::result_type> */)  
>     {  
>         for(;;) {  
>             typedef T result_type;  
>             // What if eng() generated a value >0 and eng.min is <0, isn't it possible that this results in positive infinity?  
  
  While it's technically possible for this to happen without  
violating the documented constraints, the values produced  
by eng() are required to be uniformly distributed.  Using  
the full range of a floating point type isn't really  
possible.  The floating point generators are somewhat  
underspecified, but all the floating point generators in  
Boost.Random are [0,1).  The standard does not have floating  
point engines, and I would discourage their use, as they  
have a couple of other issues that I've never found a  
good solution for.  
  
>             result_type numerator = static_cast<T>(eng() - (eng.min)());  
>             // Again, what if eng.max is >0 and eng.min is <0 (e.g., numeric_limits<float>::max and min), wouldn't that run into positive infinity?  
>             result_type divisor = static_cast<T>((eng.max)() - (eng.min)());  
>             [...]  
>         }  
>     }  
>   
> Hopefully someone can answer my questions why this isn't a problem for the uniform distribution.  
>   
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: drauch  
> Created at: 2018-09-20 20:23:39 UTC  
> Updated at: 2018-09-20 20:23:47 UTC  
> Url: https://github.com/boostorg/random/issues/42#issuecomment-423320573  

Thank you for the quick response.  
  
> Using the full range of a floating point type isn't really possible.  
  
Why so? You will most certainly create lots of E+38 values and almost never an E+1 value, but it is still possible to provide uniformly distributed values, isn't it?

---

## Comment 3

> Username: swatanabe  
> Created at: 2018-09-20 20:42:08 UTC  
> Url: https://github.com/boostorg/random/issues/42#issuecomment-423326155  

AMDG  
  
On 09/20/2018 02:23 PM, drauch wrote:  
> Thank you for the quick response.  
>   
>> Using the full range of a floating point type isn't really possible.  
> Why so? You will most certainly create lots of E+38 values and almost never an E+1 value, but it is still possible to provide uniformly distributed values, isn't it?  
>   
  
Okay, it's possible, but it serves no purpose.  
Filling the entire mantissa makes sense, but using  
the whole exponent does nothing for you.  Scaling  
the exponent up or down is irrelevant as far as  
a random engine is concerned.  I have never heard  
of any algorithm that would do this.  
  
In Christ,  
Steven Watanabe
