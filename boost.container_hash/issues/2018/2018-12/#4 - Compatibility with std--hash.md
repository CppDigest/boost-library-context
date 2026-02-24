# #4 - Compatibility with std::hash [Open]

> Username: JVApen  
> Created at: 2018-12-31 18:26:04 UTC  
> Updated at: 2022-10-27 12:40:39 UTC  
> Url: https://github.com/boostorg/container_hash/issues/4  

boost::hash_combine is very useful, however, it does require to implement a hash function twice in order to be used. `std::hash` and `boost::hash_value`.  
  
On stackoverflow, several 'workarounds' exist for making this easier, all assume you are able to encapsulate the includes to hash.hpp in your own wrapping header.   
  
Would it be possible to allow hash_combine ... to fall back to `std::hash` when no `hash_value` is available?  
  
  
  
https://stackoverflow.com/a/51915825/2466431

---

## Comment 1

> Username: pdimov  
> Created at: 2022-10-27 12:40:39 UTC  
> Url: https://github.com/boostorg/container_hash/issues/4#issuecomment-1293467794  

That's an interesting idea that can allow us to eliminate the various `hash_value` overloads for standard types, and is apparently also what Abseil does by default. But it's not quite as trivial as in the SO answer, because today, it's typical for a user-defined type to declare both `hash_value` (for `boost::hash`) _and_ specialize `std::hash`. Something like this, for example:  
```  
struct uuid  
{  
    unsigned char data[ 16 ];  
  
    inline friend std::size_t hash_value( uuid const& u ) noexcept  
    {  
        return boost::hash_value( u.data );  
    }  
  
    inline friend bool operator==( uuid const& u1, uuid const& u2 ) noexcept  
    {  
        return std::equal( u1.data + 0, u1.data + 16, u2.data );  
    }  
};  
  
namespace std  
{  
  
template<> struct hash< ::uuid >  
{  
    std::size_t operator()( uuid const& u ) const noexcept  
    {  
        return hash_value( u );  
    }  
};  
  
} // namespace std  
```  
And in that case, if there's an overload for `boost::hash_value` for all std-hashable types, we're disturbingly close to infinite recursion. It doesn't seem to happen here, but I'm not particularly comfortable with this arrangement.
