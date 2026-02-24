# #35 - `-Wambiguous-reversed-operator` generated when compiling the test suite under C++20 [Closed]

> Username: cmazakas  
> Created at: 2021-11-18 16:14:10 UTC  
> Updated at: 2021-11-19 02:25:46 UTC  
> Closed at: 2021-11-19 02:25:46 UTC  
> Url: https://github.com/boostorg/unordered/issues/35  

Running:  
```console  
exbigboss@DESKTOP-I99BGOL:~/cpp/boost-root$ ./b2 -a libs/unordered/test cxxstd=20 toolset=clang-12  
```  
generates several warnings of the form:  
```console  
In file included from libs/unordered/test/unordered/assign_tests.cpp:8:  
In file included from ./boost/unordered_set.hpp:17:  
In file included from ./boost/unordered/unordered_set.hpp:20:  
In file included from ./boost/unordered/detail/set.hpp:6:  
./boost/unordered/detail/implementation.hpp:4344:17: warning: ISO C++20 considers use of overloaded operator '==' (with operand types 'test::test_detail::list_iterator<test::object>' and 'test::test_detail::list_iterator<test::object>') to be ambiguous despite there being a unique best viable function [-Wambiguous-reversed-operator]  
          if (i == j)  
              ~ ^  ~  
./boost/unordered/unordered_set.hpp:1714:14: note: in instantiation of function template specialization 'boost::unordered::detail::table<boost::unordered::detail::set<test::cxx11_allocator<test::object, test::propagate_assign>, test::object, test::hash, test::equal_to>>::insert_range_equiv<test::test_detail::list_iterator<test::object>>' requested here  
      table_.insert_range_equiv(first, last);  
             ^  
./boost/unordered/unordered_set.hpp:1559:13: note: in instantiation of function template specialization 'boost::unordered::unordered_multiset<test::object, test::hash, test::equal_to, test::cxx11_allocator<test::object, test::propagate_assign>>::insert<test::test_detail::list_iterator<test::object>>' requested here  
      this->insert(f, l);  
            ^  
libs/unordered/test/unordered/assign_tests.cpp:49:9: note: in instantiation of function template specialization 'boost::unordered::unordered_multiset<test::object, test::hash, test::equal_to, test::cxx11_allocator<test::object, test::propagate_assign>>::unordered_multiset<test::test_detail::list_iterator<test::object>>' requested here  
      T x(v.begin(), v.end());  
        ^  
libs/unordered/test/unordered/assign_tests.cpp:251:18: note: in instantiation of function template specialization 'assign_tests::assign_tests1<boost::unordered::unordered_multiset<test::object, test::hash, test::equal_to, test::cxx11_allocator<test::object, test::propagate_assign>>>' requested here  
  UNORDERED_TEST(assign_tests1,  
                 ^  
libs/unordered/test/unordered/../helpers/./list.hpp:116:12: note: ambiguity is between a regular call to this operator and a call with the argument order reversed  
      bool operator==(const_iterator y) const { return ptr_ == y.ptr_; }  
           ^  
```  
  
You can see similar issues raised in the CI here:  
https://github.com/boostorg/unordered/runs/4216196265?check_suite_focus=true#step:6:5429
