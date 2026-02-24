# #380 - Improve numerical accuracy of circular axis [Open]

> Username: HDembinski  
> Created at: 2022-12-24 13:50:02 UTC  
> Updated at: 2023-01-12 11:00:19 UTC  
> Url: https://github.com/boostorg/histogram/issues/380  

The `if (options_type::test(option::circular))` inside `index_type index(value_type x)` and `value_type value(real_index_type i)` reminds me of when I was dealing with periodic argument reduction in a simulator+monitor program (every ms PC sends sin(A*t+B) and receives actual value to&from a chip through UDP, t can be as large as several days). There should be some high precision functions in boost.core/utility/utilities/tool/tools/toolbox/... and we just need to find them.  
https://en.cppreference.com/w/cpp/numeric/lerp  
https://stackoverflow.com/questions/64058564/single-precision-argument-reduction-for-trigonometric-functions-in-c  
```C++  
{  
    // using A = axis::variable<double, axis::null_type, op::circular_t>; // using double solves the errors  
    using A = axis::variable<float, axis::null_type, op::circular_t>;  
    auto a = A({  
      1.,  
      1e+8,  
      2e+8,  
    });  
    BOOST_TEST_EQ(a.index(1e8), 1);  
    BOOST_TEST_EQ(a.index(2e8), 0); // test 'a.index(2e8) == 0' ('-1' == '0') failed  
    BOOST_TEST_EQ(a.index(4e8), 0); // test 'a.index(4e8) == 0' ('-1' == '0') failed  
}  
{  
    // using A = axis::variable<double, axis::null_type, op::circular_t>; // using double solves the errors  
    using A = axis::variable<float, axis::null_type, op::circular_t>;  
    auto a = A({  
      -2e+8,  
      -1e+8,  
      -1.,  
    });  
    BOOST_TEST_EQ(a.index(-1e8), 1);  
    BOOST_TEST_EQ(a.index(-2e8), 0);  
    BOOST_TEST_EQ(a.index(-4e8), 1); // test 'a.index(-4e8) == 1' ('0' == '1') failed  
}  
return boost::report_errors();  
```  
  
_Originally posted by @jhcarl0814 in https://github.com/boostorg/histogram/issues/372#issuecomment-1364386000_

---

## Comment 1

> Username: jhcarl0814  
> Created at: 2022-12-27 08:17:15 UTC  
> Url: https://github.com/boostorg/histogram/issues/380#issuecomment-1365702640  

The implementation below only ensures correctness (not considering inf and nan), can be used to verify more efficient solutions (if there are any) in the future.  
  
[turn a floating point into an integer of `/* the leading 1 is implicit */ 1 + matissa + (std::numeric_limits<T>::max_exponent - std::numeric_limits<T>::min_exponent)` = `1 + (std::numeric_limits<T>::digits - 1) + (std::numeric_limits<T>::max_exponent - std::numeric_limits<T>::min_exponent)` bits (--std=gnu++14 -O2 -Wall -pedantic -pthread -lquadmath) (`-std=gnu++14` and `-lquadmath` are only for `boost::multiprecision::float128` demonstration)](https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGe1wAyeAyYAHI%2BAEaYxCAAnFykAA6oCoRODB7evnrJqY4CQSHhLFEx8baY9vkMQgRMxASZPn4JdpgO6bX1BIVhkdFxrXUNTdlD3b3FpYMAlLaoXsTI7BzmAMzByN5YJmtutHgshAq72CYaAILrm9uYu25OCgTEmKyn51dmGwxbXjt7ThYhjwiXel2uP1u9wAbu0iMQwZ9vr9/m4IqgUgQAPRoF5YiKEAB0CESoLWZ3BXxufzue3RmKxLC8tEciReyDwqQEWOCBEwwGixNJiIhKNpaIxT0ZzNZ7M56SxVFoqCYBC4ZgAHEKyRSkdTUcggQQECLLnyWIkDHz7gQAJ6JRisTAAagAKoinsQvA5nalgAw2byAPoEZ1YrHOhAEAiJBQgcNPJjIADWqFhxCVqAA7oS0CwsUwsQB2NYl2JmLhYjVrSRrNYAVjWHxMRasl2dHbdzoYuzbF07vrw/sDghDEFdzpYM2dIG7ECnzpbFkXRYAIh8B1RiHhXL6COgQCBMS9WIuzAA2Z2oB3EVUke73CBPA9Hz2vFhny%2BoUjOtAMJ6DsO26jqG5iXgoMwbp2S5QQOHZ4FQzpPoSDCLnszoaNOLwEIsqGoGhbj3GeZgALTmGYBFESRCgob2sFwdhuFXpR6E0T2ax9gOLbruCa50WamAWla4p2g6zBsG6iJMF4RCAQGwEEOOzrQpBvGcZ2jHEKhfrycGBD3O65IQCp/FXHx4ICUJqoifajoSYZuoTvUhAIGwjjIEG9BUAQQYKAgCGKRO0LkIIzqsAsgiqWZ6nwVQxn3Jh9Ewe2cGxRA4VeIICVRalqWaahJFQCRJnkqcJEZZFpm5c6lRKPR1X5c6hUQMVkF7PcFUEG1MUrjx/adrVdwpR2yX9alCHpSwEX6XsiXDdVzqNdC7zkuVU2ZTNPUDoN9W5UtD57J1VXQeZZl9R85qWtZNq2eJLoOR8Tnbsabl4B527AFGvn%2Bd5SnBbyYXrZVan0RNy2zTlJ1bZ2E1HRDu15ZgOFaU1RUlYRexrdN3UIzVtB1fNDVI0xzWtSt2CdTj83cfRO3U62uOw0DM1uHNY0Lft7WY3D0MdnT7NE8jqHg2c5I80lp00xZFyXcJN1iU6kmlZcXipEYzq8vy0RBlmJDJsEwC%2BfCTACiGaGrs69JPIeTIsiCcpcn4IAMP0CJ0pKBA2zK9vtPKzgoKSQbBhESbJq4D7zRAXBnsuT77oeLtsNuHkHEcBAnHsDmHvgwDHE1zpcNO1hIc%2BCc%2BNEb2eYcxwGacNtMKoQaYKoyQhKFJF7i%2BicVyn1fp7X5I28ETctwIjBdXM9FRzHJfx875fJ1XacZ24WcgDnecd4XM9x13C%2BV6nNeZ3XIBAo3zet%2BP%2Bel/PScH33K9r0cDAj5fkWT/NVue6f3tsr7juHh0pgdAQYgT%2BkIDSH8X8vZ2z/hyABKAEDtDDugH80JUB4HQKcNCos%2BwXUElda0exRJ2XuoidBmDnR8ieBAKKo0ByJnckGJgCglANF3mXO%2Bvdl4D2wIeW8%2BBVC7HXGsVcZhSDkXIlTdmN80AyRYm4YiqQABemBUBUFnORBRndDxINUJmVU2iVFqLiq6IuXNFFgFWPWNwDArHHQ7LIhYoELHEQ3unTRZgzAHUUTfPRBiXEY18XPbui9D792PoPdeQ484%2BOdPYmxdjVgcXok4%2BRcTyJYAYCQFgoDgieIonEvxzcAlGJCfvbhR9V4nyyTkvJDBaHaISbY%2BxKT5ppMCURciz8CllJfP45UhiinlK4UvKpT9giNLic0pJDidEoGcdo7pw8L5j0EL04Z/SSmDM6axEZPcxkROqVE5%2Br81mhi3k06xLTkk9Q6UsrxZ8NmuOKfonZfTOEHPCY/E%2BZ8pmuJma0u5c85G7MUd0huZy26e2Ih8kAAyVRgvmaE%2B%2BPDIl8NPpC1Z0L87R2mdc2ZbSZEgsWRkrxOlZzRwJAQH8QJBCcgUEwZ5QTZ571Gd83h2cYnp1xeYllWjqUKB/Ni8ezKiLQIWS8Q81KdaYONHHVUldRBPHuJlIB6ANZZSMpWAAVGFWgtBnSqOIPhHVEZi4cNvl8h%2BnLMXn1HjijuN8UWVKORMl%2BIrQrF23t6rEeqxCGrHs6M104t58qIoC25qSSXpNceRTWApiDOl1sQfW6sngkBNuwWFcTp4WudRUw5PyonuIULyneBb2U2vRfXe1b8LnIsLRymtp8VkOvHjMcN6EBU1y8T4yNcyOn9oJUCpK7S56wgcPedFzoDgqtEUuQqZiNA/movssJ1bjkYtqcQXJz9aGrsrdatFW6h4NLmKjMxCRL0zC4Gutlx7xkn0wLGOgAgD03q4CupqR6N0nqfg3A9uNxwzG/b%2B1FT6S2MDqfui94HXXFoxbBn8IHr2oesPBottqX2pGVOelDV6wProg2635gHIJ8SJZuEgf0QBzq6hLXm8zQUKNeQEnx0IFEDqJbjVWBt5nKmABEOZA4%2BPprngoUQtAIjsSYwDF0wikKceEYpzCaEABiGEZxIV5DMCAgmIjGU7cKkMBDzY1RE52CcH5FOSbEDJ4yq7MDdTDOa%2BsFhr1mEhqlIdFjulphdLJEw7nPMzDFXsNjOyfEsB8VoiAwbnTePrGcPtFib5YFMGlkJKQEDZhDnVVx4oHxePMyQGJDBIIleHcFm5plcYujIqI1lny/2Qe3dystYbLMdhs01uz0mGksBQ5h5tp7om5x5WGlz4ZNV8kTYzQQWtiA6z1gbI2mbTYswchrFg85pHVV80E8iyAECGAFJOQwjhWFMCofhBNAwc1Za2W8xF0XYslfi4pyRT3dHNx8TfPyeWWFFYi0OXSggICHDakdrxlXvF%2BY%2BwlpLKX4dBPS%2B0f72XAdZnyyDxRCmEeFKa2V3OFXvtBO43gwmA0Y6KctS6rDLbTmeobYXbrO3hHORekjSuH0vp%2BQCpDob9Om2bvdVCq%2BU32eHeK2YE7Z2XQs9u92AQJEQjAEVbCcLbhXmY5fNj3H/2wcjkUlD97FF4t6uR%2BT%2B46PMto6x7lnHwO4l460YpknwQ4fVcSaO6W1UFOWEUyRTApmLTs8OJz56rkefvSHPzn6puhsh7D4kfbuUZftS8fLowiv22hVki1Zn%2Bf9KWDpVdxlYXHsO%2Be3rwBTvDdpeNwpIX0PZdISR8FlHdf14Y5%2B87HLQOCssrd1V4nH0vc272JT5s1PHExpZvcGf9NzqS0uADIEky6EM3mtQlm7GjLp47Hv%2B46AFgRHoKcWhcyT/u0xDA2U/90iHgCeqDUV/pFSwuBwOYtBOD1l4D8A4C0FIFQE4EIiD0sF9AWCWEDzWB4FIE9mAJ/zmGTBAHPC4EJEkHPArEkA0FiHLC4HPCLASD/w4EkF4BYBAEkCLEJAwNiA0BoPoMYKLAwNICAJALAI4F4DjBXSQK0DmDgFgCQDzESDoGiHIEoFEPEJiGAAUGYFjFyxpSwGhDekwAADUdwswAB5MSTgBAmgFkaIOMCACITQXgAkZgYgW0fQ3gPMNgQQbQhgWgGw5A0gLAOlYAcQNw/AdkRwWEOMNw5udoGSFYBAzWMgkAg4CIW8awjwLAcwxA7cKg7gH/PgAwOQzQzAHQvQ1ImQQQEQMQdgKQfI%2BQJQNQRI3QUgs7FAawawfQPACIOMWAO6EAL%2BUgdMfQjUHgYza8aoQIkie9YRf0LwOoywKQJqbQiiEiAAdQDSagdHQEuzegWONBPA1RIloAAEcvAmB0AjQEAeDKg4Rn8IBXBRgWhSBAhgg%2BgSgBgEhcg0gBALicgUgniGBJhXY9A2gOgBAugRhPBmhvjjjfiahhgegbiph7jbBwSXjxgGhPi7iYhC55hFhlgJBf9/9ADEiuDnRVANRzwSJzxJBnRgBkBkAkJnhMpkxpwIBcBCASAzx4CZheB%2BC0iIAkBgjkAZISBJD0piA5DlBDBKghAncgCEDpCrR0ghSQhaBRTswOC7DUALQZCQA5CFDAcaVJTohtCZJ5SsxFSrjVB2gLgBTAjeAuTah8AgDeB%2BACipNijpA7SyiVB1A3Cqj9AajTBICbBojmioBWj2jOjuBuiUDQDEh%2BjOBBjnxhiXYxiPMSSSIpimo5iDUFjgFliKSSIIyEB1imptjdj9jVRDjUTYDvj9xggZSRSxTbCkjMAwjeAsxbxEhbDMSOAAD2CcTOBsBjTuT4Q8SCSiSSSySKSIAqSGAaSkIICrBLAfx6T%2Bz1hC5WTzDjMkE9iBgD00C1gtRGwiwNR6wGCNA9y1gzBYh9BOAKDOy3CuCeC9A2TjMyCzBKCJANAV1DSbzECVy5h0wAFJAgA%3D%3D%3D).  
  
the integer = the floating point * 2^`-(std::numeric_limits<T>::min_exponent-1)` * 2^`(std::numeric_limits<T>::digits - 1)` (so the decimal point is moved to the right so it can be an integer)  
  
actual operations used:  
  
1. (similar to `frexp`) temp = the floating point * 2^(an integer necessary to make temp falls in `[1, 2)`) (exponent is changed);  
2. temp = temp * 2^`(std::numeric_limits<T>::digits - 1)`, temp is guaranteed to be an integer now, convert it to an integer (mantissa's decimal point moved to the right);  
3. temp = temp * 2^`-(std::numeric_limits<T>::min_exponent - 1)` (exponent is changed);  
4. temp = temp / 2^(the integer in step1) (exponent is changed) (now exponent (`= original exponent - (std::numeric_limits<T>::min_exponent-1) + (std::numeric_limits<T>::digits - 1)`) is guaranteed to be non-negative);  
  
```C++  
#include<limits>  
#include<iostream>  
#include<iomanip>  
#include<vector>  
#include<boost/core/bit.hpp>  
#include<boost/multiprecision/integer.hpp>  
#include<boost/multiprecision/float128.hpp>  
#include<cmath>  
  
template<typename T>  
struct signprint_t // https://stackoverflow.com/a/7373921/8343353  
{  
    T n;  
    signprint_t(T m) : n(m) { }  
    friend std::ostream & operator<<(std::ostream & o, const signprint_t & s)  
    {  
        if (s.n < 0) return o << "-" << -s.n;  
        return o << s.n;  
    }  
};  
template<typename T>  
auto signprint(T v)  
{  
    return signprint_t<T>(v);  
}  
  
template<typename T>  
T arithmetic_left_shift(T v,int amount)  
{  
    if(v<0)  
    {  
        if(amount<0)  
            return -((-v)>>-amount);  
        else  
            return -((-v)<<amount);  
    }  
    else  
    {  
        if(amount<0)  
            return v>>-amount;  
        else  
            return v<<amount;  
    }  
}  
  
template<typename T>  
T arithmetic_right_shift(T v,int amount)  
{  
    if(v<0)  
    {  
        if(amount<0)  
            return -((-v)<<-amount);  
        else  
            return -((-v)>>amount);  
    }  
    else  
    {  
        if(amount<0)  
            return v<<-amount;  
        else  
            return v>>amount;  
    }  
}  
  
template<typename T>  
using integer_working_storage_t = boost::multiprecision::number<boost::multiprecision::cpp_int_backend<  
    (1 + (std::numeric_limits<T>::digits - 1) + (std::numeric_limits<T>::max_exponent - std::numeric_limits<T>::min_exponent)),  
    (1 + (std::numeric_limits<T>::digits - 1) + (std::numeric_limits<T>::max_exponent - std::numeric_limits<T>::min_exponent)),  
    boost::multiprecision::signed_magnitude, boost::multiprecision::checked, void> >;  
  
template<typename T>  
void test()  
{  
    static_assert(std::numeric_limits<T>::radix==2,"");  
    std::cout << "sizeof: " << std::hexfloat << sizeof(T) << '\n';  
    std::cout << "digits: "<< std::hexfloat << std::numeric_limits<T>::digits << '\n';  
    std::cout << "denorm_min: " << std::hexfloat << std::numeric_limits<T>::denorm_min() << '\n';  
    std::cout << "min: " << std::hexfloat << std::numeric_limits<T>::min() << '\n';  
    std::cout << "min_exponent: " << std::hexfloat << std::numeric_limits<T>::min_exponent - 1 << '\n';  
    std::cout << "max: " << std::hexfloat << std::numeric_limits<T>::max() << '\n';  
    std::cout << "max_exponent: " << std::hexfloat << std::numeric_limits<T>::max_exponent - 1 << '\n';  
    std::cout << "sign: 1 bit, mantissa: " << (std::numeric_limits<T>::digits - 1) << " bits, exponent: " << boost::core::bit_width(static_cast<unsigned int>(1/* all zero */ + (std::numeric_limits<T>::max_exponent - std::numeric_limits<T>::min_exponent + 1) + 1/* all one */) - 1) << '\n';  
    std::cout << "integer working storage: " << (1 + (std::numeric_limits<T>::digits - 1) + (std::numeric_limits<T>::max_exponent - std::numeric_limits<T>::min_exponent)) << " bits"<<'\n';  
    std::cout<<'\n';  
      
    std::vector<T> list={-(T)0, -std::numeric_limits<T>::denorm_min(), -std::numeric_limits<T>::min(), -(T)1, -(T)1-std::numeric_limits<T>::epsilon(), -(T)10, -std::numeric_limits<T>::max(),  
        (T)0, std::numeric_limits<T>::denorm_min(), std::numeric_limits<T>::min(), (T)1, (T)1+std::numeric_limits<T>::epsilon(), (T)10, std::numeric_limits<T>::max()};  
    for(T v:list)  
    {  
        std::cout <<std::hexfloat<<v <<'\n';  
  
        using std::logb;  
        using std::scalbn;  
        int e = (v == 0) ? 0 : (int)(logb(v)), e_temp = e;  
        T m = scalbn(v, -e); // [1, 2)  
        std::cout<<"move to [1, 2): " <<std::hexfloat<<m<<" ( * 2^"<<std::dec<<std::noshowbase<< e<<" = origin)" <<'\n';  
  
        e -= (std::numeric_limits<T>::digits - 1);  
        m = scalbn(m, (std::numeric_limits<T>::digits - 1)); // integer  
        integer_working_storage_t<T> im(m);  
        std::cout<<"change mantissa to integer: " <<std::hexfloat<<m<<" ( = "<<std::hex<<std::showbase<<signprint(im)<<")"<<" ( * 2^"<<std::dec<<std::noshowbase<< e <<" = origin)"<<'\n';  
  
        e += (std::numeric_limits<T>::min_exponent - 1);  
        im=arithmetic_right_shift(im,(std::numeric_limits<T>::min_exponent - 1));  
        std::cout<<"change exponent to non-negative: " <<std::hex<<std::showbase<<signprint(im)<<" ( * 2^"<<std::dec<<std::noshowbase<< e<<" = origin)"<<'\n';  
  
        e += -e_temp;  
        im=arithmetic_right_shift(im,-e_temp);  
        std::cout<<"change exponent to (-min_exponent+mantissa): " <<std::hex<<std::showbase<<signprint(im)<<" ( * 2^"<<std::dec<<std::noshowbase<< e<<" = origin)"<<'\n';  
  
        std::cout<<'\n';  
    }  
}  
  
int main()  
{  
    test<float>();  
    test<double>();  
    test<boost::multiprecision::float128>();  
}  
```  
  
[turn a floating point value into an integer, and then use integer arithmetic to get correct results](https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGIAGwA7KSuADJ4DJgAcj4ARpjEElxmpAAOqAqETgwe3r4BwemZjgLhkTEs8YlcybaY9iUMQgRMxAS5Pn5BdQ3Zza0EZdFxCUkpCi1tHfndEwNDFVVjAJS2qF7EyOwc5gDMEcjeWCa7brR4LIQKJ9gmGgCCewdHmCduThPEmKw3d49m%2BwYhy8x1OThYhjwqV%2BDyeQJebwAbpgHCQYfcAPQYgDUcOBoLcsVQGQIGLQXwxsUIADoEKlobtbrCAc8Qa9TkSSRiWF5aI5Ul9kHhMgIMRECJhgAlafT0XiERziRNubz%2BYLhdkMVRaKgmARkgAOGUMpn/QH49luZAQggIOUs%2BFst5iYAkQgIFhyh4SlipAwSt4EACeqUYrEw2IAKujPl4HNjMsAGALxQB9AjYrHYhAEAipBQgLFzZAAa1QyOI2tQAHdqWgWBimBjArsWwBOMxcDEG3aSXa7ACsuz%2BJkCVge2MnUexDBO4/uU4TeCTKcE6YgkexLGW2JAM4g29xY6PABE/ouqMQ8K4EwR0CAQCSvqxcWZ/NjUKHiHq0ac3hAJnvR9Pm%2BFhX3fVBSGxNAGAmJcVyvNcM3Md8FGWc8p1HedF0XPAqGxADqQYXFTmxDQdy%2BAgNmI1ASLcN5XzMABacwzDohimIUIi5wwnDsUo6iP3Y0iuNnXZsMwwIz1hKSeO9TBfX9S1g1DZg2CjdEmC8Ih4OTRCCA3bFEXQmSJMnATiGIxM9LTAg3mjRkIGMuTHlk2F5MUvVlJDMN1Ic01N1ad02EcZBU3oKgCFTBQEDwgzN0RchBGxVh1kEEzXLM7E8Kct5yN4o8stwqgIFSrxBDyjK%2BL4iziKYqAmOcxkbiYsr0pc6rsXqJQCs62rsXqiBGvQv9TjaggRqy0dpIXKduteCdJKKqccvGyreuq/rEV%2BRlWpYNK7PEjbJ3m46cK2t5nX28rDqmtzXJmv4fT9LzAx8tSI38v5AqvW0QrwMKr2AHNotiyLDMS8UUuu9rTIKnLttOfLFsnLCztWmHDrcZHZs68zMCoyyBoapr6NOPaDsms7TpRvH%2BsG4aduwcaqdp6aCpp3HCvRkq1qRqq8fxwniMRsm3Apm6OuqznBaFwTEduRk%2Bbux77qehSXoDU4VN8z70S8TIjGywRJQSVNqxIEsImAaKiG/KV0xIk9sU5CYHx5PkoXVEU/BABgRmIN5XYId3VS9lENWcFB6VTNNYiYUtXEu2mIC4V8LAIwCH39tgrzC85LgIa5Tn8h98GAK4BuxLgd2sTO72znwEgB8KLiueybndphVFTTBVHSSJkqY28gJz5v87bouO8Zd2Il7/uBEYCbVgK1P0/r0em7z1vC%2BLtxS5AcvK%2BHmv14Ahu/a3luC/bkvO5ACEe77gel6rrPL9z6/J73g/LgYeeX7pRXrTYOodPYCgjj7B81lMDoFTBCJMhA2RQVAQ/MOEChRQJQAgFEJZYFQURKgPA6AbgkUVvOdWnktZuB1h9DSzUHjilNsQc2ltra2xIEwB2WN/LYiILHE2UoWEW2IFbIwHD7aYHXAldCY4CpZnfmgbSwk3DvxwaoKsepLqnERCosAOwBxuAYPolyBUDbWxHg%2BHUwBYhS0nOYo278FCiFoLEMSWUoYRhOM7JyTtvFkVrrsAAYmRXcBFxTLAgNY2ITllirC6umDWTsup2OnGBfxzixBuKclBJimBJqZhxCYAcFguBQTMALScCiL5KKxpdMwZh9rIj4bRYppTynLD3KxFRai%2B6aLqacT0o16INIItiAAVNiMwxTbgNO0aoi%2BWBTDDPfgwDICAazxx6sMlJwzun%2BLdBXBg6FRnzJMYY4xOwjruS5hGFiuwfGrKvhPXe09sBl2XMfaurMubpIeQmFx2SWBQXPpvT%2BLzb773vkfIuVca4FKzEwoR8NBFmxEWIm2ExOHcLedlFgB4fmLmqUBWp8zWLIAQIYKUW5DCOAUAoJgLTjYSiEV005KyL7qP6fMoZYtukQGSaxeZvTVDCovjFTZTBtli2squAyFwRp8oaSc6ZezRkCsmdMgcszVUysWSiMVo91mSulQxLxaq2IHKBhEFVZyDFGJMdcx4tMvGWH8aCxu4Kd6Qt/nPZ%2Bi8h7fNSRcbxQU/oExbkDEGMU4oQAuKQD1H9x7eqnnfGeD8/UL0HhmE%2BcTUnEofKSi1FKqURn9dmplayGBMUiMAPUeBkRsstRyoC6jDXQI2dWLZlo3iyv0nG7cZL1UTKmTMoVLay4GonX7Y1XapU9tIgukZzbnaHJteOsW5yHVXIoS69O/i8mJN9MGoZJ4w0egjYDZc0awbyuBYe56hKpwFpQOsAZy6S1GDLVm1%2BOkhp/wAQGuylgISCGFAyzpjEemcr7u2kAEq52mtOH28UA7FX1LYhq0d2qN29v1csvVRqEPdvmbspVK6PzWuObh04W7LmmNpi%2Botm77X0adYufqIanXsw8prbyqlwz0NNIQ4hfDMATETRELIYg8AAC8zbnAmG8xTBAoLvyk44GT8mWEqbeZMKURcMpo1pu/ZEqJA5glRcIth4isWSPTG8m4ZnUypP4MQCGIAVOVO5rTRcznqSpANggVM8dSwQH4UitFNnMV2y4VIgyxkn18SY2%2B%2BZ/nQslggOh2jrHHUq3kTiRRqXhl0bywVNzhl9MExAFVwzBVjNc1wlZ1hoj2F2biw5tN2BCCtAM94iLzX0Vtdi9w8LvWCZJZwil7S8yevED6yV3LO70a1e8XG2rjUUSpmpJeWQWWTkDiclt6kGWsubbCjt4ge283seqjlObC3sbec6g9gm1g1vpYTpl5Y53tu7cEPt1JuFVtuoef5/7BlJtEsKzU4rYtXvvtK8t3zU4iszZ2Ymrw9IzZEnKugI7F34hHKy6QfzrgScI5%2B%2BlyUEQzunztcU7dqSeNc2m4jpbqS0fs8Z2xihatGHJQhLTozcjaYSiU6cbljksJlK4JgA0pAzDy9INNFXY45cK6VwryQyvpqTfF1jKX2AIBYSYlr0gTENcW7Kar03VuzfK6YjrhXevUkG7eOgdYsR6A3BN%2Br0gVvze2/V8r83zu1cnn1%2BJrGnuvDe/ZMb035vLeO5t1JNXFgU8K4d9n8PrvbuTnd4qEkYC1SQOyA%2Bfphpfcy4D6H3X6eZf1%2B1w3yPbvo9ByVCHNB4DvYV5AFXswBoa9jhz9b1PEeM9Z4t8nvPUkfks44KsWgnABy8D8BwLQpBUCcBGVYSwCZ1ibFdbsHgpAQ6b6X6sEsARdjUg0AaDQbZdhtjbJIQIgRH/%2BAVyvjgkheAsAgCSBGh9hmAaADgDiBCSBtiGgdikAb5b474cC8AFgaDn6aBL4JowCICvq%2Bh0AJDkCUD1ipD4GJDAAKDMD5gbKqZYCIgAyYAABq141YAA8qpJwGfjQHyAkAWBALEBgaQFSMwMQEGBwbwPWGwIICwQwLQKIZfqQFgKBsAOIPIfgIKI4MiAWPIX3CiNpNsGfkwr/lvucLEN%2BCIR4FgAIQQFeIAdwJgdqFwgoEwZgKwewXYTIIICIGIOwFIB4fIEoGoAIboGUgYEYCgO9pYPoHgLEAWLAB9CAMHKTqMNwAaDwHEtvqkI0FoUxJxHeN4kmF4BEaUpIANCwWxExAAOpiC0ADShjoC0oAy1G2jPjoADS0AACOXgTA6ANoCAKB9QKIjQLgDA7gngnQegYQEQwwlQowZSRQWQAg0wfgcxGQCxDACwAcegdggxfQkw7QYx%2BQZS2xDgux8wUxiwsxtgexSxWxexGxMx1QqwCgR%2BWwEgy%2Bq%2B6%2BAhSB2IqgBo/gTE/gJRwAyAyABE1h5UJYO4EAuAhAJAr4p%2BywvAF%2BWgqwEASAOhyA2kJAhBpU82Cgyghg9QQgnaG%2BZ%2BxBdA9aAgBJkQtAxJNYCB4hqAeB9AZBFBTAVBqAqm5JLJLB2kdJ1YDJIQqgKI9weJnAvAGJzQ%2BAG%2BvA/AnhLiPh0gcp/hKg6g8hwR%2BgVK4RbqkRJhsRUA8RiRFYHBqRV%2BGRWRnAORgE%2BR/sRRUgpR5RVRtANRTEdRDRoJrpzR3wrRTEHRXRPReofRawGwrxRxd4EQ1JRJJJYh5%2BXw%2BhvA1Y34qQYh7xHAa%2B8BXxnA2AwpmJdsPxfxAJQJIJYJxAEJUJe%2B1gUEMJeZewNcSJGB6ROC3RowJON%2Buw/g1IMBbYGg/gGg4BDSXAz%2BKQv%2B/%2BGZ8hSBKBegyJZpv%2BZgABEgfZY5iB4p6Bl%2B6RFYUCkgQAA%3D%3D).  
```C++  
#include<limits>  
#include<iostream>  
#include<iomanip>  
#include<vector>  
// #include<boost/core/bit.hpp>  
#include<boost/multiprecision/integer.hpp>  
#include<boost/multiprecision/float128.hpp>  
#include<cmath>  
#include<algorithm>  
  
template<typename T>  
struct signprint_t // https://stackoverflow.com/a/7373921/8343353  
{  
    T n;  
    signprint_t(T m) : n(m) { }  
    friend std::ostream & operator<<(std::ostream & o, const signprint_t & s)  
    {  
        if (s.n < 0) return o << "-" << -s.n;  
        return o << s.n;  
    }  
};  
template<typename T>  
auto signprint(T v)  
{  
    return signprint_t<T>(v);  
}  
  
template<typename T>  
T arithmetic_left_shift(T v,int amount)  
{  
    if(v<0)  
    {  
        if(amount<0)  
            return -((-v)>>-amount);  
        else  
            return -((-v)<<amount);  
    }  
    else  
    {  
        if(amount<0)  
            return v>>-amount;  
        else  
            return v<<amount;  
    }  
}  
  
template<typename T>  
T arithmetic_right_shift(T v,int amount)  
{  
    if(v<0)  
    {  
        if(amount<0)  
            return -((-v)<<-amount);  
        else  
            return -((-v)>>amount);  
    }  
    else  
    {  
        if(amount<0)  
            return v<<-amount;  
        else  
            return v>>amount;  
    }  
}  
  
template<typename T>  
using integer_working_storage_t = boost::multiprecision::number<boost::multiprecision::cpp_int_backend<  
    (1 + (std::numeric_limits<T>::digits - 1) + (std::numeri c_limits<T>::max_exponent - std::numeric_limits<T>::min_exponent)),  
    (1 + (std::numeric_limits<T>::digits - 1) + (std::numeric_limits<T>::max_exponent - std::numeric_limits<T>::min_exponent)),  
    boost::multiprecision::signed_magnitude, boost::multiprecision::checked, void> >;  
  
template<typename T>  
integer_working_storage_t<T> to_integer_working_storage_t(T v){  
    // std::cout <<std::hexfloat<<v <<'\n';  
  
    using std::logb;  
    using std::scalbn;  
    int e = (v == 0) ? 0 : (int)(logb(v)), e_temp = e;  
    T m = scalbn(v, -e); // [1, 2)  
    // std::cout<<"move to [1, 2): " <<std::hexfloat<<m<<" ( * 2^"<<std::dec<<std::noshowbase<< e<<" = origin)" <<'\n';  
  
    e -= (std::numeric_limits<T>::digits - 1);  
    m = scalbn(m, (std::numeric_limits<T>::digits - 1)); // integer  
    integer_working_storage_t<T> im(m);  
    // std::cout<<"change mantissa to integer: " <<std::hexfloat<<m<<" ( = "<<std::hex<<std::showbase<<signprint(im)<<")"<<" ( * 2^"<<std::dec<<std::noshowbase<< e <<" = origin)"<<'\n';  
  
    e += (std::numeric_limits<T>::min_exponent - 1);  
    im=arithmetic_right_shift(im,(std::numeric_limits<T>::min_exponent - 1));  
    // std::cout<<"change exponent to non-negative: " <<std::hex<<std::showbase<<signprint(im)<<" ( * 2^"<<std::dec<<std::noshowbase<< e<<" = origin)"<<'\n';  
  
    e += -e_temp;  
    im=arithmetic_right_shift(im,-e_temp);  
    // std::cout<<"change exponent to (-min_exponent+mantissa): " <<std::hex<<std::showbase<<signprint(im)<<" ( * 2^"<<std::dec<<std::noshowbase<< e<<" = origin)"<<'\n';  
  
    // std::cout<<'\n';  
    return im;  
}  
template<typename T>  
void test(std::initializer_list<T>list, std::initializer_list<T>targets)  
{  
    std::vector<integer_working_storage_t<T>>vec_;  
    for(T v:list)  
    {  
        vec_.push_back(to_integer_working_storage_t(v));  
        // std::cout<<vec_.back()<<'\n';  
    }  
    // std::cout<<'\n';  
    for(T target:targets)  
    {  
        integer_working_storage_t<T>itarget=to_integer_working_storage_t(target);  
        // std::cout<<itarget<<'\n';  
        itarget=(itarget-vec_.front())%(vec_.back()-vec_.front());  
        if(itarget<0)  
            itarget+=(vec_.back()-vec_.front());  
        itarget+=vec_.front();  
        // std::cout<<itarget<<'\n';  
        std::cout<< (std::upper_bound(vec_.begin(),vec_.end(),itarget)-vec_.begin()-1) <<'\n';  
    }  
    // std::cout<<'\n';  
    std::cout<<'\n';  
}  
  
int main()  
{  
    test<float>({1,1e8,2e8,},{1e8,2e8,4e8,});  
    test<float>({-2e8,-1e8,-1,},{-1e8,-2e8,-4e8,});  
    test<double>({1,1e8,2e8,},{1e8,2e8,4e8,});  
    test<double>({-2e8,-1e8,-1,},{-1e8,-2e8,-4e8,});  
    test<boost::multiprecision::float128>({1,1e8,2e8,},{1e8,2e8,4e8,});  
    test<boost::multiprecision::float128>({-2e8,-1e8,-1,},{-1e8,-2e8,-4e8,});  
}  
```

---

## Comment 2

> Username: HDembinski  
> Created at: 2023-01-11 10:58:03 UTC  
> Updated at: 2023-01-11 10:58:31 UTC  
> Url: https://github.com/boostorg/histogram/issues/380#issuecomment-1378572734  

I do not think that you need to convert floats to ints in order to have exact arithmetic. Also in the floating point format, all arithmetic operations are exact if do not exhaust the bits in the mantissa (equivalent to integer overflow) and if you only divide by numbers that do not leave a residual.

---

## Comment 3

> Username: jhcarl0814  
> Created at: 2023-01-12 11:00:19 UTC  
> Url: https://github.com/boostorg/histogram/issues/380#issuecomment-1380156623  

@HDembinski The problem occurs when `if do not exhaust the bits in the mantissa` is not satisfied. In the example above, `2e+8 - 1` and `4e+8 - 1` can not be stored in `float` exactly (the least significant bits are lost).  
```  
BOOST_TEST_EQ(a.index(4e8), 0); // test 'a.index(4e8) == 0' ('-1' == '0') failed  
```  
because `x -= std::floor((4e+8 - 1) / (2e+8 - 1)) * (2e+8 - 1);` is actually `x -= std::floor(4e+8 / 2e+8) * 2e+8;` make `x` become `0`, `0`'s index in `{ 1., 1e+8, 2e+8, }` is `-1` (should be impossible to be returned by `index`).
