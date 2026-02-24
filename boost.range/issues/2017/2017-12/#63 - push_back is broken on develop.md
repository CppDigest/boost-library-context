# #63 - push_back is broken on develop [Closed]

> Username: Kojoley  
> Created at: 2017-12-29 21:38:04 UTC  
> Updated at: 2019-05-23 11:00:08 UTC  
> Closed at: 2019-05-23 11:00:08 UTC  
> Url: https://github.com/boostorg/range/issues/63  

Since d99628981d1ff7181bee652bc5177342d79aaf4e commit `boost::push_back` corrupts heap on a simple example:  
  
```cpp  
#include <boost/range/adaptor/transformed.hpp>  
#include <boost/range/adaptor/uniqued.hpp>  
#include <boost/range/algorithm_ext/push_back.hpp>  
  
#include <vector>  
#include <string>  
  
int main()  
{  
    std::vector<std::string> source;  
    source.push_back("Foo");  
    source.push_back("Bar");  
    source.push_back("Baz");  
    source.push_back("Baz");  
  
    std::vector<std::string> result;  
  
    using namespace boost::adaptors;  
  
    boost::push_back(result, source | uniqued);  
  
    return 0;  
}  
```  
  
![image](https://user-images.githubusercontent.com/2743474/34447806-6f8786b8-ecf8-11e7-8784-6f8fa9643a80.png)  
  
<details>  
  <summary>DrMemory log</summary>  
  
```  
Dr. Memory version 1.11.0 build 2 built on Aug 29 2016 02:41:18  
Dr. Memory results for pid 244: "boost_workbench.exe"  
Application cmdline: "boost_workbench.exe"  
Recorded 117 suppression(s) from default C:\Program Files\DrMemory-Windows-1.11.0-2\bin64\suppress-default.txt  
  
Error #1: UNADDRESSABLE ACCESS beyond heap bounds: writing 0x0000000000f809f8-0x0000000000f80a00 8 byte(s)  
# 0 std::_Container_base12::_Container_base12                   [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\xutility:109]  
# 1 std::_String_val<>::_String_val<>                           [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\xstring:1565]  
# 2 std::_Compressed_pair<>::_Compressed_pair<><>               [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\xutility:320]  
# 3 std::_String_alloc<>::_String_alloc<><>                     [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\xstring:1720]  
# 4 std::basic_string<>::basic_string<>                         [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\xstring:2038]  
# 5 std::_Default_allocator_traits<>::construct<>               [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\xmemory0:944]  
# 6 std::_Uninitialized_copy_al_unchecked<>                     [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\xmemory:92]  
# 7 std::_Uninitialized_copy<>                                  [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\xmemory:122]  
# 8 std::vector<>::_Ucopy<>                                     [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\vector:1913]  
# 9 std::vector<>::_Insert_range<>                              [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\vector:1207]  
#10 std::vector<>::insert<>                                     [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\vector:1311]  
#11 boost::range_detail::push_back_impl<>                       [...\boost\range\algorithm_ext\push_back.hpp:33]  
#12 boost::range::push_back<>                                   [...\boost\range\algorithm_ext\push_back.hpp:71]  
#13 main                                                        [...\main.cpp:20]  
Note: @0:00:00.181 in thread 12640  
Note: refers to 0 byte(s) beyond last valid byte in prior malloc  
Note: prev lower malloc:  0x0000000000f80980-0x0000000000f809f8  
Note: allocated here:  
Note: # 0 replace_operator_new                 [d:\drmemory_package\common\alloc_replace.c:2899]  
Note: # 1 MSVCP140D.dll!std::_Lockit::~_Lockit [f:\dd\vctools\crt\crtw32\stdcpp\xlock.cpp:76]  
Note: # 2 KERNEL32.dll!BaseThreadInitThunk  
Note: instruction: mov    $0x0000000000000000 -> (%rax)  
  
Error #2: UNADDRESSABLE ACCESS beyond heap bounds: writing 0x0000000000f80a00-0x0000000000f80a04 4 byte(s)  
# 0 replace_memmove                                                            [d:\drmemory_package\drmemory\replace.c:763]  
# 1 std::basic_string<>::_Assign_rv_contents_with_alloc_always_equal           [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\xstring:2137]  
# 2 std::basic_string<>::basic_string<>                                        [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\xstring:2040]  
# 3 std::_Default_allocator_traits<>::construct<>                              [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\xmemory0:944]  
# 4 std::_Uninitialized_copy_al_unchecked<>                                    [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\xmemory:92]  
# 5 std::_Uninitialized_copy<>                                                 [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\xmemory:122]  
# 6 std::vector<>::_Ucopy<>                                                    [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\vector:1913]  
# 7 std::vector<>::_Insert_range<>                                             [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\vector:1207]  
# 8 std::vector<>::insert<>                                                    [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\vector:1311]  
# 9 boost::range_detail::push_back_impl<>                                      [...\boost\range\algorithm_ext\push_back.hpp:33]  
#10 boost::range::push_back<>                                                  [...\boost\range\algorithm_ext\push_back.hpp:71]  
#11 main                                                                       [...\main.cpp:20]  
Note: @0:00:00.207 in thread 12640  
Note: prev lower malloc:  0x0000000000f80980-0x0000000000f809f8  
Note: allocated here:  
Note: # 0 replace_operator_new                 [d:\drmemory_package\common\alloc_replace.c:2899]  
Note: # 1 MSVCP140D.dll!std::_Lockit::~_Lockit [f:\dd\vctools\crt\crtw32\stdcpp\xlock.cpp:76]  
Note: # 2 KERNEL32.dll!BaseThreadInitThunk  
Note: instruction: mov    %ecx -> (%rax)  
  
Error #3: INVALID HEAP ARGUMENT to free 0x0000000000f80a40  
# 0 replace_operator_delete_nothrow                                      [d:\drmemory_package\common\alloc_replace.c:2974]  
# 1 boost::range_detail::skip_iterator<>::~skip_iterator<>  
# 2 std::move_iterator<>::~move_iterator<>  
# 3 std::vector<>::insert<>                                              [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\vector:1312]  
# 4 operator delete                                                      [f:\dd\vctools\crt\vcstartup\src\heap\delete_scalar_size.cpp:30]  
# 5 std::_Default_allocator_traits<>::deallocate                         [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\xmemory0:937]  
# 6 std::_Deallocate_plain<>                                             [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\xmemory0:1318]  
# 7 std::_String_alloc<>::_Free_proxy                                    [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\xstring:1780]  
# 8 std::_String_alloc<>::~_String_alloc<>                               [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\xstring:1728]  
# 9 std::basic_string<>::~basic_string<>                                 [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\xstring:2232]  
#10 std::basic_string<>::`scalar deleting destructor'  
#11 std::_Default_allocator_traits<>::destroy<>                          [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\xmemory0:951]  
#12 std::_Destroy_range1<>                                               [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\xmemory0:1191]  
#13 std::_Destroy_range<>                                                [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\xmemory0:1213]  
#14 std::vector<>::_Destroy                                              [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\vector:1939]  
#15 std::vector<>::_Tidy                                                 [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\vector:2005]  
#16 std::vector<>::~vector<>                                             [c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.12.25827\include\vector:920]  
#17 main                                                                 [...\main.cpp:22]  
Note: @0:00:00.216 in thread 12640  
```  
  
</details>  
   
  
Replacing this check `std::is_lvalue_reference<Range>()` with `std::true_type()` solves the problem.

---

## Comment 1

> Username: Flast  
> Created at: 2018-01-03 14:30:37 UTC  
> Url: https://github.com/boostorg/range/issues/63#issuecomment-355025199  

In `push_back(dst, src | /some-adaptor/)`, `src | /some-adaptor/` is rvlaue but `src` should still lvalue and should not break the range. There is no generic way to detect movable or not-movable from the type, so I think the PR #54 should be reverted.

---

## Comment 2

> Username: Kojoley  
> Created at: 2018-01-03 14:57:32 UTC  
> Url: https://github.com/boostorg/range/issues/63#issuecomment-355031947  

Meh, the dark part of the move semantics. The rvalue says that the object is temporary, but if underlying items could be moved or copied must be decided by donor (moved from object) and not by recipient (actually it could, like removing const, but must not happen implicitly).

---

## Comment 3

> Username: Flast  
> Created at: 2018-01-04 00:31:11 UTC  
> Url: https://github.com/boostorg/range/issues/63#issuecomment-355166918  

#64

---

## Comment 4

> Username: cwecht  
> Created at: 2018-04-03 09:06:12 UTC  
> Url: https://github.com/boostorg/range/issues/63#issuecomment-378181229  

I don't know, if this is supposed to be here, but I think it fits well in the discussion.  
  
In order to make it possible to use move-only value-types in functions such as `push_back` , I would suggest a helper function like this, which makes the move explicit:  
  
```cpp  
template <typename Range>  
auto move_range(Range &&range) {  
  return boost::make_iterator_range(std::make_move_iterator(std::begin(range)),  
                                    std::make_move_iterator(std::end(range)));  
}  
```  
For the sake of simplicity, this is a C++14 implementation, but this should also be implementable with C++11.

---

## Comment 5

> Username: mjendruk  
> Created at: 2019-05-23 08:56:04 UTC  
> Url: https://github.com/boostorg/range/issues/63#issuecomment-495129240  

The changes that caused this bug seem to have been reverted. Can this be closed?

---

## Comment 6

> Username: Kojoley  
> Created at: 2019-05-23 11:00:08 UTC  
> Url: https://github.com/boostorg/range/issues/63#issuecomment-495171873  

Fixed in #64
