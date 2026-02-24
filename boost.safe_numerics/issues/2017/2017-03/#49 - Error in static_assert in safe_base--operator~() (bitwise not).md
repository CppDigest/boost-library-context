# #49 - Error in static_assert in safe_base::operator~() (bitwise not) [Closed]

> Username: nmusolino  
> Created at: 2017-03-23 02:02:55 UTC  
> Updated at: 2018-09-23 22:26:36 UTC  
> Closed at: 2018-08-10 20:45:07 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/49  

[This static_assert](https://github.com/robertramey/safe_numerics/blob/master/include/safe_base.hpp#L261) is missing a `!`.  The intent of the static assert is to confirm the Stored type is unsigned.  
  
    constexpr safe_base operator~() const {  
        static_assert(  
            std::numeric_limits<Stored>::is_signed,  
            "Bitwise inversion of signed value is an error"  
        );  
        return ~(m_t);  
    }  
  
I actually was going to submit a pull request but got surprising runtime behavior.  I will detail this in a separate issue.

---

## Comment 1

> Username: robertramey  
> Created at: 2018-08-10 20:45:07 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/49#issuecomment-412201002  

I think I've addressed this.
