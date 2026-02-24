# #276 Fix clang -Wdeprecated-copy warning when using -std=gnu++2b in boost/archive/detail/helper_collection.hpp [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2023-01-08 16:30:03 UTC  
> Updated at: 2023-08-23 21:23:25 UTC  
> Merged at: 2023-08-23 18:47:41 UTC  
> Closed at: 2023-08-23 18:47:42 UTC  
> Url: https://github.com/boostorg/serialization/pull/276  

```  
In file included from magadm/CmdGetInfoHTHSession.cpp:24:  
In file included from /remote/users/mlamesch/CloneFromPlatinum/SI_Pack23-2/common/include/si/common/HTHSegmentationCacheInfoSerialization.h:19:  
In file included from /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/archive/text_iarchive.hpp:24:  
In file included from /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/archive/basic_text_iarchive.hpp:30:  
In file included from /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/archive/detail/common_iarchive.hpp:21:  
In file included from /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/archive/detail/basic_iarchive.hpp:28:  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/archive/detail/helper_collection.hpp:58:44: error: definition of implicit copy constructor for 'predicate' is deprecated because it has a user-declared copy assignment operator [-Werror,-Wdeprecated-copy]  
        BOOST_DELETED_FUNCTION(predicate & operator=(const predicate & rhs))  
                                           ^  
/opt/1A/toolchain/x86_64-v23.0.2/lib64/gcc/x86_64-1a-linux-gnu/13.0.0/../../../../include/c++/13.0.0/bits/predefined_ops.h:325:37: note: in implicit copy constructor for 'boost::archive::detail::helper_collection::predicate' first required here  
    { return _Iter_pred<_Predicate>(_GLIBCXX_MOVE(__pred)); }  
                                    ^  
/opt/1A/toolchain/x86_64-v23.0.2/lib64/gcc/x86_64-1a-linux-gnu/13.0.0/../../../../include/c++/13.0.0/bits/move.h:167:30: note: expanded from macro '_GLIBCXX_MOVE'  
                             ^  
/opt/1A/toolchain/x86_64-v23.0.2/lib64/gcc/x86_64-1a-linux-gnu/13.0.0/../../../../include/c++/13.0.0/bits/stl_algo.h:3923:26: note: in instantiation of function template specialization '__gnu_cxx::__ops::__pred_iter<boost::archive::detail::helper_collection::predicate>' requested here  
                            __gnu_cxx::__ops::__pred_iter(__pred));  
                                              ^  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/archive/detail/helper_collection.hpp:75:18: note: in instantiation of function template specialization 'std::find_if<__gnu_cxx::__normal_iterator<std::pair<const void *, boost::shared_ptr<void>> *, std::vector<std::pair<const void *, boost::shared_ptr<void>>>>, boost::archive::detail::helper_collection::predicate>' requested here  
            std::find_if(  
                 ^  
```

---

## Comment 1

> Username: robertramey  
> Created_at: 2023-08-23 20:50:41 UTC  
> Url: https://github.com/boostorg/serialization/pull/276#issuecomment-1690620769  

Did you actually test this?  When merge this change I get compile error:  
  
`class helper_collection  
{  
    helper_collection(const helper_collection&);              // non-copyable  
    helper_collection& operator = (const helper_collection&); // non-copyable  
  
    // note: we dont' actually "share" the function object pointer  
    // we only use shared_ptr to make sure that it get's deleted  
  
    typedef std::pair<  
        const void *,  
        boost::shared_ptr<void>  
    > helper_value_type;  
    template<class T>  
    boost::shared_ptr<void> make_helper_ptr(){  
        // use boost::shared_ptr rather than std::shared_ptr to maintain  
        // c++03 compatibility  
        return boost::make_shared<T>();  
    }  
  
    typedef std::vector<helper_value_type> collection;  
    collection m_collection;  
  
    struct predicate {  
        BOOST_DEFAULTED_FUNCTION(predicate(const predicate& rhs), { m_ti = rhs.m_ti; }) // Cannot assign to non-static data member 'm_ti' with const-qualified type 'const void *const'  
        BOOST_DELETED_FUNCTION(predicate & operator=(const predicate & rhs))  
    public:  
        const void * const m_ti;  // Non-static data member 'm_ti' declared const here  
        bool operator()(helper_value_type const &rhs) const {  
            return m_ti == rhs.first;  
        }  
        predicate(const void * ti) :  
            m_ti(ti)  
        {}  
    };  
protected:  
    helper_collection(){}  
    ~helper_collection(){}  
public:  
    template<typename Helper>  
    Helper& find_helper(void * const id = 0) {  
        collection::const_iterator it =  
            std::find_if(  
                m_collection.begin(),  
                m_collection.end(),  
                predicate(id)  
            );  
  
        void * rval = 0;  
        if(it == m_collection.end()){  
            m_collection.push_back(  
                std::make_pair(id, make_helper_ptr<Helper>())  
            );  
            rval = m_collection.back().second.get();  
        }  
        else{  
            rval = it->second.get();  
        }  
        return *static_cast<Helper *>(rval);  
    }  
};  
`

---

## Comment 2

> Username: Romain-Geissler-1A  
> Created_at: 2023-08-23 21:22:26 UTC  
> Updated_at: 2023-08-23 21:23:25 UTC  
> Url: https://github.com/boostorg/serialization/pull/276#issuecomment-1690658715  

I am not at work right now, so I cannot check, but all patches we submit we usually apply it in our own code base. However it’s been a long time we don’t use C++03 anymore, so in our case what we test is the explicitly defaulted copy constructor. I actually expect the CI to detect these kind of things to prevent from merging too soon.  
  
i guess here the fix is to initialize the member outside the body, but I don’t know if this is allowed by this boost macro. I can have a look on Friday when I am back to work.

---
