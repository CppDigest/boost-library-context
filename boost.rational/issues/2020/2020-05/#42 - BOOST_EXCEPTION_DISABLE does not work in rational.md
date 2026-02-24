# #42 - BOOST_EXCEPTION_DISABLE does not work in rational [Open]

> Username: tower120  
> Created at: 2020-05-06 15:06:31 UTC  
> Updated at: 2020-05-06 15:06:31 UTC  
> Url: https://github.com/boostorg/rational/issues/42  

`BOOST_EXCEPTION_DISABLE` have no effect with rational lib. Due to:   
  
rational.hpp  
```cpp  
// Input and output  
template <typename IntType>  
std::istream& operator>> (std::istream& is, rational<IntType>& r)  
{  
    using std::ios;  
  
    IntType n = IntType(0), d = IntType(1);  
    char c = 0;  
    detail::resetter sentry(is);  
  
    if ( is >> n )  
    {  
        if ( is.get(c) )  
        {  
            if ( c == '/' )  
            {  
                if ( is >> std::noskipws >> d )  
                    try {  
                        r.assign( n, d );  
                    } catch ( bad_rational & ) {        // normalization fail  
                        try { is.setstate(ios::failbit); }  
                        catch ( ... ) {}  // don't throw ios_base::failure...  
                        if ( is.exceptions() & ios::failbit )  
                            throw;   // ...but the original exception instead  
                        // ELSE: suppress the exception, use just error flags  
                    }  
            }  
            else  
                is.setstate( ios::failbit );  
        }  
    }  
  
    return is;  
}  
```
