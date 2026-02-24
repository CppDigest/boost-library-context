# #596 Introduce official clang-format [Open]

> Username: lpranam  
> Created at: 2021-04-11 16:21:54 UTC  
> Updated at: 2021-08-20 09:57:45 UTC  
> Url: https://github.com/boostorg/gil/pull/596  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
provides .clang-format with the latest(clang-format 12) configuration  
  
### References  
  
- Closes: #518  
- https://github.com/boostorg/gil/pull/87  
- [example/clang-format](https://github.com/boostorg/gil/tree/23702f9879e81bb3b08971eb153b199ea56bb0af/example/clang-format)  
  
### Tasklist  
  
- [ ] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: lpranam  
> Created_at: 2021-04-11 16:25:54 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-817333852  

It is manually configured according to what seemed more readable to me.  
  
not so useful options or non cpp options are left as default by llvm style

---

## Review 2 [Changes requested]

> Username: mloskot  
> Created_at: 2021-04-12 16:46:56 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/596#pullrequestreview-633759213  

First, I'm happy to see this initiative moving forward.  
  
How this configuration relates to the initial one and example linked from here https://github.com/boostorg/gil/issues/518#issuecomment-681851554 ? Is this significantly different?   
I'd like to see some reference to those in this PR.

---

## Comment 3

> Username: lpranam  
> Created_at: 2021-04-12 18:31:57 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-818034085  

the example provided in #518 is default LLVM while in this PR it follows our existing example at most of the places and I used all the options so that formatting can be fine-tuned and better. and also some twiques to make this more suitable for us i.e: `IncludeCategories`

---

## Comment 4

> Username: mloskot  
> Created_at: 2021-04-12 19:25:10 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-818072814  

Right, but the #87 one offers customised  configuration. I was mostly interested to know how different is this one from the #87 I have not had a chance to try it out myself. I may have time on Wednesday.

---

## Comment 5

> Username: lpranam  
> Created_at: 2021-04-12 22:08:12 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-818274628  

I have not tested this against #87. but I have tested this against our example file(i don't know if they are same), only difference in there(from the few files I tested)   
  
1. if parameters do not fit one parameter each line  
2. align the assignment operators  
  
My main test files were kernel.hpp and convolve.hpp

---

## Comment 6

> Username: mloskot  
> Created_at: 2021-04-19 18:18:45 UTC  
> Updated_at: 2021-04-19 18:30:15 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-822675996  

Do you know if there is any way to tell `clang-format` to respect custom macro like `BOOST_FORCEINLINE` as `inline`?  
  
Currently, this:  
  
```cpp  
BOOST_FORCEINLINE  
int foo() { return 1 + 2; }  
  
inline  
int bar() { return 1 + 2; }  
```  
  
becomes this  
  
```cpp  
BOOST_FORCEINLINE int  
    foo()  
{  
    return 1 + 2;  
}  
  
inline int bar()  
{  
    return 1 + 2;  
}  
```  
  
------  
  
UPDATE: I can not find any option for `inline`. [It looks like it's not possible, neither for `static`](https://stackoverflow.com/questions/62601655/break-after-inline-keyword-in-clang-format).  
Then, I think, it is even more important to refactor into trailing return as I explained in https://github.com/boostorg/gil/pull/596#issuecomment-822681523

---

## Comment 7

> Username: mloskot  
> Created_at: 2021-04-19 18:20:37 UTC  
> Updated_at: 2021-04-19 18:21:44 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-822677161  

Do you know if there is any way in clang-format 12 to handle the trailing return better way?  
  
Here is one bit from `algorithm.hpp` which I manually formatted long time ago  
  
```cpp  
template<typename T, typename CS>  
auto copy(  
    boost::gil::pixel<T, CS>* first,  
    boost::gil::pixel<T, CS>* last,  
    boost::gil::pixel<T, CS>* dst)  
    ->  boost::gil::pixel<T, CS>*  
{  
    auto p = std::copy((unsigned char*)first, (unsigned char*)last, (unsigned char*)dst);  
    return reinterpret_cast<boost::gil::pixel<T, CS>*>(p);  
}  
```  
  
and `clang-format` formats it this way:  
  
```cpp  
template <typename T, typename CS>  
auto copy(  
    boost::gil::pixel<T, CS>* first,  
    boost::gil::pixel<T, CS>* last,  
    boost::gil::pixel<T, CS>* dst) -> boost::gil::pixel<T, CS>*  
{  
    auto p = std::copy((unsigned char*)first, (unsigned char*)last, (unsigned char*)dst);  
    return reinterpret_cast<boost::gil::pixel<T, CS>*>(p);  
}  
```  
  
I like the former. Or, we can just live with the latter :-)

---

## Comment 8

> Username: mloskot  
> Created_at: 2021-04-19 18:27:36 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-822681523  

I think this would also be an opportunity to refactor functions into trailing return type.  
  
For example, this input  
  
```cpp  
template <typename IC>  
inline typename type_from_x_iterator<planar_pixel_iterator<IC,cmyk_t> >::view_t  
planar_cmyk_view(std::size_t width, std::size_t height, IC c, IC m, IC y, IC k, std::ptrdiff_t rowsize_in_bytes)  
{  
    using view_t = typename type_from_x_iterator<planar_pixel_iterator<IC,cmyk_t> >::view_t;  
    return view_t(width, height, typename view_t::locator(planar_pixel_iterator<IC,cmyk_t>(c,m,y,k), rowsize_in_bytes));  
}  
```  
  
is reformatted as  
  
```cpp  
template <typename IC>  
inline typename type_from_x_iterator<planar_pixel_iterator<IC, cmyk_t>>::view_t planar_cmyk_view(  
    std::size_t width,  
    std::size_t height,  
    IC c,  
    IC m,  
    IC y,  
    IC k,  
    std::ptrdiff_t rowsize_in_bytes)  
{  
    using view_t = typename type_from_x_iterator<planar_pixel_iterator<IC, cmyk_t>>::view_t;  
    return view_t(  
        width,  
        height,  
        typename view_t::locator(planar_pixel_iterator<IC, cmyk_t>(c, m, y, k), rowsize_in_bytes));  
}  
```  
  
but if the is changed to trailing return type, then the reformatting output is nicer, I think:  
  
```cpp  
template <typename IC>  
inline auto planar_cmyk_view(  
    std::size_t width,  
    std::size_t height,  
    IC c,  
    IC m,  
    IC y,  
    IC k,  
    std::ptrdiff_t rowsize_in_bytes) ->  
    typename type_from_x_iterator<planar_pixel_iterator<IC, cmyk_t>>::view_t  
{  
    using view_t = typename type_from_x_iterator<planar_pixel_iterator<IC, cmyk_t>>::view_t;  
    return view_t(  
        width,  
        height,  
        typename view_t::locator(planar_pixel_iterator<IC, cmyk_t>(c, m, y, k), rowsize_in_bytes));  
}  
```

---

## Review 9 [Approved]

> Username: mloskot  
> Created_at: 2021-04-25 17:54:41 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/596#pullrequestreview-644165346  

I'm happy to go for this - it is good enough and better is evil of good, especially that we have not been able to work out any better for quite some time due to clang-format limitations :-)  
  
We still need to consider how to approach the "big reformat" - see the MongoDB story I linked in https://github.com/boostorg/gil/issues/518#issuecomment-688721865

---

## Comment 10

> Username: sdebionne  
> Created_at: 2021-04-30 10:49:57 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-830011517  

It looks like `clang-tools` 12 is required? With 11.1, I get:  
```  
Invalid argument  
YAML:6:25: error: invalid boolean  
AlignConsecutiveMacros: AcrossEmptyLinesAndComments  
                        ^~~~~~~~~~~~~~~~~~~~~~~~~~~  
```

---

## Comment 11

> Username: lpranam  
> Created_at: 2021-04-30 13:48:42 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-830108098  

@sdebionne yes clang 12 is required.

---

## Comment 12

> Username: mloskot  
> Created_at: 2021-05-02 16:52:39 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-830838091  

I understand clang 12 is not yet widely available via popular distro packages, but I think we should start using clang-format from the latest release, and keep ourselves updated as soon as new version is released.

---

## Comment 13

> Username: mloskot  
> Created_at: 2021-05-06 18:20:27 UTC  
> Updated_at: 2021-05-06 18:25:33 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-833753768  

I have another example and question:  
  
I run the `clang-format` against header from the PR https://github.com/boostorg/gil/pull/604 and wonder if this alignment of `=` assignments is intentional?  
  
```cpp  
     for (std::ptrdiff_t i = 0; i < 16; i++)  
     {  
-        pointers[i] = src_loc.cache_location(points[i][0], points[i][1]);  
+        pointers[i]        = src_loc.cache_location(points[i][0], points[i][1]);  
         intensity_array[i] = src_loc[pointers[i]];  
     }  
```  
  
I'm not convinced as it may artificially lengthen lines and cause unnecessary line wrapping.  
What you think @lpranam @sdebionne ?  
  
-----  
  
Here is similar situation where all the `score > fast_...` lines got aligned to the right  
  
```cpp  
if (!nonmax || score > fast_score_matrix(j - 1, i)[0] &&  
                    score > fast_score_matrix(j + 1, i)[0] &&  
                    score > fast_score_matrix(j - 1, i - 1)[0] &&  
                    score > fast_score_matrix(j, i - 1)[0] &&  
                    score > fast_score_matrix(j + 1, i - 1)[0] &&  
                    score > fast_score_matrix(j - 1, i + 1)[0] &&  
                    score > fast_score_matrix(j, i + 1)[0] &&  
                    score > fast_score_matrix(j + 1, i + 1)[0])  
{  
    keypoints.push_back(u);  
    scores.push_back(score);  
}  
```  
  
I, personally, do not like it as the big blank space to the left is just wasteful - I don't you know but I read from left to right :-)

---

## Review 14 [Commented]

> Username: lpranam  
> Created_at: 2021-05-06 18:48:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/596#pullrequestreview-653787149  

📁 .clang-format

```diff
   5 |+ AlignAfterOpenBracket: AlwaysBreak
   6 |+ AlignConsecutiveMacros: AcrossEmptyLinesAndComments
   7 |+ AlignConsecutiveAssignments: AcrossEmptyLinesAndComments
```

> Username: lpranam  
> Created_at: 2021-05-06 18:48:32 UTC  
> Updated_at: 2021-05-06 18:48:33 UTC  
> Url: https://github.com/boostorg/gil/pull/596#discussion_r627682537  

yes it was intentional, but we can change I don't mind anything in this case

> Username: sdebionne  
> Created_at: 2021-05-06 18:55:25 UTC  
> Updated_at: 2021-05-06 18:55:26 UTC  
> Url: https://github.com/boostorg/gil/pull/596#discussion_r627687067  

Not big fan of the alignment of `=` assignments neither. As of the alignment to the right, I have never seen anything like that before. I am all for a consistent formatting, really don't care too much about the rules as long as they are not too artificial,  not too far from what a human developer (think lazy) would write :grin:

> Username: mloskot  
> Created_at: 2021-05-06 19:27:59 UTC  
> Updated_at: 2021-05-06 19:28:00 UTC  
> Url: https://github.com/boostorg/gil/pull/596#discussion_r627707213  

Good. I don't think I'd ever hit the TAB so many times myself :-)  
  
So, I suggest to avoid those alignments.


---

## Comment 15

> Username: mloskot  
> Created_at: 2021-05-06 23:23:55 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-833941725  

Replying to my own https://github.com/boostorg/gil/pull/596#issuecomment-822677161  
> Do you know if there is any way in clang-format 12 to handle the trailing return better way?  
  
There is a way!  https://stackoverflow.com/a/60471935/151641 which, I think, we should consider for complex prototypes. For example, **without the hack** :  
  
```cpp  
template <typename String, typename FormatTag, typename ConversionPolicy>  
inline auto make_reader(  
    String const& file_name,  
    FormatTag const&,  
    ConversionPolicy const& cc,  
    typename std::enable_if<  
        mp11::mp_and<detail::is_supported_path_spec<String>, is_format_tag<FormatTag>>::value>::  
        type* = nullptr) -> typename get_reader<String, FormatTag, ConversionPolicy>::type  
{  
    return make_reader(file_name, image_read_settings<FormatTag>(), cc);  
}  
```  
  
and with the hack is better, I think  
  
```cpp  
template <typename String, typename FormatTag, typename ConversionPolicy>  
inline auto make_reader(  
    String const& file_name,  
    FormatTag const&,  
    ConversionPolicy const& cc,  
    typename std::enable_if<  
        mp11::mp_and<detail::is_supported_path_spec<String>, is_format_tag<FormatTag>>::value>::  
        type* = nullptr)  //  
    -> typename get_reader<String, FormatTag, ConversionPolicy>::type  
{  
    return make_reader(file_name, image_read_settings<FormatTag>(), cc);  
}  
```

---

## Comment 16

> Username: mloskot  
> Created_at: 2021-05-06 23:39:57 UTC  
> Updated_at: 2021-05-06 23:41:07 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-833948328  

I updated this PR with commit https://github.com/boostorg/gil/pull/596/commits/a3af6221ce0ea2748bb52f50af18b7b58a87f479 with proposal of tweaks, please have a look  
  
<details>  
<summary>Here is `test.hpp` sample with output of my tweaks</summary>  
<p>  
  
```cpp  
//  
// Copyright 2007-2012 Christian Henning, Andreas Pokorny, Lubomir Bourdev  
//  
// Distributed under the Boost Software License, Version 1.0  
// See accompanying file LICENSE_1_0.txt or copy at  
// http://www.boost.org/LICENSE_1_0.txt  
//  
#ifndef BOOST_GIL_IO_READ_VIEW_HPP  
#define BOOST_GIL_IO_READ_VIEW_HPP  
  
#include <boost/gil/detail/mp11.hpp>  
#include <boost/gil/io/base.hpp>  
#include <boost/gil/io/conversion_policies.hpp>  
#include <boost/gil/io/device.hpp>  
#include <boost/gil/io/get_reader.hpp>  
#include <boost/gil/io/path_spec.hpp>  
  
#include <boost/mp11.hpp>  
  
#include <type_traits>  
  
namespace boost { namespace gil {  
  
/// \ingroup IO  
  
/// \brief Reads an image view without conversion. No memory is allocated.  
/// \param reader An image reader.  
/// \param view The image view in which the data is read into.  
/// \param settings  Specifies read settings depending on the image format.  
/// \throw std::ios_base::failure  
template <typename Reader, typename View>  
inline void read_view(  
    Reader reader,  
    View const& view,  
    typename std::enable_if<mp11::mp_and<  
        detail::is_reader<Reader>,  
        typename is_format_tag<typename Reader::format_tag_t>::type,  
        typename is_read_supported<  
            typename get_pixel_type<View>::type,  
            typename Reader::format_tag_t>::type>::value>::type* = nullptr)  
{  
    reader.check_image_size(view.dimensions());  
    reader.init_view(view, reader._settings);  
    reader.apply(view);  
}  
  
template <typename String, typename FormatTag, typename ConversionPolicy>  
inline auto make_reader(  
    String const& file_name,  
    FormatTag const&,  
    ConversionPolicy const& cc,  
    typename std::enable_if<  
        mp11::mp_and<detail::is_supported_path_spec<String>, is_format_tag<FormatTag>>::value>::  
        type* = nullptr)  //  
    -> typename get_reader<String, FormatTag, ConversionPolicy>::type  
{  
    return make_reader(file_name, image_read_settings<FormatTag>(), cc);  
}  
  
template <typename FormatTag, typename ConversionPolicy>  
inline auto make_reader(  
    const std::wstring& file_name,  
    const FormatTag&,  
    const ConversionPolicy& cc)  //  
    -> typename get_reader<std::wstring, FormatTag, ConversionPolicy>::type  
{  
    return make_reader(file_name, image_read_settings<FormatTag>(), cc);  
}  
  
void foo()  
{  
    for (std::ptrdiff_t i = 0; i < 16; i++)  
    {  
        pointers[i] = src_loc.cache_location(points[i][0], points[i][1]);  
        pointers[i] = src_loc.cache_location(points[i][0], points[i][1]) + src_loc[pointers[i]]  
                    - src_loc[pointers[i]];  
        intensity_array[i] = src_loc[pointers[i]];  
    }  
  
    for (std::ptrdiff_t i = 0; i < 16; i++)  
    {  
        if (!nonmax  
            || (score > fast_score_matrix(j - 1, i)[0] && score > fast_score_matrix(j + 1, i)[0]  
                && score > fast_score_matrix(j - 1, i - 1)[0]  
                && score > fast_score_matrix(j, i - 1)[0]  
                && score > fast_score_matrix(j + 1, i - 1)[0]  
                && score > fast_score_matrix(j - 1, i + 1)[0]  
                && score > fast_score_matrix(j, i + 1)[0]  
                && score > fast_score_matrix(j + 1, i + 1)[0]))  
        {  
            keypoints.push_back(u);  
            scores.push_back(score);  
        }  
    }  
  
    if (!nonmax  
        || (score > fast_score_matrix(j - 1, i)[0] && score > fast_score_matrix(j + 1, i)[0]  
            && score > fast_score_matrix(j - 1, i - 1)[0] && score > fast_score_matrix(j, i - 1)[0]  
            && score > fast_score_matrix(j + 1, i - 1)[0]  
            && score > fast_score_matrix(j - 1, i + 1)[0] && score > fast_score_matrix(j, i + 1)[0]  
            && score > fast_score_matrix(j + 1, i + 1)[0]))  
    {  
        keypoints.push_back(u);  
        scores.push_back(score);  
    }  
}  
  
struct tencil_5points  
{  
    double delta_t = 0.25;  
  
    template <typename SubImageView>  
    auto compute_laplace(SubImageView view, point_t origin)  
        -> stencil_type<typename SubImageView::value_type>  
    {  
        auto current = view(origin);  
        stencil_type<typename SubImageView::value_type> stencil;  
        using channel_type = typename channel_type<typename SubImageView::value_type>::type;  
        std::array<gil::point_t, 8> offsets(get_directed_offsets());  
        typename SubImageView::value_type zero_pixel;  
        static_fill(zero_pixel, 0);  
        for (std::size_t index = 0; index < offsets.size(); ++index)  
        {  
            if (index % 2 != 0)  
            {  
                static_transform(  
                    view(origin.x + offsets[index].x, origin.y + offsets[index].y),  
                    current,  
                    stencil[index],  
                    std::minus<channel_type>{});  
            }  
            else  
            {  
                stencil[index] = zero_pixel;  
            }  
        }  
        return stencil;  
    }  
  
    template <typename Pixel>  
    Pixel reduce(const stencil_type<Pixel>& stencil)  
    {  
        auto first = stencil.begin();  
        auto last = stencil.end();  
        using channel_type = typename channel_type<Pixel>::type;  
        auto result = []() {  
            Pixel zero_pixel;  
            static_fill(zero_pixel, channel_type(0));  
            return zero_pixel;  
        }();  
  
        for (std::size_t index : {1u, 3u, 5u, 7u})  
        {  
            static_transform(result, stencil[index], result, std::plus<channel_type>{});  
        }  
        Pixel delta_t_pixel;  
        static_fill(delta_t_pixel, delta_t);  
        static_transform(result, delta_t_pixel, result, std::multiplies<channel_type>{});  
  
        return result;  
    }  
};  
  
void foo()  
{  
}  
  
}}  // namespace boost::gil  
  
#endif  
```  
  
</p>  
</details>  
  
I think we are close to merge this, once for all and for good :)

---

## Comment 17

> Username: sdebionne  
> Created_at: 2021-05-18 16:40:18 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-843338924  

In case anyone is interested, I have just pushed a re-formated `develop` [here](sdebionne/gil/tree/apply-clang-format). So far I am happy with the new formatting.  
  
I noticed the io extension uses:   
  
```diff  
-    template< typename View_Dst  
-            , typename View_Src  
-            >  
-    void copy_data( const View_Dst&              dst  
-                  , const View_Src&              src  
-                  , typename View_Dst::y_coord_t y  
-                  , std::true_type // is gray1_view  
-                  )  
+    template <typename View_Dst, typename View_Src>  
+    void copy_data(  
+        const View_Dst& dst,  
+        const View_Src& src,  
+        typename View_Dst::y_coord_t y,  
+        std::true_type  // is gray1_view  
```  
I kinda like the comma in front and the closing `>`  and `)` on a new line. What do you think?  
  
I'll try to rewrite the complete history and push it in a new fork.

---

## Comment 18

> Username: sdebionne  
> Created_at: 2021-05-18 16:48:33 UTC  
> Updated_at: 2021-05-18 16:48:51 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-843351291  

One more comment: one-liner member functions are now expended:  
  
```diff  
-    static void apply(V const &, Value const &) { throw std::bad_cast();}  
+    static void apply(V const&, Value const&)  
+    {  
+        throw std::bad_cast();  
+    }  
```  
  
Is that configurable?

---

## Comment 19

> Username: mloskot  
> Created_at: 2021-05-18 16:52:38 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-843356530  

@sdebionne Awesome! I'll check it in details later tonight.  
  
> I kinda like the comma in front and the closing > and ) on a new line. What do you think?  
  
It's been my long time preference, especially for members initialisation [in constructors e.g. in Boost.Geometry](https://github.com/boostorg/geometry/blob/7cbae4d68d10d0b4d4a57fa60cf87a788b3fe8f3/include/boost/geometry/algorithms/difference.hpp#L377-L379)  
  
> One more comment: one-liner member functions are now expended:  
> Is that configurable?  
  
Yes, `AllowShortFunctionsOnASingleLine` should do it.

---

## Comment 20

> Username: lpranam  
> Created_at: 2021-05-19 07:24:49 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-843821870  

@sdebionne why try to rewrite the history? boost does not allow force push so ultimately things become not usable in this case? or am I misinterpreting something?

---

## Comment 21

> Username: mloskot  
> Created_at: 2021-05-19 07:44:29 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-843837251  

The branch protection that forbids forced-push is [ours in boostorg/gil settings](https://lists.boost.org/boost-gil//2018/06/0044.php).  
  
I recall [I was warning you](https://cpplang.slack.com/archives/CSVT0STV2/p1599329490002100) @lpranam about not doing the force-push though :-)  
  
However, we, @stefanseefeld and myself, recreated `develop` branch from `master` branch and we had to 1) disable the branch protection 2) force-push 3) re-enable the branch protection as it was effectively rewrite of history of `develop`.  
It was discussed in our Gitter, but it takes some serching - I wish it was discussed on boost-gil ML :-)  
For example, [here is a trace of the discussion](https://gitter.im/boostorg/gil?at=5b33a19e479ca2668984433a).  
  
The superproject at `boostorg/boost` automagically updates the references every half an hour or so, AFAIR.

---

## Comment 22

> Username: lpranam  
> Created_at: 2021-05-19 07:56:18 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-843845794  

in that case, I have done such thing in past on one of my project let me try to find if I can get my hands on the command/script

---

## Comment 23

> Username: sdebionne  
> Created_at: 2021-05-26 10:48:14 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-848667842  

Here is [a gil-reformatted repo](https://github.com/sdebionne/gil-reformated). I used [lint_history](https://github.com/newren/git-filter-repo/blob/b1606ba8ac8393d704ba41319c0bba3e334f3341/contrib/filter-repo-demos/lint-history):  
  
```  
lint-history --relevant 'return filename.endswith(b".cpp") or filename.endswith(b".hpp")' clang-format -style=file -i  
```  
  
Pretty fast, so not a pb if it needs to be run again with the modifications discussed above.  
  
At some points we will need to unprotect the protected branches, force push the new history, protect again. It's best to do that when we have the least WIP PRs, since every contributors will need to rebase on the new history. A bit of git gymnastic...

---

## Comment 24

> Username: sdebionne  
> Created_at: 2021-05-31 07:37:38 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-851273219  

AppVeyor fails with `fatal: unable to access 'https://github.com/boostorg/boost.git/': Could not resolve host: github.com`.  Do we still need this service, or is it fully covered by Azure / GA?

---

## Comment 25

> Username: lpranam  
> Created_at: 2021-05-31 10:21:26 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-851389158  

GA is not covering stdcxx = 11 on msvc but definitely we can do something about it

---

## Comment 26

> Username: mloskot  
> Created_at: 2021-06-17 23:10:45 UTC  
> Updated_at: 2021-06-17 23:11:03 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-863618247  

@lpranam   
> GA is not covering stdcxx = 11 on msvc but definitely we can do something about it  
  
There is no such thing as plain C++11 for MSVC, only C11. [VS 2015, 2017, 2019 default to C++14](https://docs.microsoft.com/en-us/cpp/build/reference/std-specify-language-standard-version).  
  
Following @sdebionne suggestion, I'm going to get rid of AppVeyor - less is more :-) - https://github.com/boostorg/gil/issues/618

---

## Comment 27

> Username: sdebionne  
> Created_at: 2021-06-18 07:25:43 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-863821835  

Do we need to pin the version of `clang-format` or is it backward compatible? e.g. given a `.clang-format` config will I get the same formatting with `clang-format` 10 and `clang-format` 13?

---

## Comment 28

> Username: sdebionne  
> Created_at: 2021-06-18 07:31:53 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-863825138  

Bummer, I have just checked with my current project, switching from v10 to v12: formatting is a little different, some whitespaces added in a few files (3 out of hundreds)...

---

## Comment 29

> Username: mloskot  
> Created_at: 2021-06-18 10:10:23 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-863925369  

> switching from v10 to v12: formatting is a little different,  
  
Yes, that is known PITA of clang-format, [from MongoDB experience](https://engineering.mongodb.com/post/succeeding-with-clangformat-part-3-persisting-the-change):  
  
> You have to ensure that everyone uses the same version of ClangFormat, or eventually you will run into config incompatibilities, and maybe even silent formatting discrepancies.  
  
I don't mind starting with the current latest. Hopefully, future releases of clang-format are going to receive less and less incompatibilities.

---

## Comment 30

> Username: mloskot  
> Created_at: 2021-06-18 10:14:45 UTC  
> Updated_at: 2021-06-18 10:15:57 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-863928289  

@sdebionne   
> At some points we will need to unprotect the protected branches, force push the new history, protect again. It's best to do that when we have the least WIP PRs, since every contributors will need to rebase on the new history.  
  
I think, the only still open PRs that are going to be merged are the GSoC ones and some several of yours and @lpranam 's  
Most of others are less important examples, GSoC tests, or work that needs some thorough refactoring (e.g. merge multiple PRs addressing the same algorithm).  
  
We may want to take the Boost release calendar into account: [Summer release on the second Wednesday of August](https://github.com/boostorg/wiki/wiki/Releases%3A-Schedule#release-dates). IOW, either we do it soon or after the next release.

---

## Comment 31

> Username: mloskot  
> Created_at: 2021-06-18 23:13:57 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-864314210  

@sdebionne I'm looking at the `develop` branch in the https://github.com/sdebionne/gil-reformated and comparing the formatting with the current one to see the overall formatting effect.  
  
Did you use @lpranam 's `.clang-format` from this PR?  
  
https://github.com/sdebionne/gil-reformated/blob/d85dff7b77f8eacd8713a4692ffeeb43c89b3009/include/boost/gil/algorithm.hpp#L33-L37 displays the unbroken templates:  
  
```cpp  
template <typename ChannelPtr, typename ColorSpace>  
struct planar_pixel_iterator;  
template <typename Iterator> class memory_based_step_iterator;  
template <typename StepIterator> class memory_based_2d_locator;  
```  
  
while @lpranam 's configuration contains   
https://github.com/boostorg/gil/blob/a3af6221ce0ea2748bb52f50af18b7b58a87f479/.clang-format#L26

---

## Comment 32

> Username: sdebionne  
> Created_at: 2021-06-21 07:43:41 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-864806244  

> Did you use @lpranam 's .clang-format from this PR?  
  
This was my intention, let me check...

---

## Comment 33

> Username: sdebionne  
> Created_at: 2021-06-23 07:15:28 UTC  
> Updated_at: 2021-06-23 07:15:41 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-866592330  

Sorry for the delay, I accidentally overwrote my `.clang-format`, so was not able to tell if it was actually the result of `git checkout lpranam:format -- .clang-format` or if it's an issue with `clang-format` that needs to be run multiple time to get the correct result.  
  
So I have reran the all thing from scratch, using the latest `.clang-format`, and I get the same result than the one published on my gil-reformat fork. Rerunning `lint-history`, I get the correct `AlwaysBreakTemplateDeclarations = Yes` formatting. Conclusion:  `lint-history` needs to be run multiple time until `clang-format` converges, which is similar to what we observed with our project.

---

## Comment 34

> Username: mloskot  
> Created_at: 2021-06-23 16:28:22 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-866986017  

@sdebionne Thanks for dobule checking, it makes sense to me.  
  
Folks, what's your preference about https://github.com/boostorg/gil/pull/596#issuecomment-863928289 about scheduling the big reformat? /cc @lpranam @simmplecoder

---

## Comment 35

> Username: lpranam  
> Created_at: 2021-06-24 13:27:28 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-867636383  

i'd suggest first week after the release.

---

## Comment 36

> Username: lpranam  
> Created_at: 2021-08-19 12:24:41 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-901871074  

So boost 1.77 is released now we can move ahead with refactoring now...

---

## Comment 37

> Username: mloskot  
> Created_at: 2021-08-20 09:57:45 UTC  
> Url: https://github.com/boostorg/gil/pull/596#issuecomment-902579807  

I say YES!  
I am swamped with work & house construction, but I'm hoping a free weekend is coming soon, so I can finally catch up with all the GIL backlog.

---
