# #35 - key_of_value on treap_set seems to be broken in 1.69 [Closed]

> Username: jm-mikkelsen  
> Created at: 2019-02-01 16:30:23 UTC  
> Updated at: 2019-02-12 06:57:10 UTC  
> Closed at: 2019-02-11 23:31:08 UTC  
> Url: https://github.com/boostorg/intrusive/issues/35  

The code below fails to compile with Boost 1.69. It works fine with 1.59.  
  
It looks a lot like `key_of_value` is not handled correctly, unless I am missing something obvious.  
  
```  
#include <boost/intrusive/treap_set.hpp>  
#include <string>  
  
using namespace boost::intrusive;  
  
class Test : public bs_set_base_hook<>  
{  
public:  
    Test(int priority, std::string key) :  
	m_priority(priority), m_key(std::move(key))  
    {  
    }  
  
    int priority() const { return m_priority; }  
    std::string const& key() const { return m_key; }  
  
private:  
    int m_priority;  
    std::string m_key;  
};  
  
struct PriorityOrder  
{  
    bool operator()(Test const& lhs, Test const& rhs) const {  
	return lhs.priority() < rhs.priority();  
    }  
};  
  
struct StringKey  
{  
    using type = std::string;  
  
    type const& operator()(Test const& v) const {  
	return v.key();  
    }  
};  
  
int main()  
{  
    treap_set<Test, key_of_value<StringKey>, priority<PriorityOrder>> s;  
  
    Test t1(1, "hello");  
  
    s.insert(t1);  
}  
```  
  
Compiler output on Xcode clang:  
  
```  
janm@jmmacpro: amt5-api $ c++ -std=c++14 -I /usr/local/include/boost-1_69 jan.cpp  
In file included from jan.cpp:1:  
In file included from /usr/local/include/boost-1_69/boost/intrusive/treap_set.hpp:17:  
In file included from /usr/local/include/boost-1_69/boost/intrusive/treap.hpp:20:  
In file included from /usr/local/include/boost-1_69/boost/intrusive/bstree.hpp:33:  
In file included from /usr/local/include/boost-1_69/boost/intrusive/detail/key_nodeptr_comp.hpp:26:  
/usr/local/include/boost-1_69/boost/intrusive/detail/tree_value_compare.hpp:101:14: error:   
      no matching function for call to object of type 'const  
      boost::intrusive::tree_value_compare<Test *, PriorityOrder, StringKey,  
      bool, false>::key_compare' (aka 'const PriorityOrder')  
   {  return this->key_comp()(key1, KeyOfValue()(value2));  }  
             ^~~~~~~~~~~~~~~~  
/usr/local/include/boost-1_69/boost/intrusive/detail/key_nodeptr_comp.hpp:110:14: note:   
      in instantiation of member function  
      'boost::intrusive::tree_value_compare<Test *, PriorityOrder, StringKey,  
      bool, false>::operator()' requested here  
   {  return base()(t1, *traits_->to_value_ptr(t2));  }  
             ^  
/usr/local/include/boost-1_69/boost/intrusive/treap_algorithms.hpp:651:33: note:   
      in instantiation of function template specialization  
      'boost::intrusive::detail::key_nodeptr_comp<PriorityOrder,  
      boost::intrusive::bhtraits<Test, boost::intrusive::tree_node_traits<void  
      *>, boost::intrusive::safe_link, boost::intrusive::dft_tag, 6>,  
      StringKey>::operator()<std::__1::basic_string<char>, const  
      boost::intrusive::tree_node<void *> *>' requested here  
      while(upnode != header && pcomp(k, upnode)){  
                                ^  
/usr/local/include/boost-1_69/boost/intrusive/treap_algorithms.hpp:493:10: note:   
      in instantiation of function template specialization  
      'boost::intrusive::treap_algorithms<boost::intrusive::tree_node_traits<void  
      *> >::rebalance_after_insertion_check<std::__1::basic_string<char>,  
      boost::intrusive::detail::key_nodeptr_comp<PriorityOrder,  
      boost::intrusive::bhtraits<Test, boost::intrusive::tree_node_traits<void  
      *>, boost::intrusive::safe_link, boost::intrusive::dft_tag, 6>, StringKey>  
      >' requested here  
         rebalance_after_insertion_check(header, commit_data.node, key, ...  
         ^  
/usr/local/include/boost-1_69/boost/intrusive/treap.hpp:662:28: note: in  
      instantiation of function template specialization  
      'boost::intrusive::treap_algorithms<boost::intrusive::tree_node_traits<void  
      *> >::insert_unique_check<std::__1::basic_string<char>,  
      boost::intrusive::detail::key_nodeptr_comp<std::__1::less<std::__1::basic_string<char>  
      >, boost::intrusive::bhtraits<Test,  
      boost::intrusive::tree_node_traits<void *>, boost::intrusive::safe_link,  
      boost::intrusive::dft_tag, 6>, StringKey>,  
      boost::intrusive::detail::key_nodeptr_comp<PriorityOrder,  
      boost::intrusive::bhtraits<Test, boost::intrusive::tree_node_traits<void  
      *>, boost::intrusive::safe_link, boost::intrusive::dft_tag, 6>, StringKey>  
      >' requested here  
         (node_algorithms::insert_unique_check  
                           ^  
/usr/local/include/boost-1_69/boost/intrusive/treap.hpp:585:20: note: in  
      instantiation of function template specialization  
      'boost::intrusive::treap_impl<boost::intrusive::bhtraits<Test,  
      boost::intrusive::tree_node_traits<void *>, boost::intrusive::safe_link,  
      boost::intrusive::dft_tag, 6>, StringKey, void, PriorityOrder, unsigned  
      long, true, void>::insert_unique_check<std::__1::basic_string<char>,  
      std::__1::less<std::__1::basic_string<char> >, PriorityOrder>' requested  
      here  
   {  return this->insert_unique_check(key, this->key_comp(), this->priv...  
                   ^  
/usr/local/include/boost-1_69/boost/intrusive/treap.hpp:499:45: note: in  
      instantiation of member function  
      'boost::intrusive::treap_impl<boost::intrusive::bhtraits<Test,  
      boost::intrusive::tree_node_traits<void *>, boost::intrusive::safe_link,  
      boost::intrusive::dft_tag, 6>, StringKey, void, PriorityOrder, unsigned  
      long, true, void>::insert_unique_check' requested here  
      std::pair<iterator, bool> ret = this->insert_unique_check(key_of_v...  
                                            ^  
/usr/local/include/boost-1_69/boost/intrusive/treap_set.hpp:240:25: note: in  
      instantiation of member function  
      'boost::intrusive::treap_impl<boost::intrusive::bhtraits<Test,  
      boost::intrusive::tree_node_traits<void *>, boost::intrusive::safe_link,  
      boost::intrusive::dft_tag, 6>, StringKey, void, PriorityOrder, unsigned  
      long, true, void>::insert_unique' requested here  
   {  return tree_type::insert_unique(value);  }  
                        ^  
jan.cpp:44:7: note: in instantiation of member function  
      'boost::intrusive::treap_set_impl<boost::intrusive::bhtraits<Test,  
      boost::intrusive::tree_node_traits<void *>, boost::intrusive::safe_link,  
      boost::intrusive::dft_tag, 6>, StringKey, void, PriorityOrder, unsigned  
      long, true, void>::insert' requested here  
    s.insert(t1);  
      ^  
jan.cpp:24:10: note: candidate function not viable: no known conversion from  
      'const boost::intrusive::tree_value_compare<Test *, PriorityOrder,  
      StringKey, bool, false>::key_type' (aka 'const basic_string<char,  
      char_traits<char>, allocator<char> >') to 'const Test' for 1st argument  
    bool operator()(Test const& lhs, Test const& rhs) const {  
         ^  
1 error generated.  
```

---

## Comment 1

> Username: jm-mikkelsen  
> Created at: 2019-02-03 13:31:32 UTC  
> Url: https://github.com/boostorg/intrusive/issues/35#issuecomment-460051974  

Looking further, this seems to have been introduced in https://github.com/boostorg/intrusive/commit/4cf38987a88e1bb1157f1b8957bc0fc167b576e4  
  
The priority comparison function changed from being defined on `T` to being defined on `key_type`. Surely this is a mistake?  
  
To go down this path, it seems like there should he `priority_of_value` option to match `key_of_value`.

---

## Comment 2

> Username: jm-mikkelsen  
> Created at: 2019-02-03 15:21:20 UTC  
> Url: https://github.com/boostorg/intrusive/issues/35#issuecomment-460060708  

I have a possible fix in commit https://github.com/jm-mikkelsen/intrusive/commit/d3bd80d0225721b1bf60c8bcd5a07a32784f0736

---

## Comment 3

> Username: igaztanaga  
> Created at: 2019-02-06 21:41:11 UTC  
> Url: https://github.com/boostorg/intrusive/issues/35#issuecomment-461198753  

Thanks for the report. I previously thought that priority should always go with key_type, but maybe you are right, the correct approach would to have a priority_of_value option. The default could to use value_type for priority comparisons.

---

## Comment 4

> Username: jm-mikkelsen  
> Created at: 2019-02-06 23:01:44 UTC  
> Url: https://github.com/boostorg/intrusive/issues/35#issuecomment-461223076  

My pull request is after my priority_of_value thought ... It is much more simple.

---

## Comment 5

> Username: igaztanaga  
> Created at: 2019-02-07 16:50:34 UTC  
> Updated at: 2019-02-07 16:57:18 UTC  
> Url: https://github.com/boostorg/intrusive/issues/35#issuecomment-461507121  

You commit seems to fail in integration tests (https://travis-ci.org/boostorg/intrusive/builds/489375350?utm_source=github_status&utm_medium=notification).

---

## Comment 6

> Username: jm-mikkelsen  
> Created at: 2019-02-08 14:07:18 UTC  
> Updated at: 2019-02-08 14:07:33 UTC  
> Url: https://github.com/boostorg/intrusive/issues/35#issuecomment-461813542  

Yes, it does. I did not consider `insert_check()`.  
  
The underlying problem is that `insert_unique_check()` in `treap_algorithms.hpp` calls `rebalance_after_insertion_checks()` if the insertion is successful, which requires a full value object because priority comparison is defined in terms of a reference to the full value object, not just the key.  
  
The changes to `insert_unique_check()` are obviously wrong when I consider how they are used from `insert_unique()`. It seems to me that the call at to `rebalance_after_insertion_checks` can be moved to the beginning of `treap_algortihms::insert_unique_commit()`. This should be OK because `commit_data` only remains valid while there are no insertions or deletions in the tree.  
  
Does this sound reasonable?

---

## Comment 7

> Username: jm-mikkelsen  
> Created at: 2019-02-11 10:39:49 UTC  
> Url: https://github.com/boostorg/intrusive/issues/35#issuecomment-462282967  

I updated the pull request to implement `priority_of_value` with the same defaults as `key_of_value`. The default uses `value_type`.  
  
This issue with this patch at the moment is that the `sizeof(treap)` tests fail because I added a member variable to avoid an ambiguous base class. It is it possible for the value comparison and the priority comparison base classes to have the same type. Adding a wrapper and using a base class should resolve this issue, but I wanted to check that this basic approach was OK before going further.

---

## Comment 8

> Username: igaztanaga  
> Created at: 2019-02-11 23:31:08 UTC  
> Url: https://github.com/boostorg/intrusive/issues/35#issuecomment-462538411  

Many thanks for the patch. Based on your patch I've prepared commit:  
  
https://github.com/boostorg/intrusive/commit/3618260489cffdea7a2457de40ed4f8b0992762e  
  
The main change is that some "insert_check/insert_unique_check" overloads need an additional priority parameter apart from the priority comparison, since the priority is a different attribute that can be obtained from a different method. By default priority_of_value is the identity operation, so your test case now compiles fine.  
  
Using priority_of_value your example would become:  
  
```  
#include <boost/intrusive/treap_set.hpp>  
#include <string>  
  
using namespace boost::intrusive;  
  
class Test : public bs_set_base_hook<>  
{  
public:  
    Test(int priority, std::string key) :  
	m_priority(priority), m_key(std::move(key))  
    {  
    }  
  
    int priority() const { return m_priority; }  
    std::string const& key() const { return m_key; }  
  
private:  
    int m_priority;  
    std::string m_key;  
};  
  
struct PriorityOrder  
{  
    bool operator()(int const& lhs, int const& rhs) const {  
	return lhs < rhs;  
    }  
};  
  
struct StringKey  
{  
    using type = std::string;  
  
    type const& operator()(Test const& v) const {  
	return v.key();  
    }  
};  
  
struct IntPrio  
{  
    using type = int;  
  
    type operator()(Test const& v) const {  
	   return v.priority();  
    }  
};  
  
int main()  
{  
    Test t1(1, "hello");  
    treap_set<Test, key_of_value<StringKey>, priority_of_value<IntPrio>, priority<PriorityOrder>> s;  
    s.insert(t1);  
}  
```

---

## Comment 9

> Username: jm-mikkelsen  
> Created at: 2019-02-12 06:57:10 UTC  
> Url: https://github.com/boostorg/intrusive/issues/35#issuecomment-462639982  

Thanks for making the change. You didn't take the tagging `ebo_functor_holder`. That breaks code where the key comparison and the priority comparison are the same type. I will open another issue.
