# #301 UB comparing unrelated pointers in priority_order [Closed]

> Username: ecatmur  
> Created at: 2021-02-15 10:36:37 UTC  
> Updated at: 2022-03-02 09:44:11 UTC  
> Closed at: 2022-03-02 09:44:01 UTC  
> Url: https://github.com/boostorg/test/pull/301  

Detected by asan (-fsanitize=pointer-compare) with gcc10:  
  
==863==ERROR: AddressSanitizer: invalid-pointer-pair: 0x7ffe21c831b0 0x00000c26f470  
    #0 0x2688702 in boost::unit_test::framework::state::priority_order::operator()(boost::unit_test::test_observer*, boost::unit_test::test_observer*) const libs/test/include/boost/test/impl/framework.ipp:865  
    #1 0x2688702 in std::_Rb_tree<boost::unit_test::test_observer*, boost::unit_test::test_observer*, std::_Identity<boost::unit_test::test_observer*>, boost::unit_test::framework::state::priority_order, std::allocator<boost::unit_test::test_observer*> >::_M_get_insert_unique_pos(boost::unit_test::test_observer* const&) /opt/gcc-10.2.0/include/c++/10.2.0/bits/stl_tree.h:2101  
    #2 0x268aafb in std::pair<std::_Rb_tree_iterator<boost::unit_test::test_observer*>, bool> std::_Rb_tree<boost::unit_test::test_observer*, boost::unit_test::test_observer*, std::_Identity<boost::unit_test::test_observer*>, boost::unit_test::framework::state::priority_order, std::allocator<boost::unit_test::test_observer*> >::_M_insert_unique<boost::unit_test::test_observer*>(boost::unit_test::test_observer*&&) /opt/gcc-10.2.0/include/c++/10.2.0/bits/stl_tree.h:2154  
    #3 0x2614ac4 in std::set<boost::unit_test::test_observer*, boost::unit_test::framework::state::priority_order, std::allocator<boost::unit_test::test_observer*> >::insert(boost::unit_test::test_observer*&&) /opt/gcc-10.2.0/include/c++/10.2.0/bits/stl_set.h:521  
    #4 0x2614ac4 in boost::unit_test::framework::register_observer(boost::unit_test::test_observer&) libs/test/include/boost/test/impl/framework.ipp:1380  
    #5 0x2642b56 in boost::unit_test::framework::register_observer_helper::register_obs() libs/test/include/boost/test/impl/framework.ipp:1627  
    #6 0x2642b56 in boost::unit_test::framework::run(unsigned long, bool) libs/test/include/boost/test/impl/framework.ipp:1731  
    #7 0x27f6c77 in boost::unit_test::unit_test_main(boost::unit_test::test_suite* (*)(int, char**), int, char**) libs/test/include/boost/test/impl/unit_test_main.ipp:250  
    #8 0x27f9495 in main libs/test/include/boost/test/impl/unit_test_main.ipp:306  
    #9 0x7f8d8b3c1554 in __libc_start_main ../csu/libc-start.c:266  
    #10 0x568e0d  (test+0x568e0d)

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2022-03-02 09:44:01 UTC  
> Url: https://github.com/boostorg/test/pull/301#issuecomment-1056699899  

Merged through #329 rev a9052b3

---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2022-03-02 09:44:11 UTC  
> Url: https://github.com/boostorg/test/pull/301#issuecomment-1056700173  

Thanks for the patch!

---
