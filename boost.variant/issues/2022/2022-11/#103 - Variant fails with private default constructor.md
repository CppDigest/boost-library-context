# #103 - Variant fails with private default constructor [Open]

> Username: dmenendez-gruposantander  
> Created at: 2022-11-10 08:56:19 UTC  
> Updated at: 2022-11-10 08:56:19 UTC  
> Url: https://github.com/boostorg/variant/issues/103  

Boost.Variant fails if its types have a private default constructor, using gcc 12.2  
Minimal example: https://godbolt.org/z/cG4PnW8ee  
  
```  
#include <boost/variant.hpp>  
#include <boost/variant2.hpp>  
class Foo  
{  
    public:  
        Foo(int a_) : a(a_) {}  
        int a;  
    private:  
        Foo();  
};  
struct Voo  
{  
    Foo f;  
    double d;  
};  
struct Doo  
{  
    Foo f;  
    int s;  
};  
#ifndef USE_VARIANT2  
    using VooDoo = boost::variant<Voo, Doo>;  
    void testVooDoo(const VooDoo& voodoo)  
    {  
        VooDoo v{Voo{Foo(3)}};  
    }   
#else  
    using VooDoo = boost::variant2::variant<Voo, Doo>;  
    void testVooDoo(const VooDoo& voodoo)  
    {  
        VooDoo v{Voo{Foo(3)}};  
    }  
#endif  
```  
If I change to use Boost.Variant2, it works.  
If I go back to gcc 7.5, it works (fails after that version).  
  
The error shows:  
```  
/opt/compiler-explorer/libs/boost_1_80_0/boost/variant/variant.hpp:194:17:   required from 'struct boost::detail::variant::find_fallback_type<boost::mpl::l_item<mpl_::long_<2>, Voo, boost::mpl::l_item<mpl_::long_<1>, Doo, boost::mpl::l_end> > >'  
/opt/compiler-explorer/libs/boost_1_80_0/boost/variant/variant.hpp:1252:17:   required from 'class boost::variant<Voo, Doo>'  
<source>:26:16:   required from here  
/opt/compiler-explorer/libs/boost_1_80_0/boost/type_traits/has_nothrow_constructor.hpp:27:84: error: 'Foo::Foo()' is private within this context  
   27 | template <class T> struct has_nothrow_constructor : public integral_constant<bool, BOOST_HAS_NOTHROW_CONSTRUCTOR(T)>{};  
      |                                                                                    ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
<source>:9:9: note: declared private here  
    9 |         Foo();  
      |         ^~~  
```  
It mentions `find_fallback_type`, so I guess that at some point it tries to determine the "fallback type" to default construct the `VooDoo`.  
However, nowhere in my program I need to default construct a `VooDoo`, so I'm not sure why that type needs to be "computed".  
  
Also, somehow Boost.Variant2 doesn't fail, so there must be a way to make this work.
