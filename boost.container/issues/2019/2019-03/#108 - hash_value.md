# #108 - hash_value [Closed]

> Username: gast128  
> Created at: 2019-03-13 14:05:29 UTC  
> Updated at: 2025-11-08 10:23:31 UTC  
> Closed at: 2025-11-08 10:23:31 UTC  
> Url: https://github.com/boostorg/container/issues/108  

Dear all,  
  
it would be nice if the container library had overloads for hashing (both Boost as std), e.g.:  
  
```  
namespace boost  
{  
    template<typename Value, std::size_t Capacity>  
    std::size_t hash_value(const boost::container::static_vector<Value, Capacity>& crvec)  
    {  
        return boost::hash_range(crvec.cbegin(), crvec.cend());  
    }  
}  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2025-11-08 10:23:31 UTC  
> Url: https://github.com/boostorg/container/issues/108#issuecomment-3506396152  

With recent Boost Hash improvements, having hash_value overloads is not necessary, anything with begin()/end() can be hashed without introducing any dependency. The standard does not offer hashing for containers because there is no good/safe default for everyone (see https://www.reddit.com/r/cpp_questions/comments/irfglg/why_is_there_no_default_hash_for_vectors/)
