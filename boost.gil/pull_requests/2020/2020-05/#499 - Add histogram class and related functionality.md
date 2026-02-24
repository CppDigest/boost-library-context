# #499 Add histogram class and related functionality [Merged]

> Username: codejaeger  
> Created at: 2020-05-29 22:33:38 UTC  
> Updated at: 2021-01-24 20:31:04 UTC  
> Merged at: 2021-01-23 23:02:51 UTC  
> Closed at: 2021-01-23 23:02:51 UTC  
> Url: https://github.com/boostorg/gil/pull/499  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
This contains the initial implementation of **histogram** functionality for STL containers for GIL images. Basic tests checking for correctness of histogram have been provided. Usage syntax and distinction between compatible and incompatible containers have been illustrated in a example file.  
  
There was some dicussion regarding the name of the function (which now is **image_histogram**) in the mailing thread ([link](https://lists.boost.org/boost-gil/2020/05/0433.php)). I chose on 'image_histogram' since the name 'histogram' is used as the class name in Boost.Histogram and might cause problems in namespace mix-up. Another option could be **histogram_1d** since these functions are meant for that purpose.  
<!-- What does this pull request do? -->  
  
### Dependency  
  
Optional - Docs for the this implementation are running in a parallel PR #503.  
  
### References  
  
[Discussions](https://lists.boost.org/boost-gil/2020/05/0410.php) on boost-gil mailing threads.  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Provide documentation  
- [x] Ensure all CI builds pass  - R.I.P. Travis CI  
- [x] Review and approve

---

## Review 1 [Changes requested]

> Username: lpranam  
> Created_at: 2020-05-31 08:45:41 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/499#pullrequestreview-421469421  

I have some objections with the code format(template parameters) I don't find the really appealing to the eyes but I maybe wrong. @mloskot what are your suggestions on it!

📁 include/boost/gil/histogram.hpp

```diff
   8 |+ 
   9 |+ #ifndef BOOST_GIL_EXTENSION_HISTOGRAM_HISTOGRAM_HPP
  10 |+ #define BOOST_GIL_EXTENSION_HISTOGRAM_HISTOGRAM_HPP
```

> Username: lpranam  
> Created_at: 2020-05-31 07:36:33 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r432919365  

This macro should be accroding to the file path in this case it should be `BOOST_GIL_HISTOGRAM_HPP`  
  
On the other hand it makes me thing is this the right place for this directly in the main GIL folder! shouldn't it have some histogram directory or maybe in image_processing directory!?

> Username: codejaeger  
> Created_at: 2020-05-31 09:46:27 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r432929046  

Yes, the first is a typo. It was first in the extension directory I forgot to update it later. I was myself not sure of the correct directory for it. Lets wait for others' opinions.

---

📁 include/boost/gil/histogram.hpp

```diff
  81 |+         > : std::true_type {} ;
  82 |+ 
  83 |+ } // namespace detail
```

> Username: lpranam  
> Created_at: 2020-05-31 07:42:06 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r432919764  

I think the right place for this section is in gil/concepts

> Username: codejaeger  
> Created_at: 2020-05-31 09:47:57 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r432929162  

Should I add it to concepts/detail/type_traits.hpp?

---

📁 include/boost/gil/histogram.hpp

```diff
  99 |+                 detail::is_resizable<Container>::value, int>::type = 0
 100 |+ >
 101 |+ inline void image_histogram(SrcView const& srcview, Container &hist)
```

> Username: lpranam  
> Created_at: 2020-05-31 07:51:58 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r432920483  

use `histogram` instead of `hist`. variable names which are exposed to the user should be as clear as possible.

> Username: codejaeger  
> Created_at: 2020-05-31 09:48:11 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r432929196  

Will do.

---

📁 include/boost/gil/histogram.hpp

```diff
 147 |+     for_each_pixel(color_converted_view<pixel_t>(srcview), [&hist](pixel_t const& p) {
 148 |+         ++hist[p];
 149 |+     });
```

> Username: lpranam  
> Created_at: 2020-05-31 08:09:52 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r432921713  

this is code repetition you should consider calling `image_histogram(SrcView const& srcview, Container &hist)`

> Username: codejaeger  
> Created_at: 2020-05-31 09:50:18 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r432929327  

Maybe make a image_histogram_impl(SrcView const& srcview, Container &hist) without the constraints on the template type is what you meant?

> Username: mloskot  
> Created_at: 2020-06-04 17:05:53 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r435413594  

@lpranam   
> I have some objections with the code format(template parameters)  
  
What are the objections exactly?  
  
Generally, as it was agreed quite some time ago, we try to keep the lines up to [100 characters long](https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md#guidelines).  
Second, parameters are often long (e.g. `mp11::mp_size<typename Layout::color_space_t>::value`) and it is often a good idea to keep names of template parameters self-descriptive, avoid cryptic abbreviation.  
  
So, this for example would fit that limit:  
  
```cpp  
template <typename T>  
struct is_indexable<T, decltype((void) ++declval<T>()[0] , 0)> : std::true_type {} ;  
```  
  
but this would not, hence each parameter goes into separate lines and `<` and `>` are formatted similarly to blocks delimited with `{` and `}`   
  
```cpp  
template <typename ChannelValue, typename Layout>  
struct pixel :  
    detail::homogeneous_color_base  
    <  
        ChannelValue,  
        Layout,  
        mp11::mp_size<typename Layout::color_space_t>::value  
    >  
{  
...  
};  
```  
  
That makes the whole guideline very simple, without too many exceptions or sub-rules.  
  
The only other, subjectively, readable formatting of highly templated source code that I know is one used by Boost.Spirit or other libraries by [Joel de Guzman](https://github.com/cycfi/), Michael Caisse et. al.

> Username: mloskot  
> Created_at: 2020-06-19 19:40:03 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r443014478  

@lpranam If you don't have any proposal on better formatting fitting template parameters list in 100 characters long lines, then I'd suggest to mark this conversation as resolved.  
  
Personally, I find reading this much more difficult:  
  
```cpp  
using tp = boost::mp11::mp_list<typename std::is_convertible<  
                    boost::mp11::mp_at<bin_t, boost::mp11::mp_size_t<I>>,  
                    typename std::tuple_element<I, Tuple>::type>::type...>;  
```  
than  
```cpp  
using tp = boost::mp11::mp_list  
<  
    typename std::is_convertible  
    <  
        boost::mp11::mp_at<bin_t, boost::mp11::mp_size_t<I>>,  
        typename std::tuple_element<I, Tuple>::type  
    >::type...  
>;  
```

> Username: codejaeger  
> Created_at: 2020-06-22 08:36:43 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r443401352  

@mloskot Is it necessary to break   
  
`histogram<boost::mp11::mp_at<bin_t, boost::mp11::mp_size_t<Dimensions>>...> sub_histogram() {}`  
  
into   
  
```  
    histogram  
    <     
        boost::mp11::mp_at  
        <  
            bin_t,  
            boost::mp11::mp_size_t<Dimensions>  
        >...  
    > sub_histogram()   
    {}  
```  
  
The former fits within 100 characters.

> Username: mloskot  
> Created_at: 2020-06-22 11:20:38 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r443489249  

@codejaeger No, if it fits in line long for 100 characters, then no need for breaking it.  
In my earlier comments I just used the snippet based on your code as an example.

---

📁 include/boost/gil/histogram.hpp

```diff
 179 |+     
 180 |+     for_each_pixel(color_converted_view<pixel_t>(srcview), [&hist](pixel_t const& p) {
 181 |+         if (p < hist.size()) ++hist[p];
```

> Username: lpranam  
> Created_at: 2020-05-31 08:32:07 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r432923388  

instead shouldn't this create equal size bins and store the data according to the bins!?

> Username: codejaeger  
> Created_at: 2020-05-31 09:52:06 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r432929474  

I was not sure how we could convey that piece of information to the end user i.e. specify what the bin size they should use when accessing the values.

---

📁 include/boost/gil/histogram.hpp

```diff
 143 |+     using pixel_t = pixel<channel_t, gray_layout_t>;
 144 |+ 
 145 |+     Container hist(std::numeric_limits<channel_t>::max() + 1);
```

> Username: lpranam  
> Created_at: 2020-05-31 08:43:33 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r432924270  

I don't think this is a good idea! We also have pixel types like RGB64!

> Username: codejaeger  
> Created_at: 2020-05-31 09:55:56 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r432929780  

Could we put a check on the channel size for specific container types? It wouldn't be clean though.

> Username: mloskot  
> Created_at: 2020-06-04 17:07:32 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r435414512  

Or have our own containers as @lpranam suggested in https://lists.boost.org/boost-gil/2020/06/0437.php


📁 test/core/histogram/CMakeLists.txt

```diff
  25 |+   unset(_target)
  26 |+   unset(_test)
  27 |+ endforeach()
```

> Username: lpranam  
> Created_at: 2020-05-31 08:40:06 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r432923997  

missing endline


📁 test/core/histogram/Jamfile

```diff
   9 |+ import testing ;
  10 |+ 
  11 |+ run histogram.cpp ;
```

> Username: lpranam  
> Created_at: 2020-05-31 08:40:12 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r432924015  

missing endline


📁 example/histogram_impl.cpp

```diff
  96 |+ 
  97 |+ 	return 0;
  98 |+ }
```

> Username: lpranam  
> Created_at: 2020-05-31 08:40:32 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r432924033  

missing endline


---

## Comment 2

> Username: codejaeger  
> Created_at: 2020-06-02 19:02:11 UTC  
> Updated_at: 2020-06-02 20:55:41 UTC  
> Url: https://github.com/boostorg/gil/pull/499#issuecomment-637746390  

I have ideas I would like to share further on the current approach.   
I added a diagram to explain better  
  
![histogram](https://user-images.githubusercontent.com/32168969/83555934-b01c2500-a52c-11ea-8203-942ad4296726.png)  
  
This approach might ease extending the implementation design in the future to support both multi dimensional histograms and external containers.   
In brief, I chose this approach because   
  
- it will simplify the usage for the end user (just deal with the simple function call syntax)  
- handle cases like when a call is made to histogram_2d with a specific container type but the image provided is gray scale so the underlying implementation should call histogram_1d_impl  
- simplify providing extensions of other container types, at most they need to replicate the procedure if not use the already present implementation  
  
So basically the first overloads make sure appropriate type checks are performed on both image and container type. The second overload would choose to call the overload specific to the requirements and the third overload can be easily extended to support higher dimensional containers (of any type).   
I would like to know if this seems right or there will be some unwanted implications of using this approach.

---

## Review 3 [Changes requested]

> Username: mloskot  
> Created_at: 2020-06-19 20:01:48 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/499#pullrequestreview-434305523  

@codejaeger  I have made another round of review and comments, nitpicks, and some more serious stuff. /cc @lpranam   
  
I'm not looking into the design and implementation details.  
  
Please, delete the accidental `test/extension/histogram/a.out` file from this PR.

📁 include/boost/gil/histogram.hpp

```diff
  17 |+ 
  18 |+ #include <type_traits>
  19 |+ #include <unordered_map>
```

> Username: mloskot  
> Created_at: 2020-06-19 19:42:55 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r443015531  

Please, try to stick to these guidelines https://github.com/boostorg/gil/wiki/Include-Directives-Order

> Username: codejaeger  
> Created_at: 2020-06-22 07:41:33 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r443371571  

@mloskot should the **container_hash/extensions.hpp** be shifted? It is actually quite confusing because boost::hash_value is actually found in another place at boost/functional/hash.hpp. I will look into this and report back.

> Username: mloskot  
> Created_at: 2020-06-22 11:26:37 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r443491987  

@codejaeger I meant something like this, which is groups, then folder-based and alphabetical order within groups:  
  
```  
#include <boost/gil/metafunctions.hpp>  
#include <boost/gil/pixel.hpp>  
#include <boost/gil/concepts/concept_check.hpp>  
  
#include <boost/mp11.hpp>  
#include <boost/container_hash/extensions.hpp>  
  
#include <type_traits>  
#include <unordered_map>  
```

---

📁 include/boost/gil/histogram.hpp

```diff
  24 |+ 
  25 |+ template<typename... T>
  26 |+ struct hash_tuple {
```

> Username: mloskot  
> Created_at: 2020-06-19 19:43:14 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r443015661  

Again, `{` in new line please

---

📁 include/boost/gil/histogram.hpp

```diff
  31 |+ };
  32 |+ 
  33 |+ // With C++14 and using auto we don't need the decltype anymore
```

> Username: mloskot  
> Created_at: 2020-06-19 19:45:13 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r443016344  

I suggest to mark such comments about improvements suggested or planned in future with `TODO`, i.e.  
```  
// TODO: With C++14 and using auto we don't need the decltype anymore  
```  
or, if it is about public interface, then the Doxygen way:  
```  
/// \todo With C++14 and using auto we don't need the decltype anymore  
```

---

📁 include/boost/gil/histogram.hpp

```diff
  51 |+ class histogram : public std::unordered_map<std::tuple<T...>, int, detail::hash_tuple<T...> >
  52 |+ {
  53 |+     using parent_t = std::unordered_map<std::tuple<T...>, int, detail::hash_tuple<T...> >;
```

> Username: mloskot  
> Created_at: 2020-06-19 19:46:14 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r443016733  

I think `base_t` is better than `parent_t`. It is canonical that we derive from *base class*.

---

📁 include/boost/gil/histogram.hpp

```diff
  59 |+     histogram() = default;
  60 |+ 
  61 |+     mapped_t& operator()(T... indices) {
```

> Username: mloskot  
> Created_at: 2020-06-19 19:47:29 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r443017143  

Again, `{` in new line.  
  
If it is annoying to keep track of such details, then just use `clang-format` and your code will be consistent at least  
See https://github.com/boostorg/gil/tree/develop/example/clang-format

---

📁 include/boost/gil/histogram.hpp

```diff
  61 |+     mapped_t& operator()(T... indices) {
  62 |+         auto key = std::make_tuple(indices...);
  63 |+         const size_t index_dimension = std::tuple_size<std::tuple<T...>>::value;
```

> Username: mloskot  
> Created_at: 2020-06-19 19:48:42 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r443017549  

Since you've been following the East Const convention, you may do here `size_t const` too.  
  
Second, please always qualify `size_t` as `std::size_t`  (I assume we never do `using namespace std;`)


📁 include/boost/gil/extension/histogram/stl_histogram.hpp

```diff
   8 |+ 
   9 |+ #ifndef BOOST_GIL_EXTENSION_HISTOGRAM_STL_HISTOGRAM_HPP
  10 |+ #define BOOST_GIL_EXTENSION_HISTOGRAM_STL_HISTOGRAM_HPP
```

> Username: mloskot  
> Created_at: 2020-06-19 19:58:20 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r443020730  

I'd suggest to rename `stl_histogram.hpp` to `std.hpp`, because, IMO:  
  
1. It is clearer name: a thing related to the `std::` stuff.  
  
2. Nowadays, 99.99% of us use the C++ Standard Library, not the [C++ Standard Template Library](https://en.wikipedia.org/wiki/Standard_Template_Library) that, *"that influenced many parts of the C++ Standard Library"*. C++ STD != C++ STL  
  
3. Boost convention, de-facto, is `std.hpp`  
  
    ```  
    $ find . -name std.hpp  
    ./assign/include/boost/assign/std.hpp  
    ./hana/include/boost/hana/ext/std.hpp  
    ./proto/include/boost/proto/functional/std.hpp  
    ```


---

## Comment 4

> Username: mloskot  
> Created_at: 2020-06-19 22:47:50 UTC  
> Updated_at: 2020-06-19 22:49:09 UTC  
> Url: https://github.com/boostorg/gil/pull/499#issuecomment-646886811  

@codejaeger Generally, I think the idea of the `histogram` class is a fine one, it looks good. For someone experienced with legacy GIL code, shift from metafunctions to `constexpr` functions and their invocations may be surprising :-)  
  
A few extra high-level suggestions:  
1. Avoid `get_` and `set_` prefix, so `get_dimension()` should read `dimension()`  
2. Prefix all predicates with `is_` consistently, so   
    - `tuple_size_compatible` -> `is_tuple_size_compatible`  
    - `tuple_type_compatible` -> `is_tuple_type_compatible`  
3. The `cast_to_histogram_key` should read `make_histogram_key`  
4. The `test/core/histogram/histogram.cpp` should be split into multiple files, for example  
    - `fill.cpp` with tests for variety of image types  
    - `key.cpp` with tests for `key_from_*`  
    - `access.cpp` with tests basic accessing of histogram entries  
    - `sub_histogram.cpp` with tests specific to sub-s  
    - Some `BOOST_TEST` checks should become `static_assert`, for example  
         ```cpp  
         static_assert(h1.get_dimension() == h2.get_dimension(), ""); // not BOOST_TEST  
         ```  
         That is, there should be `dimension.cpp`, `is_*.cpp` with compile-time only tests (`compile` and `compile-fail` rule in `Jamfile`)  
    - `helpers.cpp` with run-time tests of `is_*` predicates and others  
5. You need to clean up mixing `std::size_t` with `std::ptrdiff_t` with `int`.   
    It is important to remember that in `view(x, y)` the `x` and `y` are `std::ptrdiff_t`. This will also avoid numerous compilation warnings.  
6. I am also not clear about the purpose of the sub-histogram, so I'd like to learn more about the motivation behind it.

---

## Review 5 [Changes requested]

> Username: lpranam  
> Created_at: 2020-06-29 16:59:56 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/499#pullrequestreview-439324304  

📁 test/core/histogram/fill.cpp

```diff
  42 |+ };
  43 |+ 
  44 |+ void fill_gray_image(std::ptrdiff_t width, std::ptrdiff_t height, std::uint8_t* matrix, gil::gray8_view_t const& view)
```

> Username: lpranam  
> Created_at: 2020-06-29 16:45:48 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r447110154  

could use `interleaved_view` maybe?

---

📁 test/core/histogram/fill.cpp

```diff
  53 |+ }
  54 |+ 
  55 |+ void fill_rgb_image(std::ptrdiff_t width, std::ptrdiff_t height, std::uint8_t* matrix, gil::rgb8_view_t const& view)
```

> Username: lpranam  
> Created_at: 2020-06-29 16:46:10 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r447110367  

could use `interleaved_view` maybe?


📁 test/core/histogram/cumulative.cpp

```diff
  73 |+ 
  74 |+     return boost::report_errors();
  75 |+ }
```

> Username: lpranam  
> Created_at: 2020-06-29 16:46:44 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r447110703  

missing end line


📁 test/core/histogram/access.cpp

```diff
  33 |+ 
  34 |+     return boost::report_errors();
  35 |+ }
```

> Username: lpranam  
> Created_at: 2020-06-29 16:47:08 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r447110929  

missing end line


📁 test/core/histogram/is_compatible.cpp

```diff
  70 |+ 
  71 |+     return boost::report_errors();
  72 |+ }
```

> Username: lpranam  
> Created_at: 2020-06-29 16:57:12 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r447117105  

missing end line


📁 test/core/histogram/sub_histogram.cpp

```diff
  56 |+ 
  57 |+     return boost::report_errors();
  58 |+ }
```

> Username: lpranam  
> Created_at: 2020-06-29 16:58:31 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r447118049  

missing end line


📁 test/core/histogram/key.cpp

```diff
  60 |+ 
  61 |+     return boost::report_errors();
  62 |+ }
```

> Username: lpranam  
> Created_at: 2020-06-29 16:58:53 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r447118272  

missing end line


📁 test/core/histogram/helpers.cpp

```diff
  71 |+ 
  72 |+     return boost::report_errors();
  73 |+ }
```

> Username: lpranam  
> Created_at: 2020-06-29 16:59:06 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r447118428  

missing end line


---

## Comment 6

> Username: mloskot  
> Created_at: 2020-07-01 08:29:23 UTC  
> Url: https://github.com/boostorg/gil/pull/499#issuecomment-652274782  

@codejaeger The subject of this PR includes words WIP and Test, does it mean the PR is still a draft?  
  
Although any PR can be considered as WIP or draft until it is merged or rejected, I'd suggest to use tag WIP only to indicate as "Please, do not review it yet, I'm still working on it". Also, the "Test" is unnecessary and confusing.  
  
If it is not a work in progress, I'd suggest to change the subject to "Add histogram class and basic operations" or similar.

---

## Comment 7

> Username: codejaeger  
> Created_at: 2020-07-01 09:07:55 UTC  
> Updated_at: 2020-07-01 09:11:35 UTC  
> Url: https://github.com/boostorg/gil/pull/499#issuecomment-652295185  

@mloskot I had forgotten to remove the [WIP] tag. In the future I will resort to using the draft mode only to convey the state.   
The 'Test' can be removed now since the class idea is finalized now.   
As for the histogram class, one might feel the need to add a certain functionality after appropriate discussions. In that case I guess new PR's are allowed to the existing class.  
For now yes it is finalized.

---

## Comment 8

> Username: codejaeger  
> Created_at: 2020-07-05 06:01:25 UTC  
> Updated_at: 2020-07-06 08:34:52 UTC  
> Url: https://github.com/boostorg/gil/pull/499#issuecomment-653846603  

@mloskot @lpranam Currently the histogram only supports integral bin types. I started with this for simplicity, but now I think floating point values are also needed for the algorithms. So, should we provide a 'histogramf' wrapper that gives a floating point version of the histogram, so that the calling interface remains the same?  
And of course rename the histogram class to something else?  
I feel there would be better ways, hence the question.  
Update : I am changing the default bin storage type to double, to keep it simple for now.

---

## Comment 9

> Username: codejaeger  
> Created_at: 2020-07-06 21:06:43 UTC  
> Url: https://github.com/boostorg/gil/pull/499#issuecomment-654465697  

@mloskot @lpranam I need to make some additions to the class which is needed for the histogram equalization algorithm. Should I add that to this PR or wait to add it with the algorithm?

---

## Review 10 [Changes requested]

> Username: lpranam  
> Created_at: 2020-07-11 15:17:46 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/499#pullrequestreview-446799376  

Looks good to me let's get this in after a couple of nitpicking. @mloskot can you have a look before we merge?

📁 example/histogram.cpp

```diff
   1 | //
   2 |- // Copyright 2005-2007 Adobe Systems Incorporated
   2 |+ // Copyright 2020 Debabrata Mandal <mandaldebabrata123@gmail.com>
```

> Username: lpranam  
> Created_at: 2020-07-11 12:24:26 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r453189945  

though you have changed the entire file, but it still the same file you can not remove the copyright.

> Username: codejaeger  
> Created_at: 2020-07-11 16:06:16 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r453209088  

The previous histogram.cpp got renamed to tutorial_histogram.cpp. I will add the copyright otherwise if needed.

> Username: mloskot  
> Created_at: 2020-07-26 18:24:45 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r460558206  

Yes, I think I was suggesting that renaming and to sum it up  
- `histogram.cpp` is @codejaeger 's new example  
- `tutorial_histogram.cpp` is the old one


📁 include/boost/gil/histogram.hpp

```diff
 442 |     using histogram_t = histogram<T...>;
 431 |-     using pair_t = std::pair<typename histogram_t::key_type, typename histogram_t::mapped_type>;
 443 |+     using pair_t  = std::pair<typename histogram_t::key_type, typename histogram_t::mapped_type>;
```

> Username: lpranam  
> Created_at: 2020-07-11 15:08:47 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r453203793  

nitpicking: remove the additional space before `=`

> Username: codejaeger  
> Created_at: 2020-07-11 16:12:18 UTC  
> Updated_at: 2020-08-24 19:34:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r453209602  

I think it got added when I used clang-format, maybe to keep the equal signs in line 473 and 474 at the same position. This has been done in many other places also I think.

> Username: mloskot  
> Created_at: 2021-01-22 22:36:55 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r562953256  

@codejaeger Which clang-format configuration file did you use?

> Username: codejaeger  
> Created_at: 2021-01-23 20:40:24 UTC  
> Updated_at: 2021-01-23 20:40:25 UTC  
> Url: https://github.com/boostorg/gil/pull/499#discussion_r563196909  

@mloskot I believe I had used the one linked below:  
[https://github.com/boostorg/gil/blob/develop/example/clang-format/.clang-format](https://github.com/boostorg/gil/blob/develop/example/clang-format/.clang-format)


---

## Comment 11

> Username: mloskot  
> Created_at: 2020-07-11 17:41:45 UTC  
> Url: https://github.com/boostorg/gil/pull/499#issuecomment-657101571  

@codejaeger @lpranam First, I'm terribly sorry, for the long silence again.  
I am catching up with the reviews now, but first I need to work out the merge of develop to master for Boost 1.74 release before the master closes today.

---

## Comment 12

> Username: codejaeger  
> Created_at: 2020-07-25 20:31:32 UTC  
> Url: https://github.com/boostorg/gil/pull/499#issuecomment-663903121  

@mloskot @lpranam I should make some patches to this PR, I can continue in this by marking it as draft. Is that fine?

---

## Comment 13

> Username: mloskot  
> Created_at: 2020-07-26 20:10:55 UTC  
> Updated_at: 2020-07-26 20:19:46 UTC  
> Url: https://github.com/boostorg/gil/pull/499#issuecomment-664034427  

@codejaeger I'm reviewing this PR, but please feel free to apply any changes you need.  
  
----  
  
@codejaeger  A bit of update to my comment:  
  
The `example/histogram.cpp` is a bit too hard to follow. An example is supposed to be a minimum sample of code that presents how to use a feature.  
I'd suggest the following approach:  
  
1. You've implemented histogram class and computation of histogram, then add sample of code how to define, construct and compute histogram.  
2. You've added histogram equalization, then, in that `example/histogram.cpp`, show how to use it.  
3. ...  
  
Finally, for the example, I'd suggest to stick to the 1D histogram and grayscale image.  
You can always add shadow file `example/histogram_color.cpp` with equivalent for colour (e.g. RGB) image.  
  
----  
  
@lpranam What's your take on this PR, I recall you had objections to some of the design choices. Please, forgive me that I had not reacted to that discussion in timely manner and that I can't remember exactly what was that about.

---

## Comment 14

> Username: codejaeger  
> Created_at: 2020-07-28 17:42:00 UTC  
> Url: https://github.com/boostorg/gil/pull/499#issuecomment-665179487  

> @codejaeger I'm reviewing this PR, but please feel free to apply any changes you need.  
>   
> @codejaeger A bit of update to my comment:  
>   
> The `example/histogram.cpp` is a bit too hard to follow. An example is supposed to be a minimum sample of code that presents how to use a feature.  
  
@mloskot I agree it is difficult enough to read for anyone. The file started as an example and became more of a test in the later revisions. I will update it at once. I could also add block comments to separate out different sections of implementations (like create a histogram, fill it etc.).   
  
Although I don't expect anyone to come to this file for reference since I aim to make the docs self sufficient, I will make the changes required. Thanks for the suggestion.

---

## Comment 15

> Username: mloskot  
> Created_at: 2020-07-28 19:20:53 UTC  
> Url: https://github.com/boostorg/gil/pull/499#issuecomment-665229112  

@codejaeger   
> The file started as an example and became more of a test in the later revisions.   
  
Yes, that is quite common approach.  
  
> Although I don't expect anyone to come to this file for reference since I aim to make the docs self sufficient, I will make the changes required.   
  
What you can do is to use the file to combine the snippets from the docs, so you can be sure they compile.  
  
Ideally, if the examples in the docs are self-contained and are compile-tested when the docs are built, but I don't know how to achieve it in Sphinx. That is one of reasons why we may consider switch to AsciiDoc as some point in future

---

## Comment 16

> Username: codejaeger  
> Created_at: 2020-07-31 09:28:05 UTC  
> Url: https://github.com/boostorg/gil/pull/499#issuecomment-667028514  

@mloskot , @lpranam  I have   
1. updated the example file for histogram  
2. reduced the warnings from the tests and example  
3. Added Doxygen comments for the new changes  
Unless the CI checks fail it is ready for review now.

---

## Comment 17

> Username: mloskot  
> Created_at: 2020-08-07 08:30:50 UTC  
> Url: https://github.com/boostorg/gil/pull/499#issuecomment-670399953  

@codejaeger Thanks for the update.  
  
Something is wrong with the histogram example, see https://ci.appveyor.com/project/stefanseefeld/gil/builds/34407783/job/fw3u0ba7yno9rdui  
  
The last build job of AppVeyor is CMake-based which builds examples too.

---

## Comment 18

> Username: codejaeger  
> Created_at: 2020-08-08 05:45:18 UTC  
> Url: https://github.com/boostorg/gil/pull/499#issuecomment-670828377  

Thanks @mloskot for pointing out the bug. I hope it gets fixed with the last commit.

---

## Review 19 [Approved]

> Username: mloskot  
> Created_at: 2021-01-22 22:46:34 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/499#pullrequestreview-574677417  

I don't have any major issues to report about this PR, so I'm happy to approve it.  
The only 'unresolved' comments are about the `x<space><space>=<space>y` formatting due to use of the `clang-format` and pointed out by @lpranam which I'd consider not a stopper issue that can be addressed after the merge along with other updates or improvements that are applicable post-merge.  
  
@codejaeger Once again, thanks for your contributions

---
