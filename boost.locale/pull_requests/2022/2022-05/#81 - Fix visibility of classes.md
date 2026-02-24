# #81 Fix visibility of classes [Merged]

> Username: Flamefire  
> Created at: 2022-05-27 16:17:52 UTC  
> Updated at: 2022-05-28 07:42:15 UTC  
> Merged at: 2022-05-28 07:42:13 UTC  
> Closed at: 2022-05-28 07:42:13 UTC  
> Url: https://github.com/boostorg/locale/pull/81  

UBSAN on OSX reports some invalid downcasts, for example:  
  
```  
runtime error: downcast of address 0x7fdd33c08a40 which does not point to an object of type 'const boost::locale::info'  
0x7fdd33c08a40: note: object is of type 'boost::locale::util::simple_info'  
 dd 7f 00 00  28 ee fe 02 01 00 00 00  00 00 00 00 00 00 00 00  04 65 6e 00 ff 7f 00 00  10 b6 b2 93  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::locale::util::simple_info'  
    #0 0x102edf7a3 in std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > boost::locale::conv::from_utf<char>(char const*, char const*, std::__1::locale const&, boost::locale::conv::method_type)+0x153 (test_std_formatting:x86_64+0x1000207a3)  
    #1 0x102edf5e2 in std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > boost::locale::conv::from_utf<char>(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, std::__1::locale const&, boost::locale::conv::method_type)+0x62 (test_std_formatting:x86_64+0x1000205e2)  
    #2 0x102ec6a2b in void test_by_char<char, char>(std::__1::locale const&, std::__1::locale const&)+0x88b (test_std_formatting:x86_64+0x100007a2b)  
    #3 0x102ec3db7 in main+0xb27 (test_std_formatting:x86_64+0x100004db7)  
    #4 0x7fff6d62acc8 in start+0x0 (libdyld.dylib:x86_64+0x1acc8)  
```  
Note that `boost::locale::util::simple_info` is a subclass of `boost::locale::info`, hence the cast is valid.  
  
In most cases the classes were already correctly decorated with `BOOST_LOCALE_DECL` but as the whole class was defined inline the library and the executable linking to it may get different VTables (i.e. an own copy each which the runtime linker doesn't combine)  
  
So far 2 solutions work:  
  
- `BOOST_SYMBOL_VISIBLE` on the class and `BOOST_LOCALE_DECL` on members as required (e.g. static member variables)  
- `BOOST_LOCALE_DECL` on the class and adding an out-of-line virtual destructor (likely any virtual method out-of-line will do)  
  
@pdimov explained via Slack  
  
> number two is the correct fix, as it emits the vtable in the library  
without it, the vtable is emitted everywhere as a weak definition, and the one in the client isn't VISIBLE  
so the types are considered different by ubsan  
[number two] emits the vtable once, in the library, which is the right thing for classes that are supposed to reside in the library (what DECL implies)  
  
Hence for most classes I made sure they are decorated with `BOOST_LOCALE_DECL` and have a (virtual) destructor defined in one of the libraries source files.  
  
For template classes this doesn't work directly as defining a member requires defining the class specialization. Hence 2 solutions are used (I mostly kept what was used before):  
- Explicitly specialize the template class for the relevant types basically using C&P of the template class body. Then the `BOOST_LOCALE_DECL` + defined (virtual) destructor approach can be used. See e.g. `boundary_indexing` & `converter`  
- Use a (very small, templated) base class containing only the inheritance on `std::locale::facet` and the required `std::locale::id` and use the same approach as above (`BOOST_LOCALE_DECL` + dtor) to get the definitions of the ids then inherit from that and decorate that template class with `BOOST_SYMBOL_VISIBLE` to get (unified) VTables. See `(base_)message_format`  
  
There might be a better solution which avoids that C&P specializations by only instantiating the template and [define the static data member specialization](https://en.cppreference.com/w/cpp/language/template_specialization):  
> An explicit specialization of a static data member of a template is a definition if the declaration includes an initializer; otherwise, it is a declaration. These definitions must use braces for default initialization:   
  
But I can't do that for std::locale::id as it isn't copyable and brace-init is C++11 but we still support C++98. Also I'm not sure what happens to the VTable in that case, i.e. how to define and correctly export/import the virtual destructor (and with it the VTable)

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-05-27 18:29:06 UTC  
> Url: https://github.com/boostorg/locale/pull/81#issuecomment-1139920882  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/81?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#81](https://codecov.io/gh/boostorg/locale/pull/81?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (f89dccf) into [develop](https://codecov.io/gh/boostorg/locale/commit/608e0151745d02492e41f7ce2088be6f95cec1e7?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (608e015) will **decrease** coverage by `0.03%`.  
> The diff coverage is `77.77%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #81      +/-   ##  
===========================================  
- Coverage    80.07%   80.04%   -0.04%       
===========================================  
  Files           76       76                
  Lines         5958     5959       +1       
===========================================  
- Hits          4771     4770       -1       
- Misses        1187     1189       +2       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/81?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/conversion.hpp](https://codecov.io/gh/boostorg/locale/pull/81/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvY29udmVyc2lvbi5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/info.hpp](https://codecov.io/gh/boostorg/locale/pull/81/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvaW5mby5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/util.hpp](https://codecov.io/gh/boostorg/locale/pull/81/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvdXRpbC5ocHA=) | `0.00% <ø> (-12.00%)` | :arrow_down: |  
| [src/util/info.cpp](https://codecov.io/gh/boostorg/locale/pull/81/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3V0aWwvaW5mby5jcHA=) | `76.92% <ø> (ø)` | |  
| [src/shared/ids.cpp](https://codecov.io/gh/boostorg/locale/pull/81/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3NoYXJlZC9pZHMuY3Bw) | `88.88% <66.66%> (-11.12%)` | :arrow_down: |  
| [include/boost/locale/message.hpp](https://codecov.io/gh/boostorg/locale/pull/81/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvbWVzc2FnZS5ocHA=) | `92.90% <100.00%> (-0.10%)` | :arrow_down: |  
| [src/util/codecvt\_converter.cpp](https://codecov.io/gh/boostorg/locale/pull/81/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3V0aWwvY29kZWN2dF9jb252ZXJ0ZXIuY3Bw) | `71.92% <100.00%> (+0.16%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/81?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/81?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [dbeb8f1...f89dccf](https://codecov.io/gh/boostorg/locale/pull/81?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---
