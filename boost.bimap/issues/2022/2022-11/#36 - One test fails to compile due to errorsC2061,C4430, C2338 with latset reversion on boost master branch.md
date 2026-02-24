# #36 - One test fails to compile due to errorsC2061,C4430, C2338 with latset reversion on boost master branch [Closed]

> Username: nnfdnkns  
> Created at: 2022-11-03 09:36:38 UTC  
> Updated at: 2022-11-03 17:43:27 UTC  
> Closed at: 2022-11-03 17:43:26 UTC  
> Url: https://github.com/boostorg/bimap/issues/36  



---

## Comment 1

> Username: pdimov  
> Created at: 2022-11-03 09:51:42 UTC  
> Url: https://github.com/boostorg/bimap/issues/36#issuecomment-1301936745  

The failure is in the Bimap tests, not JSON tests.  
  
The compile errors you list are correct; they occur in compile-fail tests. The failure is in another test:  
```  
...failed testing.capture-output ..\..\bin.v2\libs\bimap\test\test_bimap_unordered.test\msvc-14.3\debug\threading-multi\test_bimap_unordered.run...  
```  
and is probably caused by recent changes to Unordered. I'll transfer the issue there.

---

## Comment 2

> Username: nnfdnkns  
> Created at: 2022-11-03 09:57:15 UTC  
> Url: https://github.com/boostorg/bimap/issues/36#issuecomment-1301936747  

sorry,Thank you for reminding me

---

## Comment 3

> Username: pdimov  
> Created at: 2022-11-03 11:04:03 UTC  
> Url: https://github.com/boostorg/bimap/issues/36#issuecomment-1301937552  

```  
testing.capture-output ..\..\bin.v2\libs\bimap\test\test_bimap_unordered.test\msvc-14.3\debug\threading-multi\test_bimap_unordered.run  
====== BEGIN OUTPUT ======  
C:\boost-git\develop\libs/bimap/test/test_bimap.hpp(419): test 'const_c.bucket_size(const_c.bucket(di->first)) == 1' ('2' == '1') failed in function 'void __cdecl test_pair_unordered_associative_container<class boost::bimaps::views::unordered_multimap_view<struct boost::bimaps::relation::member_at::right,class boost::bimaps::detail::bimap_core<struct boost::bimaps::unordered_set_of<char,struct boost::hash<char>,struct std::equal_to<char> >,struct boost::bimaps::unordered_multiset_of<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,struct boost::hash<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,struct std::equal_to<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > > >,struct boost::mpl::na,struct boost::mpl::na,struct boost::mpl::na> >,class std::map<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,char,struct std::less<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,class std::allocator<struct std::pair<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const ,char> > >>(class boost::bimaps::views::unordered_multimap_view<struct boost::bimaps::relation::member_at::right,class boost::bimaps::detail::bimap_core<struct boost::bimaps::unordered_set_of<char,struct boost::hash<char>,struct std::equal_to<char> >,struct boost::bimaps::unordered_multiset_of<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,struct boost::hash<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,struct std::equal_to<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > > >,struct boost::mpl::na,struct boost::mpl::na,struct boost::mpl::na> > &,const class std::map<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,char,struct std::less<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,class std::allocator<struct std::pair<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const ,char> > > &)'  
C:\boost-git\develop\libs/bimap/test/test_bimap.hpp(419): test 'const_c.bucket_size(const_c.bucket(di->first)) == 1' ('2' == '1') failed in function 'void __cdecl test_pair_unordered_associative_container<class boost::bimaps::views::unordered_multimap_view<struct boost::bimaps::relation::member_at::right,class boost::bimaps::detail::bimap_core<struct boost::bimaps::unordered_set_of<char,struct boost::hash<char>,struct std::equal_to<char> >,struct boost::bimaps::unordered_multiset_of<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,struct boost::hash<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,struct std::equal_to<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > > >,struct boost::mpl::na,struct boost::mpl::na,struct boost::mpl::na> >,class std::map<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,char,struct std::less<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,class std::allocator<struct std::pair<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const ,char> > >>(class boost::bimaps::views::unordered_multimap_view<struct boost::bimaps::relation::member_at::right,class boost::bimaps::detail::bimap_core<struct boost::bimaps::unordered_set_of<char,struct boost::hash<char>,struct std::equal_to<char> >,struct boost::bimaps::unordered_multiset_of<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,struct boost::hash<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,struct std::equal_to<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > > >,struct boost::mpl::na,struct boost::mpl::na,struct boost::mpl::na> > &,const class std::map<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,char,struct std::less<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,class std::allocator<struct std::pair<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const ,char> > > &)'  
C:\boost-git\develop\libs/bimap/test/test_bimap.hpp(419): test 'const_c.bucket_size(const_c.bucket(di->first)) == 1' ('2' == '1') failed in function 'void __cdecl test_pair_unordered_associative_container<class boost::bimaps::views::unordered_multimap_view<struct right_tag,class boost::bimaps::detail::bimap_core<struct boost::bimaps::unordered_set_of<struct boost::bimaps::tags::tagged<char,struct left_tag>,struct boost::hash<char>,struct std::equal_to<char> >,struct boost::bimaps::unordered_multiset_of<struct boost::bimaps::tags::tagged<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,struct right_tag>,struct boost::hash<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,struct std::equal_to<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > > >,struct boost::mpl::na,struct boost::mpl::na,struct boost::mpl::na> >,class std::map<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,char,struct std::less<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,class std::allocator<struct std::pair<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const ,char> > >>(class boost::bimaps::views::unordered_multimap_view<struct right_tag,class boost::bimaps::detail::bimap_core<struct boost::bimaps::unordered_set_of<struct boost::bimaps::tags::tagged<char,struct left_tag>,struct boost::hash<char>,struct std::equal_to<char> >,struct boost::bimaps::unordered_multiset_of<struct boost::bimaps::tags::tagged<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,struct right_tag>,struct boost::hash<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,struct std::equal_to<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > > >,struct boost::mpl::na,struct boost::mpl::na,struct boost::mpl::na> > &,const class std::map<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,char,struct std::less<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,class std::allocator<struct std::pair<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const ,char> > > &)'  
C:\boost-git\develop\libs/bimap/test/test_bimap.hpp(419): test 'const_c.bucket_size(const_c.bucket(di->first)) == 1' ('2' == '1') failed in function 'void __cdecl test_pair_unordered_associative_container<class boost::bimaps::views::unordered_multimap_view<struct right_tag,class boost::bimaps::detail::bimap_core<struct boost::bimaps::unordered_set_of<struct boost::bimaps::tags::tagged<char,struct left_tag>,struct boost::hash<char>,struct std::equal_to<char> >,struct boost::bimaps::unordered_multiset_of<struct boost::bimaps::tags::tagged<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,struct right_tag>,struct boost::hash<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,struct std::equal_to<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > > >,struct boost::mpl::na,struct boost::mpl::na,struct boost::mpl::na> >,class std::map<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,char,struct std::less<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,class std::allocator<struct std::pair<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const ,char> > >>(class boost::bimaps::views::unordered_multimap_view<struct right_tag,class boost::bimaps::detail::bimap_core<struct boost::bimaps::unordered_set_of<struct boost::bimaps::tags::tagged<char,struct left_tag>,struct boost::hash<char>,struct std::equal_to<char> >,struct boost::bimaps::unordered_multiset_of<struct boost::bimaps::tags::tagged<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,struct right_tag>,struct boost::hash<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,struct std::equal_to<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > > >,struct boost::mpl::na,struct boost::mpl::na,struct boost::mpl::na> > &,const class std::map<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,char,struct std::less<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,class std::allocator<struct std::pair<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const ,char> > > &)'  
4 errors detected.  
  
EXIT STATUS: 4  
====== END OUTPUT ======  
```

---

## Comment 4

> Username: pdimov  
> Created at: 2022-11-03 17:43:26 UTC  
> Url: https://github.com/boostorg/bimap/issues/36#issuecomment-1302461593  

Duplicate of #35.
