# #114 - Adaptors: returning underlying container iterator (root) of adapted range (view) [Open]

> Username: trueqbit  
> Created at: 2020-10-23 13:16:14 UTC  
> Updated at: 2020-10-26 20:49:19 UTC  
> Url: https://github.com/boostorg/range/issues/114  

When I keep using iterators of adapted ranges (aka views) I find it very useful not having to use `range_iterator::base()` in order to access the underlying container iterator and currently iterated object.  
  
I often find code like  
  
    if (auto rng = find<return_found_end>(c | transformed(std::bind(&Object::name, _1)), v))  
    {  
        const Object& o = *rng.begin().base();  
        // ...  
    }  
    // or  
    if (auto it = find(c | transformed(std::bind(&Object::name, _1)), v);  
        it.base() != c.end())  
    {  
        const Object& o = *it.base();  
        // ...  
    }  
  
  
Yes, it's just a matter of adding the call to `.base()`, however I always find less clutter to be worthwhile. The issue gets more obvious the more adaptors get chained.    
Instead the intent can be expressed by additional `enum range_return_value` values.  
  
  
Currently this is exactly what I am doing, using this header file:  
  
    #pragma once  
    #include <boost/range/detail/range_return.hpp>  
  
  
    namespace kj  
    {  
  
    inline constexpr auto return_base_found = boost::range_return_value(boost::return_begin_end + 1);  
    inline constexpr auto return_base_found_end = boost::range_return_value(return_base_found + boost::return_found_end);  
  
    }  
  
    namespace boost  
    {  
  
    template<typename SinglePassRange>  
    struct range_return<SinglePassRange, kj::return_base_found>  
    {  
        using iterator = typename range_iterator<SinglePassRange>::type;  
        using type = typename iterator::base_type;  
  
        static type pack(iterator found, SinglePassRange&)  
        {  
            return found.base();  
        }  
    };  
  
    template<typename SinglePassRange>  
    struct range_return<SinglePassRange, kj::return_base_found_end>  
    {  
        using iterator = typename range_iterator<SinglePassRange>::type;  
        using base_iterator = typename iterator::base_type;  
        using type = boost::iterator_range<base_iterator>;  
  
        static type pack(iterator found, SinglePassRange& rng)  
        {  
            return type(found.base(), boost::end(rng).base());  
        }  
    };  
  
    }  
  
  
But extending the range_return handling in this way isn't perfect, and I think others can benefit as well.  
  
That's why I propose extending `enum range_return_value` with the following enum values:  
  
    return_root_found,  
    return_root_next,  
    return_root_prior,  
    return_root_begin_found,  
    return_root_begin_next,  
    return_root_begin_prior,  
    return_root_found_end,  
    return_root_next_end,  
    return_root_prior_end,  
    return_root_begin_end,  
  
At the very least I'd like to see a function, e.g. named `root_of()`, which walks the adaptor iterator chain and returns the root iterator, such that ...  
  
        const Object& o = *root_of(it);  
  
... works transparently no matter how many iterator are layered on top of each other.
