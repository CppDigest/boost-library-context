# #33 - modify_payload: new method for hashed_index [Closed]

> Username: tobias-loew  
> Created at: 2020-04-17 15:46:10 UTC  
> Updated at: 2020-04-21 08:01:38 UTC  
> Closed at: 2020-04-21 08:01:38 UTC  
> Url: https://github.com/boostorg/multi_index/issues/33  

Hi,  
I really like multi_index. I use multiple versions of the following schema: a vector which has an additional hashed-key to access the values.  
```  
    typedef boost::multi_index::multi_index_container<  
        some_type,  
        boost::multi_index::indexed_by<  
        boost::multi_index::random_access<>,  // keep insertion order  
        boost::multi_index::hashed_unique<  
        boost::multi_index::tag<some_tag>,  
        boost::multi_index::member<some_type, key_type, &some_type::m_key> >  
        >  
    > some_vector;  
```  
But I always stumble when I want to change the "mapped-to" values after `find`-ing them through the `hashed_unique`-interface: I can either   
- use "modify", but this has a lot of runtime-overhead  
- use "mutable" for the mapped-to variables as shown here https://www.boost.org/doc/libs/1_72_0/libs/multi_index/doc/tutorial/techniques.html#emulate_assoc_containers, but this makes the mapped-to values ALWAYS modifiable (not only in the context of an multi_index_container-access)  
- cast away the `const` from the mapped-to members when writing to them, but if you want to do it in a generic way you end up with something like this  
```  
        const_cast<std::add_lvalue_reference_t<std::remove_cvref_t<decltype((*it))>>>(*it).m_member = value;  
```  
  
So, my idea is to add a new function, e.g. named `modify_payload`, which has the same syntax as `modify` BUT it DOES NOT check for changes of keys (I also would remove the try-catch handling). And it is absolutely UP TO THE USER to not change any value that is used a key.  
  
If you like the idea, I could implement I first try and create a PR.  
  
best  
Tobias

---

## Comment 1

> Username: joaquintides  
> Created at: 2020-04-17 20:01:50 UTC  
> Updated at: 2020-04-17 20:02:27 UTC  
> Url: https://github.com/boostorg/multi_index/issues/33#issuecomment-615438976  

Hi Tobias, thank you for your interest in Boost.MultiIndex.  
  
I honestly don't see much merit in adding such a member function as `modify_payload` for what it's a mere wrapper around `const_cast`, with the added problem that it would rely on the user to keep a non-enforced promise to not break anything: explicit `const_cast`ing is at least more conspicuous to the reviewer's eye.  
  
As for the clumsiness of `std::add_lvalue_reference_t`and stuff to `const_cast` generically, the thing can be written more tersely as follows:  
```  
template<typename T>  
T& strip_const(const T& x){return const_cast<T&>(x);}  
...  
strip_const(*it).m_member=value;  
```

---

## Comment 2

> Username: tobias-loew  
> Created at: 2020-04-19 08:38:42 UTC  
> Url: https://github.com/boostorg/multi_index/issues/33#issuecomment-616077206  

Hi Joaquin,  
Thanks for your reply.  
I understand your arguments. To get a safe solution multi-index would need something like "with_data" in bimaps.  
I like the strip_const and I think it would help a lot, if you could add it to the documentation in the section "Emulation of associative containers".  
Stay healthy  
Tobias

---

## Comment 3

> Username: joaquintides  
> Created at: 2020-04-21 08:01:38 UTC  
> Url: https://github.com/boostorg/multi_index/issues/33#issuecomment-617020973  

I feel advice along the `strip_const` approach does not belong in the documentation for Boost.MultiIndex, and I'd rather not give the impression I'm endorsing `const_cast`ing things away when `modify` is the proper way to go with --folks will undoubtedly arrive at `const_cast` on their own, as many Stack Overflow questions during all these years show :-)  
  
Thank you again for taking the time to report. Best,
