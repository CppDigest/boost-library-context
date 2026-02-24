# #38 - treap: Same type for priority and key comparison leads to ambiguous base class error [Closed]

> Username: jm-mikkelsen  
> Created at: 2019-02-12 07:13:40 UTC  
> Updated at: 2019-02-12 22:21:08 UTC  
> Closed at: 2019-02-12 22:20:56 UTC  
> Url: https://github.com/boostorg/intrusive/issues/38  

Using the same type for priority and key comparison leads to an ambiguous base class error.  
  
A fix for this issue is in my pull request #37, which added a type tag to `ebo_functor_holder`.  
  
Also note that when the key/priority comparison types are the same, the size of the class may increase by one word because the two empty base classes need distinct addresses. This is possibly worth mentioning in the documentation because this is a case where the size might be larger than expected.  
  
With my testing, the extra word is only allocated when `constant_time_size<false>` is used. My testing was on "Apple LLVM version 10.0.0 (clang-1000.11.45.5)", I haven't tried other compilers/platforms yet.  
  
For example:  
  
```  
#include <boost/intrusive/treap_set.hpp>  
  
using namespace boost::intrusive;  
  
class Test : public bs_set_base_hook<>  
{  
public:  
    Test(int priority, int key) :  
	m_priority(priority), m_key(key)  
    {  
    }  
  
    int priority() const { return m_priority; }  
    int key() const { return m_key; }  
  
private:  
    int m_priority;  
    int m_key;  
};  
  
struct PriorityOfValue  
{  
    using type = int;  
  
    type operator()(Test const& v) const {  
	return v.priority();  
    }  
};  
  
struct KeyOfValue  
{  
    using type = int;  
  
    int operator()(Test const& v) const {  
	return v.key();  
    }  
};  
  
int main()  
{  
    Test t1(1, 2);  
  
    treap_set<  
	Test,  
	key_of_value<KeyOfValue>,  
	priority_of_value<PriorityOfValue>,  
	priority<std::less<int>>,  
	compare<std::less<int>>  
    > s;  
  
    s.insert(t1);  
}  
```  
  
Gives this error Xcode clang:  
  
```  
In file included from less.cpp:1:  
In file included from /Users/janm/Builds/boost_1_69_0/boost/intrusive/treap_set.hpp:17:  
/Users/janm/Builds/boost_1_69_0/boost/intrusive/treap.hpp:161:38: error:   
      ambiguous conversion from derived class  
      'boost::intrusive::treap_impl<boost::intrusive::bhtraits<Test,  
      boost::intrusive::tree_node_traits<void *>, boost::intrusive::safe_link,  
      boost::intrusive::dft_tag, 6>, KeyOfValue, std::__1::less<int>,  
      PriorityOfValue, std::__1::less<int>, unsigned long, true, void>' to base  
      class 'boost::intrusive::treap_impl<boost::intrusive::bhtraits<Test,  
      boost::intrusive::tree_node_traits<void *>, boost::intrusive::safe_link,  
      boost::intrusive::dft_tag, 6>, KeyOfValue, std::__1::less<int>,  
      PriorityOfValue, std::__1::less<int>, unsigned long, true,  
      void>::prio_base' (aka 'ebo_functor_holder<std::__1::less<int> >'):  
    class boost::intrusive::treap_impl<struct boost::intrusive::bhtraits<class Test, struct boost::intrusive::tree_node_traits<void *>, boost::intrusive::safe_link, struct boost::intrusive::dft_tag, 6>, struct KeyOfValue, struct std::__1::less<int>, struct PriorityOfValue, struct std::__1::less<int>, unsigned long, true, void> -> bstree_impl<struct boost::intrusive::bhtraits<class Test, struct boost::intrusive::tree_node_traits<void *>, boost::intrusive::safe_link, struct boost::intrusive::dft_tag, 6>, struct KeyOfValue, struct std::__1::less<int>, unsigned long, true, BsTreeAlgorithms, void> -> bstbase<struct boost::intrusive::bhtraits<class Test, struct boost::intrusive::tree_node_traits<void *>, boost::intrusive::safe_link, struct boost::intrusive::dft_tag, 6>, struct KeyOfValue, struct std::__1::less<int>, true, unsigned long, (enum boost::intrusive::algo_types)4U, void> -> bstbase_hack<struct boost::intrusive::bhtraits<class Test, struct boost::intrusive::tree_node_traits<void *>, boost::intrusive::safe_link, struct boost::intrusive::dft_tag, 6>, struct KeyOfValue, struct std::__1::less<int>, true, unsigned long, (enum boost::intrusive::algo_types)4U, void> -> bstbase2<struct boost::intrusive::bhtraits<class Test, struct boost::intrusive::tree_node_traits<void *>, boost::intrusive::safe_link, struct boost::intrusive::dft_tag, 6>, struct KeyOfValue, struct std::__1::less<int>, (enum boost::intrusive::algo_types)4U, void> -> detail::ebo_functor_holder<typename bst_key_types<typename bhtraits<Test, tree_node_traits<void *>, boost::intrusive::safe_link, dft_tag, 6>::pointer, KeyOfValue, less<int> >::value_compare> -> struct boost::intrusive::tree_value_compare<class Test *, struct std::__1::less<int>, struct KeyOfValue, _Bool, false> -> boost::intrusive::detail::ebo_functor_holder<less<int> >  
    class boost::intrusive::treap_impl<struct boost::intrusive::bhtraits<class Test, struct boost::intrusive::tree_node_traits<void *>, boost::intrusive::safe_link, struct boost::intrusive::dft_tag, 6>, struct KeyOfValue, struct std::__1::less<int>, struct PriorityOfValue, struct std::__1::less<int>, unsigned long, true, void> -> detail::ebo_functor_holder<typename treap_prio_types<typename bhtraits<Test, tree_node_traits<void *>, boost::intrusive::safe_link, dft_tag, 6>::pointer, PriorityOfValue, less<int> >::priority_compare>  
   {  return static_cast<prio_base&>(*this).get();  }  
                                     ^~~~~  
/Users/janm/Builds/boost_1_69_0/boost/intrusive/treap.hpp:601:75: note: in  
      instantiation of member function  
      'boost::intrusive::treap_impl<boost::intrusive::bhtraits<Test,  
      boost::intrusive::tree_node_traits<void *>, boost::intrusive::safe_link,  
      boost::intrusive::dft_tag, 6>, KeyOfValue, std::__1::less<int>,  
      PriorityOfValue, std::__1::less<int>, unsigned long, true,  
      void>::priv_pcomp' requested here  
  ...this->insert_unique_check(key, this->key_comp(), prio, this->priv_pcomp(...  
                                                                  ^  
/Users/janm/Builds/boost_1_69_0/boost/intrusive/treap.hpp:515:45: note: in  
      instantiation of member function  
      'boost::intrusive::treap_impl<boost::intrusive::bhtraits<Test,  
      boost::intrusive::tree_node_traits<void *>, boost::intrusive::safe_link,  
      boost::intrusive::dft_tag, 6>, KeyOfValue, std::__1::less<int>,  
      PriorityOfValue, std::__1::less<int>, unsigned long, true,  
      void>::insert_unique_check' requested here  
      std::pair<iterator, bool> ret = this->insert_unique_check(key_of_v...  
                                            ^  
/Users/janm/Builds/boost_1_69_0/boost/intrusive/treap_set.hpp:241:25: note: in  
      instantiation of member function  
      'boost::intrusive::treap_impl<boost::intrusive::bhtraits<Test,  
      boost::intrusive::tree_node_traits<void *>, boost::intrusive::safe_link,  
      boost::intrusive::dft_tag, 6>, KeyOfValue, std::__1::less<int>,  
      PriorityOfValue, std::__1::less<int>, unsigned long, true,  
      void>::insert_unique' requested here  
   {  return tree_type::insert_unique(value);  }  
                        ^  
less.cpp:57:7: note: in instantiation of member function  
      'boost::intrusive::treap_set_impl<boost::intrusive::bhtraits<Test,  
      boost::intrusive::tree_node_traits<void *>, boost::intrusive::safe_link,  
      boost::intrusive::dft_tag, 6>, KeyOfValue, std::__1::less<int>,  
      PriorityOfValue, std::__1::less<int>, unsigned long, true, void>::insert'  
      requested here  
    s.insert(t1);  
      ^  
1 error generated.  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2019-02-12 22:21:08 UTC  
> Url: https://github.com/boostorg/intrusive/issues/38#issuecomment-462960470  

Many thanks for the report.
