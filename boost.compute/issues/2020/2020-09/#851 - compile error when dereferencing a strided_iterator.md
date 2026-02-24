# #851 - compile error when dereferencing a strided_iterator [Open]

> Username: michael-brade  
> Created at: 2020-09-24 16:41:51 UTC  
> Updated at: 2020-09-24 16:47:14 UTC  
> Url: https://github.com/boostorg/compute/issues/851  

It is not possible to compile a program that dereferences a `strided_iterator`:  
```  
boost::compute::int_ data[] = { 1, 2, 3, 4, 5, 6, 7, 8 };  
auto it = boost::compute::make_strided_iterator(data, 3);  
BOOST_CHECK_EQUAL(*it, 1);  
```  
That results in:  
```  
test_strided_iterator.cpp:22:  
/compute/include/boost/compute/iterator/strided_iterator.hpp: In instantiation of ‘boost::compute::strided_iterator<Iterator>::reference boost::compute::strided_iterator<Iterator>::dereference() const [with Iterator = int*; boost::compute::strided_iterator<Iterator>::reference = int&]’:  
/usr/include/boost/iterator/iterator_facade.hpp:550:31:   required from ‘static typename Facade::reference boost::iterators::iterator_core_access::dereference(const Facade&) [with Facade = boost::compute::strided_iterator<int*>; typename Facade::reference = int&]’  
/usr/include/boost/iterator/iterator_facade.hpp:656:53:   required from ‘boost::iterators::detail::iterator_facade_base<Derived, Value, CategoryOrTraversal, Reference, Difference, false, false>::reference boost::iterators::detail::iterator_facade_base<Derived, Value, CategoryOrTraversal, Reference, Difference, false, false>::operator*() const [with Derived = boost::compute::strided_iterator<int*>; Value = int; CategoryOrTraversal = std::random_access_iterator_tag; Reference = int&; Difference = long int; boost::iterators::detail::iterator_facade_base<Derived, Value, CategoryOrTraversal, Reference, Difference, false, false>::reference = int&]’  
/compute/test/test_strided_iterator.cpp:120:3:   required from here  
/compute/include/boost/compute/iterator/strided_iterator.hpp:187:16: error: value-initialization of reference type ‘boost::compute::strided_iterator<int*>::reference’ {aka ‘int&’}  
  187 |         return reference();  
      |                ^~~~~~~~~~~  
make[3]: *** [test/CMakeFiles/test_strided_iterator.dir/build.make:82: test/CMakeFiles/test_strided_iterator.dir/test_strided_iterator.cpp.o] Fehler 1  
make[2]: *** [CMakeFiles/Makefile2:3532: test/CMakeFiles/test_strided_iterator.dir/all] Fehler 2  
```

---

## Comment 1

> Username: michael-brade  
> Created at: 2020-09-24 16:47:14 UTC  
> Url: https://github.com/boostorg/compute/issues/851#issuecomment-698461598  

I will create a pull request in the next hour.
