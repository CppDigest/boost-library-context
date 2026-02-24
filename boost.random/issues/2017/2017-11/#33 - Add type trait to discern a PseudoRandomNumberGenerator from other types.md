# #33 - Add type trait to discern a PseudoRandomNumberGenerator from other types [Closed]

> Username: jeking3  
> Created at: 2017-11-03 20:05:55 UTC  
> Updated at: 2017-11-04 12:30:21 UTC  
> Closed at: 2017-11-04 12:19:27 UTC  
> Url: https://github.com/boostorg/random/issues/33  

A `PseudoRandomNumberGenerator` requires a seed in most cases to be useful and must provide a `seed()` method.  I do not see a way to take a `UniformRandomNumberGenerator` as a template argument and then determine if it is a `PseudoRandomNumberGenerator` which has a `seed()` method that needs to be called.  
  
I believe it would be helpful for the random number generator types to declare their concept in some way so this could be discerned at compile time cleanly.  Currently in Boost.Uuid if you look at http://www.boost.org/doc/libs/1_65_1/boost/uuid/seed_rng.hpp it has a special case for `random_device` that allows `basic_random_generator<random_device>` to work properly.  It would be nice to make this more generic, and only seed if it is actually a `PseudoRandomNumberGenerator`.

---

## Comment 1

> Username: swatanabe  
> Created at: 2017-11-03 20:46:10 UTC  
> Url: https://github.com/boostorg/random/issues/33#issuecomment-341822495  

AMDG  
  
On 11/03/2017 02:05 PM, James E. King, III wrote:  
> A `PseudoRandomNumberGenerator` requires a seed in most cases to be useful and must provide a `seed()` method.  I do not see a way to take a `UniformRandomNumberGenerator` as a template argument and then determine if it is a `PseudoRandomNumberGenerator` which has a `seed()` method that needs to be called.  
>   
> I believe it would be helpful for the random number generator types to declare their concept in some way so this could be discerned at compile time cleanly.  Currently in Boost.Uuid if you look at http://www.boost.org/doc/libs/1_65_1/boost/uuid/seed_rng.hpp it has a special case for `random_device` that allows `basic_random_generator<random_device>` to work properly.  It would be nice to make this more generic, and only seed if it is actually a `PseudoRandomNumberGenerator`.  
>   
  
  Any such system must work with the standard generators as well.  
This means that we have to rely on detecting whether seed  
is present, which can only be done with SFINAE or overload  
resolution with a derived class.  This doesn't absolutely  
have to be implemented inside Boost.Random, and I'm not  
convinced that it's a sufficiently common need.  Usually  
generic code should not create PRNGs. They should generally  
be created and seeded by code that knows the concrete type.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: jeking3  
> Created at: 2017-11-03 22:46:40 UTC  
> Url: https://github.com/boostorg/random/issues/33#issuecomment-341845583  

This is in specific reference to seed_rng.hpp inside Boost.Uuid as anyone can add any UniformRandomNumberGenerator to basic_random_generator, so it has to support both seedable and not-seedable implementations, and right now there is no way to tell...

---

## Comment 3

> Username: swatanabe  
> Created at: 2017-11-03 23:48:31 UTC  
> Url: https://github.com/boostorg/random/issues/33#issuecomment-341853801  

AMDG  
  
On 11/03/2017 04:46 PM, James E. King, III wrote:  
> This is in specific reference to seed_rng.hpp inside Boost.Uuid as anyone can add any UniformRandomNumberGenerator to basic_random_generator, so it has to support both seedable and not-seedable implementations, and right now there is no way to tell...  
>   
  
Basic C++03 implementation:  
  
```  
struct tag {};  
typedef char (&fail)[2];  
fail operator,(const tag&, char);  
  
template<class T>  
struct has_seed_impl1 : T {  
  tag seed(...);  
};  
template<class T>  
struct has_seed_impl2 : has_seed_impl1<T> {  
  using has_seed_impl1<T>::seed;  
};  
template<class T>  
struct has_seed<T> : mpl::bool_<(sizeof(  
  declval<has_seed_impl2<T>&>().seed(  
    declval<uint32_t*&>(),  
    declval<uint32_t*>()), 'a') == sizeof(char))> {};  
```  
  
Basic expression SFINAE implementation:  
  
```  
template<class T>  
using has_seed_impl =  
  decltype(declval<T&>().seed(  
    declval<uint32_t*&>(), declval<uint32_t*>()));  
  
template<class T>  
using has_seed = std::experimental::is_detected<has_seed_impl, T>;  
```  
  
Note: BOOST_TTI_HAS_FUNCTION almost does what you want, but  
it's somewhat too strict as it makes a pointer-to-member.  
(The standard permits implementations to add additional  
default arguments to class member functions.  As such there  
is no portable way to take a member pointer for any  
non-virtual member function from the standard library)  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: jeking3  
> Created at: 2017-11-04 00:02:51 UTC  
> Url: https://github.com/boostorg/random/issues/33#issuecomment-341855352  

That's cool, I was looking at maintaining the boost::random::random_device template specialization in seed_rng, however I would much prefer a more generic implementation - plus I get to learn something in the process.  Thank you!

---

## Comment 5

> Username: jeking3  
> Created at: 2017-11-04 12:19:27 UTC  
> Url: https://github.com/boostorg/random/issues/33#issuecomment-341892585  

I was able to make something that works using `BOOST_TTI_HAS_MEMBER_FUNCTION`:  
```  
private:  
    // Detect whether UniformRandomNumberGenerator has a seed() method which indicates that  
    // it is a PseudoRandomNumberGenerator and needs a seed to initialize it.  This allows  
    // basic_random_generator to take any type of UniformRandomNumberGenerator and still  
    // meet the post-conditions for the constructor.  
    BOOST_TTI_HAS_MEMBER_FUNCTION(seed)  
  
    template<class MaybePseudoRandomNumberGenerator>  
    typename boost::enable_if<has_member_function_seed<MaybePseudoRandomNumberGenerator, void> >::type  
    seedrng(MaybePseudoRandomNumberGenerator& rng)  
    {  
        detail::seed_rng egen;  
        generator_iterator<detail::seed_rng> begin(&egen);  
        generator_iterator<detail::seed_rng> end;  
        rng.seed(begin, end);  
    }  
  
  
    template<class MaybePseudoRandomNumberGenerator>  
    typename boost::disable_if<has_member_function_seed<MaybePseudoRandomNumberGenerator, void> >::type  
    seedrng(MaybePseudoRandomNumberGenerator&)  
    {  
    }  
```  
  
This allowed me to remove the specific template specialization for `boost::random::random_device` from seed_rng.hpp, which is nice - now one can pass in any `UniformRandomNumberGenerator`, not limited to a `PseudoRandomNumberGenerator || boost::random::random_device`.

---

## Comment 6

> Username: jeking3  
> Created at: 2017-11-04 12:30:20 UTC  
> Url: https://github.com/boostorg/random/issues/33#issuecomment-341893249  

Wow, that solution causes an internal compiler error on Visual Studio 2010, 2012, and 2013, repeatable every time.  That is really depressing.
