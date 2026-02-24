# #541 Added all standard morphological transformations [Merged]

> Username: meshtag  
> Created at: 2021-01-21 19:52:20 UTC  
> Updated at: 2021-08-21 18:50:25 UTC  
> Merged at: 2021-02-13 16:01:22 UTC  
> Closed at: 2021-02-13 16:01:22 UTC  
> Url: https://github.com/boostorg/gil/pull/541  

### Description  
  
This pull request adds all standard morphological transformations in Boost Gil library.  
  
Summary of applied morphological concepts:  
1. Dilation: Gives the maximum overlapped value with respect to the structuring element to the pixel overlapping with the center element of structuring element.We can vary the number of times dilation happens by varying the   
argument 'iterations' in the dilate function.  
2. Erosion: Give the minimum overlapped value with respect to the structuring element to the pixel overlapping with the center element of structuring element.We can vary the number of times erosion happens by varying the   
argument 'iterations' in the erode function.  
3. Opening: Opening is just another name of erosion followed by dilation.  
   It is useful in removing noise.  
4. Closing: Closing is reverse of Opening, Dilation followed by Erosion.  
   It is useful in closing small holes inside the foreground objects, or small black points on the object.  
5. Morphological Gradient: It is the difference between dilation and erosion of an image.  
  The result will look like the outline of the object.  
6. Top Hat: It is the difference between input image and Opening of the image.  
7. Black Hat: It is the difference between the closing of the input image and input image.  
  Functions have been made for applying above morphological transformations.  
  
### References  
  
https://homepages.inf.ed.ac.uk/rbf/HIPR2/dilate.htm  
https://homepages.inf.ed.ac.uk/rbf/HIPR2/erode.htm  
https://docs.opencv.org/master/d9/d61/tutorial_py_morphological_ops.html  
  
### Tasklist  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass - RIP Travis CI, ignored and replaced with GitHub Actions  
- [x] Review and approve

---

## Review 1 [Commented]

> Username: simmplecoder  
> Created_at: 2021-01-24 17:39:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/541#pullrequestreview-574948252  

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  32 |+ namespace detail{
  33 |+ template <typename SrcView, typename DstView, typename Kernel>
  34 |+ void morph_impl(SrcView const& src_view, DstView const& dst_view, Kernel const& kernel,const int identifier)
```

> Username: simmplecoder  
> Created_at: 2021-01-24 16:44:04 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r563318153  

Shouldn't `identifier` be type of `morphological_operations` instead of `int`?

> Username: simmplecoder  
> Created_at: 2021-01-24 17:05:52 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r563320785  

If you'll read my review further you won't need to change this part like that.

> Username: mloskot  
> Created_at: 2021-01-24 19:40:02 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r563339412  

@meshtag Nitpick: The `const` is unnecessary for the `identifier`, besides   
  
Prefer [east const](http://slashslash.info/eastconst/) over west const, i.e. `T const identifier` etc.

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  34 |+ void morph_impl(SrcView const& src_view, DstView const& dst_view, Kernel const& kernel,const int identifier)
  35 |+ {
  36 |+     int flip_ker_row, flip_ker_col, row_boundary, col_boundary;
```

> Username: simmplecoder  
> Created_at: 2021-01-24 16:45:06 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r563318265  

Shouldn't these be `std::ptrdiff_t` as well?

> Username: meshtag  
> Created_at: 2021-01-25 05:34:36 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r563472244  

yeah,they should be

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  41 |+         {
  42 |+             max_overlapped_element = (std::numeric_limits<typename channel_type<SrcView>::type>::min)();
  43 |+             min_overlapped_element = (std::numeric_limits<typename channel_type<SrcView>::type>::max)();
```

> Username: simmplecoder  
> Created_at: 2021-01-24 17:03:01 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r563320481  

Great catch, but unfortunately will not work for our quirky types like what you get when `SrcView == gil::gray32f_view_t` and the like. I would just set to the first element and mention in every function that uses this to not pass empty views, or bad things will happen.

> Username: meshtag  
> Created_at: 2021-01-25 05:52:43 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r563476838  

Okay,i will mention about not passing empty views in docs and in comments of example program(gil/example/morphology.cpp)

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  61 |+                             max_overlapped_element = src_view(col_boundary, row_boundary);
  62 |+                         if(src_view(col_boundary, row_boundary) < min_overlapped_element)
  63 |+                             min_overlapped_element = src_view(col_boundary, row_boundary);
```

> Username: simmplecoder  
> Created_at: 2021-01-24 17:05:06 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r563320675  

I believe you forgot to multiply by kernel value at that point.  
  
I believe you should create a function pointer before entering those loops, so that only one element is searched for (e.g. either min or max, but not both). The easiest option I see is to accept a functor that choose the right element by changing the declaration to this:  
  
```  
template <typename SrcView, typename DstView, typename Kernel, typename SelectionFunctor>  
void morph_impl(SrcView const& src_view, DstView const& dst_view, Kernel const& kernel, SelectionFunctor selector)  
```  
  
Then the four lines will be reduced to this:  
  
```  
    target_element = selector(target_element, src_view(col_boundary, row_boundary));  
```  
  
Then just `switch` inside `morph` to pass correct functor. Remember, you have to pass `&std::max<channel_type>` or `&std::min<channel_type>`, otherwise you cannot take a pointer to template function, you have to explicitly tell which version you need.

> Username: meshtag  
> Created_at: 2021-01-25 12:13:00 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r563677095  

Yeah my bad, I forgot to multiply.  
Regarding the second point,can we solve it with simple if else conditions like  
```  
 product = src_view(col_boundary, row_boundary) * kernel.at(flip_ker_row, flip_ker_col);  
                        if(identifier == 1)  
                        {  
                            if(product > max_overlapped_element)  
                                max_overlapped_element = src_view(col_boundary, row_boundary);  
                        }  
                        else if(identifier == 0)  
                        {  
                            if(product < min_overlapped_element)  
                                min_overlapped_element = src_view(col_boundary, row_boundary);  
                        }  
```  
or is there any other specific reason for using functors, I am fine with either approaches

> Username: mloskot  
> Created_at: 2021-01-25 20:51:00 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r564034377  

@meshtag To me, one of reasons to prefer functor or named lambda is readability:  
- here we search max, there we search min, and I don't have to look further to find the branching, and decode it in my brain when happens what  
- `if(identifier == 1)` is just a terribly obscure: wtf is the `identifier`, wtf is the magic `1` - how on earth the magic `identifier == 1` corresponds to searching for min/max?!  
  
By he way, you create an enum earlier, why you are refusing to use it and preferring the magic numbers?  
Please, write code for humans first, then for machines!

> Username: meshtag  
> Created_at: 2021-01-26 03:11:09 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r564187821  

@mloskot ,got the point

> Username: mloskot  
> Created_at: 2021-01-26 09:41:16 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r564373940  

@meshtag The message I was trying to carry through in the chatty way was that if we write code [Sean Parent's 1st goal](https://www.youtube.com/watch?v=W2tWOdzgXHA) in mind for [better code](https://www.youtube.com/results?search_query=sean+parent+better+code), making intentions more explicit and well named, the life would be easier :-)

> Username: meshtag  
> Created_at: 2021-01-26 13:11:56 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r564498325  

Okay, thanks @mloskot ,I will try to name my variables and functions in a more expressive sense from now on.

> Username: meshtag  
> Created_at: 2021-01-26 19:25:03 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r564772784  

@simmplecoder ,  
I am having trouble figuring out what exactly you wanted me to do here   
``` Remember, you have to pass &std::max<channel_type> or &std::min<channel_type>```  
can u please elaborate ,I understand we cannot have a pointer to a template and I am trying to figure out a way to make our function pointer generic w.r.t all gil views.

> Username: meshtag  
> Created_at: 2021-01-27 17:46:03 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r565509170  

@simmplecoder ,  
In my recent commit , I have handled this part as   
```  
if(identifier == boost::gil::morphological_operations::dilation)  
                            {  
                                if(src_view(col_boundary, row_boundary) > target_element)  
                                    target_element = src_view(col_boundary, row_boundary);  
                            }  
                            else if(identifier == boost::gil::morphological_operations::erosion)  
                            {  
                                if(src_view(col_boundary, row_boundary) < target_element)  
                                    target_element = src_view(col_boundary, row_boundary);  
                            }  
```  
since I was not sure regarding the implementation of functor you suggested.Please check this part and tell me if I need to improve it further.

> Username: simmplecoder  
> Created_at: 2021-02-01 08:11:43 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r567628529  

@meshtag, I will send you a patch regarding the function switch depending on the erosion/dilation mode. You can resolve this thread for now, good job.

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  78 |+     using namespace boost::gil;
  79 |+     BOOST_ASSERT(ker_mat.size() != 0 && src_view.dimensions() == dst_view.dimensions());
  80 |+     gil::gray8_image_t intermediate_img(src_view.dimensions());
```

> Username: simmplecoder  
> Created_at: 2021-01-24 17:07:07 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r563320933  

Do we need this?

> Username: meshtag  
> Created_at: 2021-01-25 12:18:22 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r563680469  

Yes I think we need this,dilate function has  
```  
for(int i=0;i<iterations;++i)  
        morph(dst_view,dst_view,ker_mat,1);  
```  
so I am passing dst_view as input view as well as output view in morph ,if I do not create a dummy/intermediate image to pass in morph_impl,modified values of dst_view will be used to modify new values in dst_view,this will not output the result of typical dilation.Moreover,if i choose to pass a separate image view than dst_view in dilate,then i would have to create new image views for each and every iteration which seems like a bad idea from the point of view of space complexity. I understand that all images will not be of the type gray8_image_t and I am thinking of a way to solve it.Feel free to share what you think of it.

> Username: mloskot  
> Created_at: 2021-01-25 20:53:37 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r564035801  

@meshtag  
> I am passing dst_view as input view as well as output view in morph  
  
I read this and it immediately smells to me, sorry.  
First, we use `dst_` as convention for output-only, a result destination.  
Second, how `T const& v` can be an output?  
Or I don't understand what is happening.

> Username: meshtag  
> Created_at: 2021-01-26 04:02:21 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r564203314  

@mloskot   
1.Regarding the first point,okay I will be more careful about the terminologies/conventions used from now on.Thanks for pointing that out.  
2.Regarding the second point,documentation states that gil views are similar to iterators(pointers),so when I am writing ```T const& v ``` where v is a gil view,it should mean that I am using something similar to a constant pointer and not a pointer with constant value,this allows me to fill dst_view .I am not sure if that answers your question,can you please elaborate?please correct me if I am wrong.

> Username: mloskot  
> Created_at: 2021-01-26 09:50:18 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r564380469  

The 1. is simply about striving for naming things as clearly as possible.  
  
Yes, you are correct about the 2. and the [docs states it quite clearly indeed](https://www.boost.org/doc/libs/1_75_0/libs/gil/doc/html/tutorial/gradient.html):   
  
> ```  
> void x_gradient(gray8c_view_t const& src, gray8s_view_t const& dst)  
> ```  
> ...  
>  Whether a view is mutable or read-only (immutable) is a property of the view type.  
  
I'm sorry, I must be thinking of something else last night.  
  
Well, I can also see this may be confusing to those new to GIL and those used to understanding `T const&` as read-only stuff. Since,  
  
> A GIL image view, is a shallow, lightweight view of a rectangular grid of pixels.  
  
we could do just:  
  
```cpp  
void x_gradient(gray8c_view_t input, gray8s_view_t output)  
```  
  
Something to think about for future readability and code quality refactoring in GIL :-)

> Username: meshtag  
> Created_at: 2021-01-26 14:00:11 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r564530540  

@mloskot   
```gray8s_view_t``` specify what we can do to the image pixels,but I am not sure if it specifies whether we can change the entity to which the view(pointer/iterator like entity)points,what if after passing the view,someone tries to modify it (possibly by operator overloading) like this ```dst_view += 1```(the way we do it with a normal pointer) inside the function,will our compiler shout at them to stop it because modifying to what the view is pointing inside a function would certainly create problems, I would look into the matter more closely once I finish working on this pull request.The above problem is certainly solved if we pass the view in the current manner ```DstView const& dst_view``` since it would create something like a constant pointer,I need to do a lot of testing before I arrive on a conclusion.

> Username: mloskot  
> Created_at: 2021-01-26 16:09:33 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r564635260  

> the view(pointer/iterator like entity)  
  
Initially, it is not a major error to think of view as pointer or iterator, but at this stage I'd suggest to make the clear distinction.  
There used to be a great article by Maciej Sobczak archived online "STL Sequences & the View Concept", C/C++ Users Journal, April 2004 Vol. 22, No. 4 but it seems no longer online. It explained the concept of a view very well. You may look at the docs of https://www.zib.de/weiser/vtl/ to grasp the idea.  
  
Essentially, a view is a container adaptor that allows us to 'see' a container in certain way - think of an eyewear.  
So, it is not really equivalent of a pointer or iterator, but view interacts with pointers or iterators.  
In GIL, view provides access to iterators and locators (another GIL concept) over the underlying sequence of pixels.  
  
> what if after passing the view,someone tries to modify it   
  
Let's try https://godbolt.org/z/vbdne5  
  
```cpp  
#include <boost/gil.hpp>  
namespace gil = boost::gil;  
  
int main()  
{  
    gil::gray8_view_t rw;  
    auto rw_pixel = rw(1, 1);  
    rw(1, 1) = 128;  
  
    gil::gray8c_view_t ro;  
    auto ro_pixel = ro(1, 1);  
    ro(1, 1) = 128; // and?  
}  
```  
  
By the way, from `04:00` minute the [GIL Video Lecture](https://www.youtube.com/watch?v=sR8Wjg0pceE&t=241) explains what is the `c` in `gray8c_view_t`, what is `s` in `gray8s_view_t`, etc.  
  
> what if after passing the view,someone tries to modify it (possibly by operator overloading) like this `dst_view += 1` (the way we do it with a normal pointer) inside the function  
  
Because the `image_view` is **not** an iterator or pointer, there is nothing in it to modify like that, and there is no such `operator+=`. That is what makes the view lightweight too. The `image_view` class has only two members `dimensions` and `pixels` and there is no way you can modify it apart from assigning one view to another what will completely reset the two resulting in a completely new view.

> Username: meshtag  
> Created_at: 2021-01-26 19:28:42 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r564775233  

Okay I think I get the difference between iterators and views now.Thanks a lot @mloskot  for helping me improve my understanding of this concept .

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  83 |+         morph_impl(
  84 |+             nth_channel_view(src_view, i),
  85 |+             nth_channel_view(view(intermediate_img), i),
```

> Username: simmplecoder  
> Created_at: 2021-01-24 17:08:49 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r563321212  

Shouldn't this be:  
  
```  
nth_channel_view(view(dst_vuew), i),  
```  
  
?

> Username: simmplecoder  
> Created_at: 2021-01-24 17:11:28 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r563321479  

I see it now. You need compatible pixel and channel types. Lets just mention in the docs that we require those to be compatible and leave it at that. Let the user deal with that, if there are some common practices, we will just implement them later when they get established. In the worst case, they can write their own adaptor on top of a view.

> Username: meshtag  
> Created_at: 2021-01-25 19:17:17 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r563979762  

My earlier comment was an attempt to clarify this.Please do have a look at it.

> Username: mloskot  
> Created_at: 2021-01-25 20:55:05 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r564036648  

@simmplecoder   
> You need compatible pixel and channel types. Lets just mention in the docs that we require those to be compatible  
  
Certainly, a comment/doc is a good idea.  
A `static_assert` **as a comment/doc** is even better, I think :-)

> Username: simmplecoder  
> Created_at: 2021-02-01 08:20:09 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r567633060  

@meshtag, please implement the check @mloskot suggested. I believe the right way is to use `gil_function_requires<PixelsCompatibleConcept<SrcPixelType, DstPixelType>>()`.

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
 123 |+ void dilate(SrcView const& src_view, DstView const& dst_view,Kernel const& ker_mat,const int iterations)
 124 |+ {
 125 |+     boost::gil::transform_pixels(src_view,dst_view,[](const boost::gil::gray8_pixel_t& p){return p[0];});
```

> Username: simmplecoder  
> Created_at: 2021-01-24 17:35:14 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r563324361  

I doubt it will work like this, leaving aside  that not every view will have `gray8_pixel_t` as pixel type. Can't nail down counterexample, but it will look like this:  
```  
input = [  
    [max_value, std_value, std_value, std_value],  
    [std_value] * 4,  
    [std_value] * 4  
]  
  
kernel = [1] * 3 * 3  
```  
  
Please test dilation with the input above. `std_value` is something lower than `max_value`. I expect first 3x3 segment to be `max_value` and final column to be `std_value`.

> Username: meshtag  
> Created_at: 2021-01-25 06:54:10 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r563495103  

I tested this as ....  
```  
input = [{ 255, 100, 100, 100},  
            { 100, 100, 100, 100},  
            { 100, 100, 100, 100}]  
  
kernel = [{1,1,1},  
               {1,1,1},  
               {1,1,1}]  
  
expected_output_for_dilation = [ { 255, 255, 100, 100},  
                                                    { 255, 255, 100, 100},  
                                                    { 100, 100, 100, 100}]  
```  
This gave me no errors,can u please point out what exactly is breaking

> Username: meshtag  
> Created_at: 2021-01-25 06:56:45 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r563496051  

I am trying to find a solution for the first problem

> Username: meshtag  
> Created_at: 2021-01-25 19:20:14 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r563981652  

@simmplecoder ,shall i use this ``` copy_pixels(src_view,dst_view);```  to solve the first problem?

> Username: meshtag  
> Created_at: 2021-01-27 17:48:59 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r565511278  

@simmplecoder , I have included this example in test file, please take a look at it and tell me if I need to change anything.

> Username: simmplecoder  
> Created_at: 2021-02-01 08:20:31 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r567633242  

Looks good, you can resolve this thread (if it is resolvable).


📁 example/morphology.cpp

```diff
  43 |+     {
  44 |+         for(int i=3;i<argc;++i)
  45 |+             ++operations[argv[i]];
```

> Username: simmplecoder  
> Created_at: 2021-01-24 17:16:19 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r563322051  

increment on `bool` is deprecated in C++17. Please just set to `true. Multiple same options will be just ignored.

---

📁 example/morphology.cpp

```diff
  25 |+     {
  26 |+         std::cout<<"Wrong format of command line arguments.\n";
  27 |+         std::cout<<"Correct format is <Input_image.png> <Output_image_template>"
```

> Username: simmplecoder  
> Created_at: 2021-01-24 17:37:59 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r563324765  

you can omit semicolon on previous line and `std::cout` here, so it will continue streaming.

> Username: mloskot  
> Created_at: 2021-01-24 19:42:36 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r563339690  

I'd suggest to `throw std::invalid_argument` instead of `std::cout`


---

## Comment 2

> Username: simmplecoder  
> Created_at: 2021-01-24 17:40:57 UTC  
> Updated_at: 2021-01-24 17:41:37 UTC  
> Url: https://github.com/boostorg/gil/pull/541#issuecomment-766400680  

Please format your files with `clang-format`, [link](https://github.com/boostorg/gil/issues/518#issue-687096504) to the most conforming config. Please do the reformat in its own commit, separate from other changes.

---

## Review 3 [Commented]

> Username: mloskot  
> Created_at: 2021-01-24 19:40:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/541#pullrequestreview-574962259  

📁 example/morphology.cpp

```diff
  17 |+ //                                    [1,1,1]
  18 |+ //SE(1,1)(center pixel) is the one which coincides with the currently considered pixel of the 
  19 |+ //image to be convolved. The structuring element can be easily changed by the user.
```

> Username: mloskot  
> Created_at: 2021-01-24 19:40:54 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r563339540  

@meshtag Please, use `//<space>Content` for non-Doxygen comments and `///<space>Content` for Doxygen comments


---

## Comment 4

> Username: mloskot  
> Created_at: 2021-01-25 20:58:31 UTC  
> Url: https://github.com/boostorg/gil/pull/541#issuecomment-767108202  

Nitpick: If the `example/original.png` corresponds to `example/morphology.hpp`, could you rename it to `example/morphology_original.png` please?

---

## Comment 5

> Username: mloskot  
> Created_at: 2021-01-25 23:37:29 UTC  
> Updated_at: 2021-01-25 23:43:19 UTC  
> Url: https://github.com/boostorg/gil/pull/541#issuecomment-767182122  

@meshtag I looked into comparing initial work on the morphological operations by @ayushbansal07 in PR #430 and your work, and I don't see a copying one to the other, but if you have used @ayushbansal07 's work in any way, then please credit him adding relevant copyright notice. I leave it to your judgement.  
  
One thing that Ayush does nicely is the Doxygen comments, so I'd suggest to look at those.  
  
------  
It would be good if you could update your PR based on the latest changes in the `develop` branch.  
Then, let us know when it is ready to continue with another review iteration(s).  
  
------  
  
You seem to be saving on whitespaces e.g. `//This` in code vs `// This` and `1.Dilation:Gives` vs `1. Dilation: Gives`  in this PR description, which I have taken the liberty to edit and fix.   
You may want to check the spacebar key of your keyboard, it may need fixing ;-)  
Seriously, if you could give a bit love to readable formatting of your commends and writing, it would save us on review iterations.

---

## Comment 6

> Username: mloskot  
> Created_at: 2021-01-26 16:28:00 UTC  
> Url: https://github.com/boostorg/gil/pull/541#issuecomment-767662094  

@meshtag If you update your PR from the latest `develop` you should get reliable CI builds as we've just replaced Travis CI with GitHub Actions

---

## Review 7 [Changes requested]

> Username: mloskot  
> Created_at: 2021-01-28 21:52:07 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/541#pullrequestreview-578765637  

I made a another round of my review which is mostly nitpicks about missing whitespaces.

📁 example/morphology.cpp

```diff
  11 |+ #include<iostream>
  12 |+ #include<map>
  13 |+ #include<string>
```

> Username: mloskot  
> Created_at: 2021-01-28 21:43:38 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r566426935  

Space missing before `<`

---

📁 example/morphology.cpp

```diff
   7 |+ //
   8 |+ #include <boost/gil/extension/io/png.hpp>
   9 |+ #include <boost/gil/image_processing/morphology.hpp>
```

> Username: mloskot  
> Created_at: 2021-01-28 21:44:18 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r566427314  

This is just an example, why not just?  
```  
#include <boost/gil.hpp>  
```

> Username: meshtag  
> Created_at: 2021-01-29 04:36:34 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r566574219  

When I tried doing that, my compiler started shouting at me about some identifiers being undefined, the program worked perfectly fine though . I will see if I can suppress those messages and then make required changes

> Username: mloskot  
> Created_at: 2021-01-29 08:42:07 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r566660613  

Every public header has to be added to `boost/gil.hpp`, so please add yours too.

---

📁 example/morphology.cpp

```diff
  20 |+ int main(int argc,char **argv)
  21 |+ {
  22 |+     std::map<std::string,bool>operations;
```

> Username: mloskot  
> Created_at: 2021-01-28 21:44:34 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r566427448  

Space missing `>o`


📁 include/boost/gil/image_processing/morphology.hpp

```diff
 194 |+ {
 195 |+     copy_pixels(src_view,int_op_view);
 196 |+     for(int i=0;i<iterations;++i)
```

> Username: mloskot  
> Created_at: 2021-01-28 21:45:28 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r566427907  

Whitespaces missing

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
 104 |+     typedef typename channel_convert_to_unsigned<d_channel_t>::type channel_t;
 105 |+     typedef pixel<channel_t, gray_layout_t> gray_pixel_t;
 106 |+     typedef image<gray_pixel_t, false> src_gray_image;
```

> Username: mloskot  
> Created_at: 2021-01-28 21:47:27 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r566429080  

We do C+11, so please prefer `using` declaration over `typedef` for aliases.

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  98 |+                                                             morphological_operations identifier)
  99 |+ {
 100 |+     using namespace boost::gil;
```

> Username: mloskot  
> Created_at: 2021-01-28 21:47:45 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r566429265  

What is this for?

> Username: meshtag  
> Created_at: 2021-01-29 04:46:01 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r566576545  

In an earlier version of this file, I was using ```gray8_image_t``` , I changed that but forgot to remove the namespace part,apologies .

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  13 |+ #include <boost/gil/image_processing/threshold.hpp>
  14 |+ 
  15 |+ namespace gil = boost::gil;
```

> Username: mloskot  
> Created_at: 2021-01-28 21:48:08 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r566429477  

Please, remove this line

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  20 |+                 dilation,
  21 |+                 erosion,
  22 |+             };
```

> Username: mloskot  
> Created_at: 2021-01-28 21:48:45 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r566429843  

Please, don't indent content of namespaces - we don't need empty column space on the left :)

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  40 |+ {
  41 |+     std::ptrdiff_t flip_ker_row, flip_ker_col, row_boundary, col_boundary;
  42 |+     typename channel_type<typename SrcView::value_type>::type target_element;
```

> Username: mloskot  
> Created_at: 2021-01-28 21:49:10 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r566430082  

If `target_element` is used inside the nested look, then narrow its scope to the loop

> Username: meshtag  
> Created_at: 2021-01-29 04:42:30 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r566575677  

```target_element``` is used for comparing all values of image view pixels which are overlapped with the structuring element in one single operation . Therefore, it must remain same during all such comparisons between overlapped values, this stops me from narrowing its scope .

> Username: mloskot  
> Created_at: 2021-01-29 08:43:36 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r566661400  

Right, I missed that.

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  64 |+                         // We ensure that we consider only those pixels which are overlapped on a
  65 |+                         // non-zero kernel_element as
  66 |+                         if(kernel.at(flip_ker_row,flip_ker_col))
```

> Username: mloskot  
> Created_at: 2021-01-28 21:50:46 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r566431000  

space after comma

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  66 |+                         if(kernel.at(flip_ker_row,flip_ker_col))
  67 |+                         {
  68 |+                             if(identifier == boost::gil::morphological_operations::dilation)
```

> Username: mloskot  
> Created_at: 2021-01-28 21:51:01 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r566431158  

`boost::gil::` needed?

> Username: meshtag  
> Created_at: 2021-01-29 04:43:46 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r566575948  

Will change it to ```morphological_operations::dilation```

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  96 |+ template <typename SrcView, typename DstView,typename Kernel>
  97 |+ void morph(SrcView const& src_view, DstView & dst_view,Kernel const& ker_mat, 
  98 |+                                                             morphological_operations identifier)
```

> Username: mloskot  
> Created_at: 2021-01-28 21:51:23 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r566431341  

Move the `morphological_operations` back to the left


📁 test/core/image_processing/morphology.cpp

```diff
  15 |+ // This function helps us fill pixels of a view given as 2nd argument with elements of the vector
  16 |+ // given as 1st argument.
  17 |+ void pixel_fill(std::vector<std::vector<int> > &original_binary_vector, boost::gil::gray8_image_t &original_img)
```

> Username: mloskot  
> Created_at: 2021-01-28 21:46:22 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r566428457  

- We do C++11, so `(std::vector<std::vector<int> >` should read `(std::vector<std::vector<int>>`  
- Attach `&` to the type, not the variable.


---

## Comment 8

> Username: meshtag  
> Created_at: 2021-01-29 04:31:13 UTC  
> Url: https://github.com/boostorg/gil/pull/541#issuecomment-769567703  

@mloskot ,apologies for making so many whitespace errors and thanks a lot for your patience and effort pointing them out.

---

## Comment 9

> Username: mloskot  
> Created_at: 2021-01-29 08:33:04 UTC  
> Updated_at: 2021-01-29 08:40:46 UTC  
> Url: https://github.com/boostorg/gil/pull/541#issuecomment-769659691  

> @mloskot ,apologies for making so many whitespace errors and thanks a lot for your patience and effort pointing them out.  
  
There is no need to apologies, we're all learning. Please, consider @simmplecoder advice in https://github.com/boostorg/gil/pull/541#issuecomment-766400680  
  
------  
  
I noticed `morphology.cpp` is missing from `example/Jamfile`, so it does not build together with all other examples, e.g.  
  
```  
cd libs/gil  
b2 cxxstd=11 variant=release example  
```  
  
or  
  
```  
b2 cxxstd=11 variant=release example//morphology  
```

---

## Review 10 [Commented]

> Username: mloskot  
> Created_at: 2021-01-29 08:50:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/541#pullrequestreview-579049020  

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  16 |+ namespace boost{
  17 |+     namespace gil{
  18 |+         enum class morphological_operations
```

> Username: mloskot  
> Created_at: 2021-01-29 08:50:54 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r566665645  

@meshtag One more thing, please rename `morphological_operations` to singular form i.e. `morphological_operation`


---

## Comment 11

> Username: meshtag  
> Created_at: 2021-01-29 11:52:45 UTC  
> Updated_at: 2021-01-29 11:54:30 UTC  
> Url: https://github.com/boostorg/gil/pull/541#issuecomment-769760582  

I tried using the .clang-format file available [here](https://github.com/boostorg/gil/issues/518) for formatting as suggested by @simmplecoder   . Unfortunately, it gave me this error   
```  
(base) prathamesh@prathamesh-GF63-Thin-9SCXR:~/gil/example$ clang-format -i morphology.cpp  
YAML:14:32: error: invalid boolean  
AllowShortBlocksOnASingleLine: Never  
                               ^~~  
Error reading /home/prathamesh/gil/.clang-format: Invalid argument  
```  
I have not used clang formatting before and hence can't figure out what to do in order to make it work.

---

## Comment 12

> Username: mloskot  
> Created_at: 2021-01-29 12:30:32 UTC  
> Url: https://github.com/boostorg/gil/pull/541#issuecomment-769777548  

Try `.clang-format` from here https://github.com/boostorg/gil/tree/develop/example/clang-format and use `clang-format` 8 or later, as specified in the README.

---

## Comment 13

> Username: meshtag  
> Created_at: 2021-01-29 13:47:45 UTC  
> Url: https://github.com/boostorg/gil/pull/541#issuecomment-769814797  

@mloskot ,this worked!

---

## Review 14 [Changes requested]

> Username: simmplecoder  
> Created_at: 2021-02-01 08:10:29 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/541#pullrequestreview-580089178  

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  69 |+                   target_element = src_view(col_boundary, row_boundary);
  70 |+               }
  71 |+             }
```

> Username: simmplecoder  
> Created_at: 2021-02-01 08:03:04 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r567624347  

Perhaps you could skip zero element right at the start of the current loop, with something like:  
  
```  
if (kernel.at(flip_ker_row, flip_ker_col) == 0) {  
    continue;  
}  
```

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  18 |+   dilation,
  19 |+   erosion,
  20 |+ };
```

> Username: simmplecoder  
> Created_at: 2021-02-01 08:05:26 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r567625412  

Is this used outside `detail` namespace? If not, please move inside that namespace.


---

## Comment 15

> Username: simmplecoder  
> Created_at: 2021-02-01 17:13:12 UTC  
> Url: https://github.com/boostorg/gil/pull/541#issuecomment-771012893  

@meshtag, I have run checks with opencv up to 3 iterations both for dilation and erosion. The images produced are identical. Good job.

---

## Review 16 [Changes requested]

> Username: simmplecoder  
> Created_at: 2021-02-01 17:19:04 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/541#pullrequestreview-580581158  

📁 include/boost/gil/image_processing/morphology.hpp

```diff
 109 |+   for (std::size_t i = 0; i < src_view.num_channels(); i++) {
 110 |+     morph_impl(nth_channel_view(src_view, i),
 111 |+                nth_channel_view(view(intermediate_img), i), ker_mat,
```

> Username: simmplecoder  
> Created_at: 2021-02-01 17:15:04 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r567996857  

Intermediate image is only one channel, but `i` might take values higher than 0. Please try to test it with multi-channel image.

> Username: meshtag  
> Created_at: 2021-02-02 09:14:38 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r568438581  

Yes you were right, it was creating problems with multi-channelled images, I changed the declaration of intermediate_img as   
```  
gil::image<typename DstView::value_type> intermediate_img(src_view.dimensions());  
```  
Now,it is working fine even with multi-channelled images.Thanks for pointing that out.

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
 240 |+                             Kernel const &ker_mat) {
 241 |+   using namespace boost::gil;
 242 |+   gray8_image_t int_dilate(src_view.dimensions()),
```

> Username: simmplecoder  
> Created_at: 2021-02-01 17:16:03 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r567997672  

Not all views will be gray image views, you can try `gil::image<typename DstView::value_type>`. Same thing with hats.

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  62 |+             // on a non-zero kernel_element as
  63 |+             if(kernel.at(flip_ker_row,flip_ker_col) == 0){
  64 |+               continue;
```

> Username: simmplecoder  
> Created_at: 2021-02-01 17:17:46 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r567998963  

When mentioning this improvement I meant placing it on line 53 from current commit.

> Username: meshtag  
> Created_at: 2021-02-02 05:56:04 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r568340454  

Okay,I will change it


---

## Comment 17

> Username: simmplecoder  
> Created_at: 2021-02-01 17:22:50 UTC  
> Url: https://github.com/boostorg/gil/pull/541#issuecomment-771019076  

It seems like formatting got worse after applying the clang-format. Lets leave this issue to the very end, when everything else will be ready.

---

## Review 18 [Changes requested]

> Username: simmplecoder  
> Created_at: 2021-02-07 19:17:48 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/541#pullrequestreview-585058767  

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  67 |+             if (identifier == morphological_operation::dilation) {
  68 |+               if (src_view(col_boundary, row_boundary) > target_element)
  69 |+                 target_element = src_view(col_boundary, row_boundary);
```

> Username: simmplecoder  
> Created_at: 2021-02-07 19:15:12 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r571673178  

Can we use std::max here? Lets try this:  
  
```  
using std::max;  
target_element[0] = max(target_element[0], src_view(col_boundary, row_boundary)[0]);  
```

> Username: meshtag  
> Created_at: 2021-02-08 04:49:11 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r571778924  

I can use std::max, but then I will have to typecast first and then compare, like this  
```  
target_element = std::max((int)src_view(col_boundary, row_boundary), (int)target_element);  
```  
I am not sure what you meant by using an indexing operator in   
```  
target_element[0] = max(target_element[0], src_view(col_boundary, row_boundary)[0]);  
```  
As per my understanding, neither target_element nor src_view(col_boundary, row_boundary) is an array, please correct me if I am wrong.

> Username: meshtag  
> Created_at: 2021-02-08 06:17:14 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r571802585  

@simmplecoder ,I have formatted my files with your version of ```.clang-format``` . Please review those changes and tell me which version would you like me to use for the above point. I have simply commented my earlier approach, I will replace that with the final version in my next commit .

> Username: meshtag  
> Created_at: 2021-02-08 06:20:37 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r571803737  

Also, I will handle the merge conflict in my next commit .

> Username: simmplecoder  
> Created_at: 2021-02-08 15:54:18 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r572158322  

It doesn't have to be array. Homogenous pixels (where all channels are of the same type) have overloaded `operator[]` so you can access channels by index. Casting everything to `int` is wrong (examples: `gil::gray32f_image_t`, `gil::gray32_image_t`).

> Username: mloskot  
> Created_at: 2021-02-09 13:23:36 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r572879240  

Also, I'd suggest to use `(std::max)(a, b)` in case the `std::max(a, b)` fails with the annoying compilation errors from MSVC

> Username: meshtag  
> Created_at: 2021-02-09 17:47:39 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r573097249  

Okay I will do that. I found a good reference on this [here](https://stackoverflow.com/questions/1632145/use-of-min-and-max-functions-in-c/2125302#2125302) . Thanks @mloskot for this great find :)

> Username: meshtag  
> Created_at: 2021-02-09 20:06:12 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r573200120  

@mloskot , I found [this](https://dustri.org/b/min-and-max-macro-considered-harmful.html) blog and hence I would like to confirm this, which version would be more safe/suitable for our cause   
```  
template<typename T>  
inline T MAX(T a, T b) { return((a) > (b) ? a : b); }  
```  
or   
```  
#define MAX(a,b) ((a) > (b) ? a : b)  
```

> Username: mloskot  
> Created_at: 2021-02-09 21:40:39 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r573261650  

@meshtag  TBH, none of the two `MAX`-es above. Any reason why it can not be just the *existing* C++ *function* `std::max`?

> Username: meshtag  
> Created_at: 2021-02-10 05:44:52 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r573463802  

@mloskot , no specific reason in particular. Perhaps I misinterpreted your earlier comment. I thought you wanted me to disable ADL(Argument dependent lookup) here by using ```(std::max)(a, b)```. Since that doesn't seems to be the case now, I will keep it as it is.

> Username: mloskot  
> Created_at: 2021-02-10 15:43:19 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r573838440  

@meshtag Yes, I suggested that in order to avoid the potential of clashing with the `max` *macro* when compiling using the MSVC, but I did not suggest introducing new `MAX` macro or function, just to stick `(std::max)(...)` instead of `std::max(...)`.

> Username: meshtag  
> Created_at: 2021-02-10 18:41:03 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r573980135  

okay, pushed the code with changes.

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
  70 |+             } else if (identifier == morphological_operation::erosion) {
  71 |+               if (src_view(col_boundary, row_boundary) < target_element)
  72 |+                 target_element = src_view(col_boundary, row_boundary);
```

> Username: simmplecoder  
> Created_at: 2021-02-07 19:15:34 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r571673214  

Same as for the other branch

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
 154 |+ 
 155 |+ // IntOpView : View utilized for performing intermediate operations which will
 156 |+ // contribute in creating the resultant view .
```

> Username: simmplecoder  
> Created_at: 2021-02-07 19:15:55 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r571673311  

Leftover?

> Username: meshtag  
> Created_at: 2021-02-08 04:52:02 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r571779549  

I thought that it would be good to express what I had in my mind while naming ```IntOpView``` to the reviewers, it was not leftover.

> Username: simmplecoder  
> Created_at: 2021-02-08 16:00:44 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r572163384  

Not sure I'm following. Perhaps it would be better to add it in the documentation of the functions then? You could also name it like `InterimOpView` which is a bit more obvious. Something along the lines of:  
  
```  
\param int_op_view - view for writing output and performing intermediate operations  
\tparam IntOpView type of output image, models gil::MutableImageViewConcept  
  
```

> Username: meshtag  
> Created_at: 2021-02-08 18:19:58 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r572267233  

For now, I have provided parameter definitions in the same way as you suggested. Do tell me if I need to tweak parameter name too .

---

📁 include/boost/gil/image_processing/morphology.hpp

```diff
 278 |+ }
 279 |+ } // namespace gil
 280 |+ /// @}
```

> Username: simmplecoder  
> Created_at: 2021-02-07 19:17:08 UTC  
> Updated_at: 2021-02-13 04:40:04 UTC  
> Url: https://github.com/boostorg/gil/pull/541#discussion_r571673443  

Could you please move this inside `gil`?


---

## Comment 19

> Username: simmplecoder  
> Created_at: 2021-02-07 19:22:15 UTC  
> Url: https://github.com/boostorg/gil/pull/541#issuecomment-774741754  

Alright, this is probably last round of reviews. Please consider changes I suggested. To reformat files using my `.clang-format` you can either download new version (mine is >=10 I believe) or build it from source or I will just send you a patch tomorrow if changes will be ready. Also please address conflicts with recent `develop` changes. If I forget to send you patches, please ping me, I do not want to stall you any longer.

---

## Comment 20

> Username: simmplecoder  
> Created_at: 2021-02-08 16:02:25 UTC  
> Url: https://github.com/boostorg/gil/pull/541#issuecomment-775252004  

Formatting still is not correct @meshtag . I do not think it is worth dealing with it on your own. I can just send you a patch. The easy way to understand that the formatter is not getting it right is by looking at space count per indent. It is 2 at the moment, should be 4.

---

## Comment 21

> Username: mloskot  
> Created_at: 2021-02-09 13:28:00 UTC  
> Url: https://github.com/boostorg/gil/pull/541#issuecomment-775934799  

Yes, @meshtag, although this may seem like an trivial annoyance, we want to fix any formatting issues before merge - the legacy code of GIL is already messy and we are slowly cleaning it up, so must keep new code as consistent as possible.

---

## Comment 22

> Username: meshtag  
> Created_at: 2021-02-09 17:11:00 UTC  
> Url: https://github.com/boostorg/gil/pull/541#issuecomment-776094897  

No problem @mloskot , I am having trouble figuring out why appveyor build is failing . Do I need to change/correct something?

---

## Comment 23

> Username: mloskot  
> Created_at: 2021-02-09 22:39:57 UTC  
> Updated_at: 2021-02-09 22:48:07 UTC  
> Url: https://github.com/boostorg/gil/pull/541#issuecomment-776292567  

@meshtag I think the key message of the log is `Failed a day ago in 1 hr 10 min` what suggests that we are hitting the [AppVeyor limits](https://www.appveyor.com/pricing/):  
  
> Are there any build time restrictions?  
> All plans have maximum build job execution time of 60 minutes.  
  
So, you don't need to do anything.  
  
Rather, I will have to think which AppVeyor job to kick out :)  
  
UPDATE: Done in 2676d31801c1232c2824c93e617f5c49a638e594

---

## Review 24 [Changes requested]

> Username: mloskot  
> Created_at: 2021-02-09 22:49:16 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/541#pullrequestreview-587090180  

@meshtag Could you hit the "Resolve conversation" button for each of the thread that you have addressed its comments? This way we will have a clearer picture of what has been done and what not. Thanks.  
  
Second, if you update this PR with the latest `develop` branch it will receive the AppVeyor fix.

---

## Comment 25

> Username: simmplecoder  
> Created_at: 2021-02-13 18:45:25 UTC  
> Url: https://github.com/boostorg/gil/pull/541#issuecomment-778660687  

[compare-results.zip](https://github.com/boostorg/gil/files/5976260/compare-results.zip)  
[python-morphops.zip](https://github.com/boostorg/gil/files/5976261/python-morphops.zip)  
[meshtag-morphops.zip](https://github.com/boostorg/gil/files/5976262/meshtag-morphops.zip)  
Here are comparison results with OpenCV

---

## Comment 26

> Username: mloskot  
> Created_at: 2021-02-16 10:10:22 UTC  
> Url: https://github.com/boostorg/gil/pull/541#issuecomment-779730885  

Thanks for the comparison. The results are looking good, I think.  
Can you tell how did you generate the files in the `compare-results.zip` ?

---

## Comment 27

> Username: simmplecoder  
> Created_at: 2021-02-17 17:47:08 UTC  
> Url: https://github.com/boostorg/gil/pull/541#issuecomment-780731638  

The code I used to get the results from opencv is   
  
```  
import cv2  
import numpy as np  
import sys  
  
  
def morphops(input_file: str, output_name: str):  
    input_img = cv2.imread(input_file, cv2.IMREAD_GRAYSCALE)  
    kernel = np.full((3, 3), 1, np.uint8)  
    dilated = cv2.dilate(input_img, kernel, iterations=3)  
    eroded = cv2.erode(input_img, kernel, iterations=3)  
    cv2.imwrite(output_name + '_dilation.png', dilated)  
    cv2.imwrite(output_name + '_erosion.png', eroded)  
  
  
if __name__ == '__main__':  
    morphops(sys.argv[1], sys.argv[2])  
```

---
