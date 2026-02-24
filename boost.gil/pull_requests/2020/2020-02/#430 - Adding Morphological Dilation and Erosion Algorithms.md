# #430 Adding Morphological Dilation and Erosion Algorithms [Closed]

> Username: ayushbansal07  
> Created at: 2020-02-02 00:51:20 UTC  
> Updated at: 2021-01-25 23:31:37 UTC  
> Closed at: 2021-01-25 23:30:44 UTC  
> Url: https://github.com/boostorg/gil/pull/430  

### Description  
Morphological Dilation and Erosion algorithms implemented (for grayscale images).  
This PR is also meant to be my solution for the competency test for GSoC 2020.  
  
**Dilation/Erosion** process is performed by laying the structuring element H on the image I and sliding it across the image in a manner similar to convolution.  
  
1. **Morphological Dilation** - The grayscale morphological dilation formula is written as follows :  
\[𝐼⊕𝐻\](𝑢,𝑣)=max(𝑖,𝑗)∈𝐻{𝐼(𝑢−𝑖,𝑣−𝑗)+𝐻(𝑖,𝑗)}  
2. **Morphological Erosion** - The grayscale morphological erosion formula is written as follows :  
\[𝐼⊕𝐻\](𝑢,𝑣)=min(𝑖,𝑗)∈𝐻{𝐼(𝑢−𝑖,𝑣−𝑗)+𝐻(𝑖,𝑗)}  
  
### References  
  
- https://lists.boost.org/Archives/boost/2020/01/248134.php thread with initial discussion  
- https://en.wikipedia.org/wiki/Dilation_(morphology)#Grayscale_dilation  
- https://en.wikipedia.org/wiki/Erosion_(morphology)  
  
### Further development during GSoC 2020  
  
* [Documentation](https://github.com/boostorg/gil/pull/430#issuecomment-583852074)  
* [KernelConcept or similar which partially satisfies the requirements of SequenceContainer](https://github.com/boostorg/gil/pull/430#discussion_r381472734)  
* Final review, approval and merge of this PR  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add basic test case(s)  
- [x] Add more advanced test case(s)  
- [x] Ensure all CI builds pass  
- [ ] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2020-02-02 19:06:35 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/430#pullrequestreview-351959272  

Thanks for the interesting PR.  
  
Here is my first quick review of it:  
  
1. I left number of comments to clear up trivial aspects of the proposed code first, so not much of commenting on the algorithms itself.  
  
2. Second, new features (or tweaks in behaviour of existing ones) must be covered with tests. Please, include test for the new functions you are proposing here.  
  
3. Is it necessary to include the two PNG files?

📁 include/boost/gil/image_processing/morph.hpp

```diff
   1 |+ //
```

> Username: mloskot  
> Created_at: 2020-02-02 18:40:53 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r373866131  

I'd suggest to rename the header to `morphology.hpp`

---

📁 include/boost/gil/image_processing/morph.hpp

```diff
  12 |+ #include <boost/gil/image_view_factory.hpp>
  13 |+ #include <boost/gil.hpp>
  14 |+ #include <boost/multi_array.hpp>
```

> Username: mloskot  
> Created_at: 2020-02-02 18:42:44 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r373866261  

Please, check https://github.com/boostorg/gil/wiki/Include-Directives-Order (which is linked from the `CONTRIBUTING.md`) for recommended order of includes.

---

📁 include/boost/gil/image_processing/morph.hpp

```diff
  12 |+ #include <boost/gil/image_view_factory.hpp>
  13 |+ #include <boost/gil.hpp>
  14 |+ #include <boost/multi_array.hpp>
```

> Username: mloskot  
> Created_at: 2020-02-02 18:43:22 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r373866305  

Is it really necessary to introduce new dependency on Boost.MultiArray library?

> Username: ayushbansal07  
> Created_at: 2020-02-03 01:09:21 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r373892309  

I actually wanted to discuss this. I wanted to know your opinion on what data structure should we use to define a Structuring Element. Any binary matrix like structure would work just fine. Do you think using a simple array/vector here would be more appropriate?

> Username: stefanseefeld  
> Created_at: 2020-02-03 01:24:29 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r373893826  

I agree with @mloskot 's objection, and I admit I'm a bit confused after a (rather quick, I admit) look at this PR: you include the `boost/multi_array.hpp` header, but you never use it (explicitly).  
Rather, you use a template argument `SE`, which in itself sounds perfectly fine, but I think requires to specify exactly what kind of requirements you put onto that type.  
Could we get away with using the same requirements we put on `SrcView` ?

> Username: ayushbansal07  
> Created_at: 2020-02-03 01:52:43 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r373896913  

Ohh, yes it was a mistake to have included `boost/multi_array.hpp` in current code. I was actually playing around with multi_array to figure out a way to put up a requirement check on it and I forgot to remove that header in the end. However, in the next iteration I would be requiring it as i have included this check -   
`boost::function_requires<boost::multi_array_concepts::ConstMultiArrayConcept<StructElement, 2>>();`  
@mloskot  @stefanseefeld could you please give your comments on whether using multi_array as the type of SE and using this check is sufficient and correct?

> Username: stefanseefeld  
> Created_at: 2020-02-03 01:59:38 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r373897732  

Well, as I said, I think requiring multi_array would be wrong. The whole point of parametrizing these functions with `SE` is to let users fill in that parameter with their own type, so long as it meets the requirements.  
(For avoidance of doubt: using multi_array in the example is perfectly fine.)

> Username: mloskot  
> Created_at: 2020-02-03 09:08:26 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r373987303  

@ayushbansal07  
> could you please give your comments on whether using multi_array as the type of SE and using this check is sufficient and correct?  
  
As I poined earlier, I also agree with @stefanseefeld on this: please try to avoid `multi_array` in codebase of GIL core and tests, and try to make those possibly container-agnostic. In examples, it is fine.

> Username: ayushbansal07  
> Created_at: 2020-02-03 19:01:12 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r374281476  

@mloskot @stefanseefeld I understand your point now. I have updated morphology.hpp. I now model a SE to be of type vector or any similar structure (which can support .size() functionality).

> Username: mloskot  
> Created_at: 2020-02-03 19:10:32 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r374285996  

@ayushbansal07   
> I now model a SE to be of type vector or any similar structure (which can support .size() functionality).  
  
As per one of the assertions, there is also required subscript (index) operator on element

> Username: ayushbansal07  
> Created_at: 2020-02-03 19:33:31 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r374297361  

@mloskot   
I am unable to find any check for subscript operator. I however did find a check for Container type. So would using that and replacing all my occurrences of `elem[i]` with `elem.begin()+i` be a good thing to do?

> Username: mloskot  
> Created_at: 2020-02-03 19:57:01 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r374308858  

Ideally if there is a concept representing requirements of `StructElement`, but I wouldn't bother for now.  
  
For now, I'd simply add (Doxygen-style) comments to `erode` and `dilate` explaining the requirement, similar to https://github.com/boostorg/gil/blob/8235ee645a4e8c6e20722cc9c9eebe1f8050f9b6/include/boost/gil/extension/numeric/kernel.hpp#L32  
  
In future, structuring element could be defined similarly to the kernel classes.  
Since your algorithm now sort of assumes rectangular shape of the structured element, it could reuse the `kernel_2d`, but I don't think it is necessary or even a good idea considering future improvements (e.g. ovals and other non-rectangular shape of structuring elements 😄).

> Username: mloskot  
> Created_at: 2020-02-21 17:46:50 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r382718307  

FYI, I'm marking this conversation as resolved

---

📁 include/boost/gil/image_processing/morph.hpp

```diff
  14 |+ #include <boost/multi_array.hpp>
  15 |+ 
  16 |+ using namespace boost::gil;
```

> Username: mloskot  
> Created_at: 2020-02-02 18:45:50 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r373866467  

In general, avoid putting `using namespace` directives in headers. Always use fully qualified names in a headers.

---

📁 include/boost/gil/image_processing/morph.hpp

```diff
  26 |+ 
  27 |+ template <typename SrcView, typename DstView, typename SE>
  28 |+ void convolve_with_SE(SrcView const &src_view, DstView &dst_view, SE const &se, std::size_t src_y, std::size_t src_x, MorphOperator const &op)
```

> Username: mloskot  
> Created_at: 2020-02-02 18:47:49 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r373866612  

Please, class, function names all lower-case, `convolve_with_SE`.

---

📁 include/boost/gil/image_processing/morph.hpp

```diff
  25 |+ };
  26 |+ 
  27 |+ template <typename SrcView, typename DstView, typename SE>
```

> Username: mloskot  
> Created_at: 2020-02-02 18:47:53 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r373866620  

For a reader fluent in morphological filters, `SE` does ring a bell, but in general an obscure abbreviation is better to be avoided. I'd suggest something like `StructElement` as clearer.  
There is `DstView`  and not `DV` for reason.

---

📁 include/boost/gil/image_processing/morph.hpp

```diff
  48 |+         // The cooresponding value to be read from src_view is calculated as (src_y + se_y - se_height/2, src_x + se_x - se_width/2)
  49 |+         // NOTE: This calculation is only valid when the SE dimensions are odd.
  50 |+         typename SrcView::x_iterator src_it = src_view.row_begin(src_y + se_y - se_height/2);
```

> Username: mloskot  
> Created_at: 2020-02-02 18:50:04 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r373866738  

`row_begin` does not take unsigned (`std::size_t`) but signed integral.  
Typically in GIL all `x_coord_t` and `y_coord_t` are aliases of `std::ptrdiff_t`.  
  
We need to be careful to avoid compilation warnings on signed/unsigned  mismatch.

---

📁 include/boost/gil/image_processing/morph.hpp

```diff
  69 |+ 
  70 |+ template <typename SrcView, typename DstView, typename SE>
  71 |+ void morph_impl(SrcView const &src_view, DstView &dst_view, SE const &se, MorphOperator const &op)
```

> Username: mloskot  
> Created_at: 2020-02-02 18:55:51 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r373867016  

`*_impl` functions should live in internal namespace called `boost::gil::detail`

---

📁 include/boost/gil/image_processing/morph.hpp

```diff
  20 |+ // SE refers to Structuring Element
  21 |+ // Result type after convolution with the structuring element
  22 |+ enum class MorphOperator{
```

> Username: mloskot  
> Created_at: 2020-02-02 18:56:32 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r373867066  

No CamelCase naming please. Reserve CamelCase for template parameters only.

---

📁 include/boost/gil/image_processing/morph.hpp

```diff
  21 |+ // Result type after convolution with the structuring element
  22 |+ enum class MorphOperator{
  23 |+     ERODE,
```

> Username: mloskot  
> Created_at: 2020-02-02 18:57:03 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r373867099  

Please, stick to lower case names. See `boost/gil/image_processing/threshold.hpp` for enumerations


---

## Comment 2

> Username: ayushbansal07  
> Created_at: 2020-02-03 19:05:29 UTC  
> Updated_at: 2020-02-07 00:30:50 UTC  
> Url: https://github.com/boostorg/gil/pull/430#issuecomment-581566588  

Updates -   
1. Resolved all the trivial coding issues.  
2. Added a test for erosion and Dilation. (Will add more tests in the meanwhile, marked as TODO).  
3. Removed .png files.  
Also, I modified morphology.hpp to better handle the corner cases.   
  
TODOs -   
- [x] Add more tests.

---

## Comment 3

> Username: mloskot  
> Created_at: 2020-02-03 19:23:58 UTC  
> Url: https://github.com/boostorg/gil/pull/430#issuecomment-581576130  

@ayushbansal07   
> Updates -  
>  ...  
  
This is looking good. Thanks for your efforts to improve this. I'll get another look and review near Wednesday.  
  
> TODOs -  
> ...  
  
I've taken the liberty and added those as the PR tasklist which you can also edit.  
  
------  
  
Another thing, you mentioned on the Boost mailing list you will be proposing those algorithms as part of your **competency test**. If you are working on this PR as the competency test, please update the description to make it clear this is your solution for the GSoC 2020 competency test.  
  
Since working on PR may involve _much more effort_ than working just on C++ code of the competency test solution, you are not expected to finish-polish the PR in order to count it for a completed competency test solution. In other words, your PR does not have to be approved and merged to consider the test completed. For the competency test, the gist of C++ code is most important.  
  
Although you can postpone addressing further reviews and completion of this PR for later (e.g. during GSoC), you are very welcome to continue working on to make it ready for merging. It's your call.  
  
/cc @stefanseefeld unless he does not agree and would like to clear my explanation.

---

## Comment 4

> Username: ayushbansal07  
> Created_at: 2020-02-03 20:06:55 UTC  
> Url: https://github.com/boostorg/gil/pull/430#issuecomment-581594149  

@mloskot   
> please update the description to make it clear this is your solution for the GSoC 2020 competency test.  
  
I have updated the description.   
  
I'd be happy to continue working on this and want to try my best to get it to merge before submitting my GSoC Proposal :)

---

## Review 5 [Changes requested]

> Username: mloskot  
> Created_at: 2020-02-05 00:12:07 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/430#pullrequestreview-353384484  

I have added a bunch of nitpicks :-)  
  
We have a prototype of benchmarks, currently in dedicated branch, that you may find interesteing. Here you can see comparison of various pixel iteration methods available in GIL  
  
- https://github.com/boostorg/gil/blob/benchmark/benchmark/celero/histogram_gray8.cpp  
- https://github.com/boostorg/gil/blob/benchmark/benchmark/celero/charts/histogram_gray8_msvc1421_HistogramGray8.html (save it on disk and open in browser)  
  
------  
  
For me, **this PR as it is now is a complete solution of your competency test** and as I mentioned in https://github.com/boostorg/gil/pull/430#issuecomment-581576130 you are not required to make it a feature complete PR before the GSoC starts  
  
If you still want to keep working on it, please consider add some some documentation e.g. Doxygen-like comments for `erode` and `dilate` routines.

📁 example/morphology.cpp

```diff
   5 |+ #include <boost/multi_array.hpp>
   6 |+ 
   7 |+ #define SE_SIZE 3
```

> Username: mloskot  
> Created_at: 2020-02-04 23:51:22 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r374989837  

Well well well, macros, really? ;)

> Username: ayushbansal07  
> Created_at: 2020-02-05 23:51:26 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r375573339  

Sorry, college habbits :P

---

📁 example/morphology.cpp

```diff
  19 |+     int se_arr[SE_SIZE][SE_SIZE] = {{0,1,0},{1,1,1},{0,1,0}};
  20 |+ 
  21 |+     typedef boost::multi_array<int, 2> se_type;
```

> Username: mloskot  
> Created_at: 2020-02-04 23:52:14 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r374990120  

Since this is C++11 code, I'd consider `using` where the most important part of the alias, the name, comes first, on left.


📁 include/boost/gil/image_processing/morphology.hpp

```diff
   6 |+ // http://www.boost.org/LICENSE_1_0.txt)
   7 |+ //
   8 |+ #ifndef BOOST_GIL_IMAGE_PROCESSING_MORPH_HPP
```

> Username: mloskot  
> Created_at: 2020-02-04 23:54:01 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r374990656  

Should read `BOOST_GIL_IMAGE_PROCESSING_MORPHOLOGY_HPP`

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
   9 |+ #define BOOST_GIL_IMAGE_PROCESSING_MORPH_HPP
  10 |+ 
  11 |+ #include <boost/gil/image_view_factory.hpp>
```

> Username: mloskot  
> Created_at: 2020-02-04 23:54:22 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r374990755  

Since you include `<boost/gil.hpp>`, this one is redundant.

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  18 |+ // Result type after convolution with the structuring element
  19 |+ enum class morph_operator{
  20 |+     ERODE,
```

> Username: mloskot  
> Created_at: 2020-02-04 23:56:10 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r374991271  

Nitpicks:  
- `morph_operator` should read `morphology_operator`  
- the make enumerators should be lower-case e.g. `erode`

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  42 |+     std::size_t struct_elem_width = struct_elem[0].size();
  43 |+ 
  44 |+     // Iterating for every position in the Structuring Element
```

> Username: mloskot  
> Created_at: 2020-02-04 23:56:59 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r374991546  

Nitpick: superfluous comment. The loop is clear what is iterated.

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  58 |+     		{
  59 |+                 auto x_to_read = src_x + struct_elem_x - struct_elem_width/2;
  60 |+                 if(x_to_read < 0 || x_to_read>=src_view.width()) continue;
```

> Username: mloskot  
> Created_at: 2020-02-04 23:58:07 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r374991892  

Please, move `continue` to separate line and use whitespaces around operators e.g. `>=`

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  84 |+     for(std::ptrdiff_t src_y = 0; src_y < height; ++src_y)
  85 |+     {
  86 |+     	for(std::ptrdiff_t src_x = 0; src_x < width; ++src_x)
```

> Username: mloskot  
> Created_at: 2020-02-04 23:59:20 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r374992252  

Indent with 4 spaces, as per https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md#guidelines


---

## Comment 6

> Username: ayushbansal07  
> Created_at: 2020-02-06 00:32:01 UTC  
> Updated_at: 2020-02-06 00:32:36 UTC  
> Url: https://github.com/boostorg/gil/pull/430#issuecomment-582679628  

@mloskot   
I have fixed the mentioned nitpicks.  
  
> Here you can see comparison of various pixel iteration methods available in GIL  
  
Thank you for this interesting comparison. If you don't mind me asking, I wanted to know, just of out curiosity, what could be the reason behind such huge run time of 2d_y_iterator. Is it because each iteration would result in a cache miss?

---

## Comment 7

> Username: mloskot  
> Created_at: 2020-02-06 09:02:09 UTC  
> Url: https://github.com/boostorg/gil/pull/430#issuecomment-582802625  

@ayushbansal07   
> what could be the reason behind such huge run time of 2d_y_iterator.  
> Is it because each iteration would result in a cache miss?  
  
Yes. I recommend to watch the original video lecture about GIL presented by Lubomir Bourdev  
which is hosted on YouTube and linked from the documentation here https://boostorg.github.io/gil/html/tutorial/video.html  
The Y-iterator and cache issue starts from around 8:00.

---

## Comment 8

> Username: ayushbansal07  
> Created_at: 2020-02-07 00:30:38 UTC  
> Url: https://github.com/boostorg/gil/pull/430#issuecomment-583178226  

@mloskot   
I have added advanced test cases too. Please let me know further steps to complete this PR :-)

---

## Review 9 [Changes requested]

> Username: mloskot  
> Created_at: 2020-02-09 10:59:52 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/430#pullrequestreview-355598839  

Could you also document template parameters using n D oxygen `\tparam` and description of type requirements or mention of concept, etc.  
  
### Optional   
  
Unfortunately, we don't have structured prose documentation for the Image Processing features. However, perhaps you could also start **scratching** two Markdown files with prose for dilation and erosion, describing what's the algorithm does, how to use it, what are limitations, corner cases or not supported cases,  include references or names of algorithms involved.

---

## Comment 10

> Username: ayushbansal07  
> Created_at: 2020-02-09 14:34:39 UTC  
> Url: https://github.com/boostorg/gil/pull/430#issuecomment-583852074  

Would it be okay if I did the documentation part later (during GSoC time)? I was asking this because I was planning to implement a separate class for Structuring Element as first step of my GSOC project and some of those corner cases, etc. would depend on the design of Structuring Element class. So, I was wondering if all of that could be done together afterwards.

---

## Comment 11

> Username: mloskot  
> Created_at: 2020-02-09 20:00:44 UTC  
> Url: https://github.com/boostorg/gil/pull/430#issuecomment-583886587  

> Would it be okay if I did the documentation part later (during GSoC time)?   
> (...)  
> So, I was wondering if all of that could be done together afterwards.  
  
Yes, certainly. That's what I also suggested in https://github.com/boostorg/gil/pull/430#issuecomment-581576130  
  
I think, your PR is completed from the competency test stand point, but to get it merged it will need that extra bit of work during the GSoC. So, let's keep it open as work in progress for now.  
  
/cc @stefanseefeld

---

## Review 12 [Changes requested]

> Username: mloskot  
> Created_at: 2020-02-19 18:49:11 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/430#pullrequestreview-361332548  

📁 example/morphology.cpp

```diff
   1 |+ #include <boost/gil/extension/io/png.hpp>
```

> Username: mloskot  
> Created_at: 2020-02-19 18:29:32 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r381462693  

Comment with licence and your copyright is missing


📁 include/boost/gil/image_processing/morphology.hpp

```diff
  10 |+ 
  11 |+ #include <boost/assert.hpp>
  12 |+ #include <boost/gil.hpp>
```

> Username: mloskot  
> Created_at: 2020-02-19 18:31:49 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r381463991  

We should avoid including the all-in-one header `#include <boost/gil.hpp>`  
If this PR is accepted, it will include `morphology.hpp` leading to cyclic dependency.  
Could you try to include individual headers of stuff that is used (yes, it may be tricky)?

> Username: ayushbansal07  
> Created_at: 2020-02-21 16:48:46 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r382690155  

@mloskot I had a little doubt about this. After I removed `#include <boost/gil.hpp>`, my example and test files compiled without any errors. I was under the impression that definitions for concepts such as `ImageViewConcept` would be included from the all-in-one header, but I guess that is not the case. There should have been an error according to me, but wasn't. Is there something that I am missing?

> Username: mloskot  
> Created_at: 2020-02-21 19:21:02 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r382760898  

@ayushbansal07 You are correct to be suspicious.  
  
I have just fixed one issue in `Jamfile`, https://github.com/boostorg/gil/commit/d026d50e178eecf6d2468f955ccaf4983ada9663, so please update your branch to get this fix.  
  
Now, if you build the standalone headers tests - tests that verify each header can be compiled alone - `b2 cxxstd=11 test/core/headers`, then you should see that your header does not compile:  
  
![image](https://user-images.githubusercontent.com/80741/75064367-17b2e500-54e7-11ea-85be-049588dd666e.png)  
  
I also tried to compile your `example/morphology.cpp` using MSVC from VS2019 and it is also failing:  
  
![image](https://user-images.githubusercontent.com/80741/75064655-a3c50c80-54e7-11ea-8f42-768b6460b007.png)

> Username: ayushbansal07  
> Created_at: 2020-02-21 23:39:27 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r382855186  

I have now fixed all the errors and warnings for both example and test file.

> Username: mloskot  
> Created_at: 2020-02-22 12:44:10 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r382910075  

Thanks

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  16 |+ // SE refers to Structuring Element
  17 |+ // Result type after convolution with the structuring element
  18 |+ enum class morphology_operator{
```

> Username: mloskot  
> Created_at: 2020-02-19 18:32:11 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r381464187  

Nitpick:  
  
```  
enum class morphology_operator{  
```  
to  
```  
enum class morphology_operator  
{  
```

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  47 |+         // NOTE: This calculation is only valid when the struct_elem dimensions are odd.
  48 |+         auto y_to_read = src_y + struct_elem_y - struct_elem_height/2;
  49 |+         if(y_to_read < 0 || y_to_read >= src_view.height())
```

> Username: mloskot  
> Created_at: 2020-02-19 18:36:03 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r381466406  

Nitpick: `for(` -> `for (` and `if(` -> `if (`, here and everywhere.

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  54 |+         {
  55 |+             // We need to consider only those positions which have 1 in Structuring Element
  56 |+             if(struct_elem[struct_elem_x][struct_elem_y] == 1)
```

> Username: mloskot  
> Created_at: 2020-02-19 18:36:10 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r381466487  

Loose comment, a while ago I personally started appreciating the early exits and continuations as recommended by https://llvm.org/docs/CodingStandards.html#use-early-exits-and-continue-to-simplify-code, this also helps to avoid too deep indentations what is a good idea for long names of templated types, etc.

> Username: ayushbansal07  
> Created_at: 2020-02-21 23:46:42 UTC  
> Updated_at: 2020-02-21 23:46:52 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r382856720  

I too prefer early exits and continuations. But, for this case I this way seemed more appropriate to me. The only reason being this flow is more coherent with the natural flow of the algorithm and for a new reader it would be more intuitive. And also, there is not much deep indentation in this part of code. However, if you still feel if I should use early continuation here, I can do that?

> Username: mloskot  
> Created_at: 2020-02-22 12:43:56 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r382910059  

It's fine as it is. The number of nested levels is not large to make it a problem. I pointed at the issues to be aware as a tool to avoid code drift towards the right too much :)

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  63 |+                     min = src_point_val;
  64 |+                 if (src_point_val > max)
  65 |+                     max = src_point_val;
```

> Username: mloskot  
> Created_at: 2020-02-19 18:37:43 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r381467470  

I'm curious, why these are `point`-s? Just `src_value` would be clear? Values of image are pixels...

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
 103 |+ /// \param dst_view - The destination image view (after performing dilation).
 104 |+ /// \param struct_elem - The Structuing Element. Should be a 2D-array type consiting of binary int values (0 and 1).
 105 |+ /// \tparam SrcView type of source image. Should confirm to gil::ImgaeViewConcept.
```

> Username: mloskot  
> Created_at: 2020-02-19 18:40:28 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r381468912  

> Should confirm to gil::ImgaeViewConcept.  
  
should just read  
  
> Models gil::ImgaeViewConcept.  
  
If you are not familiar with notion of model w.r.t. to concept, here you can find nicely documented examples  
https://www.boost.org/libs/geometry/doc/html/geometry/reference/concepts.html  
  
(we need similar documentation for GIL :))

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
 105 |+ /// \tparam SrcView type of source image. Should confirm to gil::ImgaeViewConcept.
 106 |+ /// \tparam DstView type of destination image. Should confirm to gil::MutableImageViewConcept.
 107 |+ /// \tparam StructElement type of structuring element. Should provide size() and operator[].
```

> Username: mloskot  
> Created_at: 2020-02-19 18:47:20 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r381472734  

In future, for such kernel-like containers, we may have `KernelConcept` or similar which partially satisfies the requirements of [SequenceContainer](https://en.cppreference.com/w/cpp/named_req/SequenceContainer).

> Username: ayushbansal07  
> Created_at: 2020-02-21 23:40:15 UTC  
> Updated_at: 2020-02-21 23:40:16 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r382855343  

Will implement it during the GSoC period :)

> Username: mloskot  
> Created_at: 2020-02-22 12:46:57 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r382910247  

Sure. I added "Further development during GSoC 2020" section to the PR description.


📁 test/core/image_processing/morphology.cpp

```diff
   1 |+ #include <boost/gil/image_processing/morphology.hpp>
```

> Username: mloskot  
> Created_at: 2020-02-19 18:48:02 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r381473107  

Comment with licence and your copyright is missing

---

📁 test/core/image_processing/morphology.cpp

```diff
 337 |+     test_erosion_big();
 338 |+ 
 339 |+     return boost::report_errors();
```

> Username: mloskot  
> Created_at: 2020-02-19 18:49:05 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r381473672  

This is a good bunch of test cases.  
  
In future, I'd suggest to convert it to Boost.Test.

> Username: ayushbansal07  
> Created_at: 2020-02-21 23:29:36 UTC  
> Updated_at: 2020-02-21 23:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/430#discussion_r382852904  

Sure. Will do it during GSOC :)


---

## Comment 13

> Username: ayushbansal07  
> Created_at: 2020-02-21 16:51:46 UTC  
> Url: https://github.com/boostorg/gil/pull/430#issuecomment-589738477  

@mloskot Really sorry for the late response. I was a little busy with my semester exams. I have made the required changes :)

---

## Comment 14

> Username: mloskot  
> Created_at: 2020-02-21 17:49:43 UTC  
> Updated_at: 2020-02-21 19:23:12 UTC  
> Url: https://github.com/boostorg/gil/pull/430#issuecomment-589761117  

@ayushbansal07   
> Really sorry for the late response.  
  
There is no need to be sorry. It's your PR, so you work on it at your own pace.  
  
> I have made the required changes :)  
  
Please, hit the `Resolve conversation` button for each item you address or add comment explaining a thing is not addressed. This helps to keep completed review iterations cleared.  
  
### Warnings  
  
@ayushbansal07  Please, could you also check compilation output of your tests and examples and clean up any compilation warnings caused by your code? Compilation of GIL code still outputs lots of warnings and we are (slowly) clearing that, but we definitely want to avoid introduction of new warnings.

---

## Comment 15

> Username: ayushbansal07  
> Created_at: 2020-02-22 06:52:28 UTC  
> Url: https://github.com/boostorg/gil/pull/430#issuecomment-589926001  

@mloskot I have made relevant changes to make sure that the Cl builds and has minimal warnings.   
  
Also, FYI, I have checked **Add more advanced test case(s)** and **Ensure all CI builds pass** in the task list.

---

## Comment 16

> Username: mloskot  
> Created_at: 2020-02-22 12:59:50 UTC  
> Url: https://github.com/boostorg/gil/pull/430#issuecomment-589953908  

@ayushbansal07 in https://github.com/boostorg/gil/pull/430#issuecomment-581594149  
> I'd be happy to continue working on this and want to try my best to get it to merge before submitting my GSoC Proposal  
  
Thank you for the very good job you've done. I consider it as more than complete for your competency test.  
  
Although the PR is very close to be approved and merged, we are not in rush  
and I'd prefer to defer merging. I also mentioned it in https://github.com/boostorg/gil/pull/430#pullrequestreview-353384484   
  
My reasoning is to :  
- give myself a bit more time to try your code out in action (I have only read-only reviewed it).  
- give other active/interested developers and users a chance to have a look and comment (/cc @stefanseefeld, @lpranam, @simmplecoder, @miralshah365, @chhenning).  
- leave it for you, in case you are approved as student, as an easy first task to completed as part of your GSoC work (an early success is an important element of GSoC :-))  
  
Regardless if you will participate during GSoC or not, since this PR is pretty much completed, I'm fairly sure your contribution will be merged eventually.

---

## Comment 17

> Username: ayushbansal07  
> Created_at: 2020-02-26 10:33:44 UTC  
> Updated_at: 2020-02-26 10:34:08 UTC  
> Url: https://github.com/boostorg/gil/pull/430#issuecomment-591356643  

@mloskot   
Thank you for your reviews. I too agree to defer the merging mostly because of the last reason that you mentioned :)  
  
Also, if it is not much of a trouble for you, please feel free to mention me in any discussion that might potentially help me understand more about GIL.  
  
Thank you again! :)

---

## Comment 18

> Username: mloskot  
> Created_at: 2020-02-26 11:23:05 UTC  
> Updated_at: 2020-02-26 11:25:00 UTC  
> Url: https://github.com/boostorg/gil/pull/430#issuecomment-591377136  

@ayushbansal07   
> please feel free to mention me in any discussion that might potentially help me understand more about GIL  
  
Sure, but nothing immediately comes to mind. Feel free to browse the issues, check the [good first issue](https://github.com/boostorg/gil/issues?q=is%3Aissue+is%3Aopen+label%3Agood-first-issue) list.  
  
For understanding GIL, I highly recommend getting familiar with these topics from [GIL docs](https://boostorg.github.io/gil/develop/doc/html/index.html):  
  
- The video lecture  
- The three tutorials  
- These from the design guide:  
    - Channel  
    - Color Space and Layout  
    - Color Base  
    - Pixel  
    - Pixel Iterator  
    - Pixel Locator  
    - Image View  
    - Image  
  
These are essential parts to understand to be comfortable with using GIL. Mind you, I'm not suggesting to memorise the API or any details of implementation, but learn vocabulary of GIL, what are the building blocks, features they offer, how to use them, when to use them.  
  
If anything needs clarifying, don't hesitate to ask questions on the boost-gil list.  
If you think anything is missing or wrong, please, open new issue.  
  
This should make a busy GSoC preparation for next days :)

---

## Comment 19

> Username: mloskot  
> Created_at: 2021-01-25 23:30:44 UTC  
> Url: https://github.com/boostorg/gil/pull/430#issuecomment-767179677  

This is being replaced with more feature-complete morphological operations implementation in #541  
  
Thanks @ayushbansal07 for your work here.

---
