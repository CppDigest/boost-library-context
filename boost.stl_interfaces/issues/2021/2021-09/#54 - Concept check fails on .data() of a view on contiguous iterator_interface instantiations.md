# #54 - Concept check fails on .data() of a view on contiguous iterator_interface instantiations [Closed]

> Username: lubgr  
> Created at: 2021-09-23 21:34:57 UTC  
> Updated at: 2021-12-09 01:11:42 UTC  
> Closed at: 2021-12-09 01:11:42 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/54  

First, let me thank you for this great library! It's a pleasure to use and definitely makes custom iterator and views straightforward to work with!  
  
I have recently hit an issue that I can't properly trace back to me misunderstanding something, a problem with `gcc/clang` with `libstdc++/libc++`, respectively, or a problem of `stl_interfaces`. Here's an illustration:  
```  
struct ConstIntIterator : boost::stl_interfaces::iterator_interface<ConstIntIterator,  
    std::contiguous_iterator_tag, const int> {  
        ConstIntIterator() = default;  
        explicit ConstIntIterator(const int* data) noexcept  
            : data{data} {}  
  
        // Fixes compilation error for gcc + libstdc++:  
        // using element_type = const int;  
  
        const int& operator*() const noexcept { return *data; }  
        ConstIntIterator& operator+=(std::ptrdiff_t n) noexcept { data += n; return *this; }  
        auto operator-(ConstIntIterator other) const noexcept { return data - other.data; }  
  
        const int* data = nullptr;  
    };  
  
struct IntView : boost::stl_interfaces::view_interface<IntView,  
    boost::stl_interfaces::element_layout::contiguous> {  
        auto begin() const noexcept { return first; }  
        auto end() const noexcept { return sentinel; }  
  
        // Fixes compilation error for gcc/libstdc++ and clang/libc++:  
        // auto data() const noexcept { return &*first; }  
  
        // Required for libstdc++ version that haven't implemented  
        // https://wg21.link/P2325R3  
        IntView() = default;  
  
        IntView(const int* first, std::size_t n) noexcept :  
            first{first}, sentinel{first + n} {}  
  
        ConstIntIterator first, sentinel;  
    };  
```  
What doesn't work is accessing `.data()` on the view, e.g.  
```  
const int n = 42;  
IntView view{&n, 1};  
std::printf("%d\n", *view.data());  
```  
The failure occurs with `gcc` (tested 11.2 and trunk) as well as `clang` (tested trunk, with both `libstdc++` and `libc++`), when compiling with `-std=c++20`(which is anyhow required for this example because it uses `std::contiguous_iterator_tag`). In the `gcc` case, it can be fixed by  
  
- providing a distinct `data()` member function for the view class, or  
- adding an `element_type` type alias to the iterator, or  
- adding an `element_type` type alias to `boost::stl_interfaces::iterator_interface`.  
  
For `clang`, it can only be fixed the distinct `data()` member function. Note that using the view base class in the v1 namespace (when passing `std::random_access_iterator_tag` and compiling with `-std=c++17`) always works. Godbolt is [here](https://godbolt.org/z/5j17er3Tv).  
  
Any ideas what the issue might be? Thanks in advance for any help!

---

## Comment 1

> Username: tzlaine  
> Created at: 2021-12-09 01:11:42 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/54#issuecomment-989368638  

Thanks for reporting this, and sorry it took me so long to get to it!  
  
This was a problem in libstdc++, and is now fixed in GCC trunk (if you follow your Godbolt link above, it now works).  Here's the bug:  
  
https://gcc.gnu.org/bugzilla/show_bug.cgi?id=96416
