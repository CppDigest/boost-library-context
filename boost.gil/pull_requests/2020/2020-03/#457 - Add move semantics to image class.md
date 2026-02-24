# #457 Add move semantics to image class [Merged]

> Username: sdebionne  
> Created at: 2020-03-19 14:00:37 UTC  
> Updated at: 2020-04-07 08:55:55 UTC  
> Merged at: 2020-04-06 21:50:03 UTC  
> Closed at: 2020-04-06 21:50:03 UTC  
> Url: https://github.com/boostorg/gil/pull/457  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Add move semantic to `image`.  
  
### References  
  
- Fix part of #438  
- [[gil] Review of move assignment operator for image class](https://lists.boost.org/Archives/boost/2020/03/248582.php)  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [ ] Update doc(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Commented]

> Username: mocabe  
> Created_at: 2020-03-19 14:30:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/457#pullrequestreview-377775031  

📁 include/boost/gil/image.hpp

```diff
 103 |+     
 104 |+     image(image&& img) noexcept {
 105 |+         _view = img._view;
```

> Username: mocabe  
> Created_at: 2020-03-19 14:30:50 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r395069369  

These are copy/move assignments, not constructions.

> Username: sdebionne  
> Created_at: 2020-03-19 14:41:55 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r395078162  

Thanks for the review, would this be better:  
  
```c++  
   image(image&& img) :  
       _view(img._view),  
       ...  
```

> Username: sdebionne  
> Created_at: 2020-03-19 17:38:21 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r395205025  

I made the suggested modifications.


---

## Review 2 [Changes requested]

> Username: mloskot  
> Created_at: 2020-03-26 23:57:06 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/457#pullrequestreview-382466972  

@sdebionne Thanks for the contribution. Here is my first review.

📁 test/core/image/image.cpp

```diff
  56 |+         BOOST_CHECK_EQUAL(image.dimensions(), gil::point_t{});
  57 |+     }
  58 |+ }
```

> Username: mloskot  
> Created_at: 2020-03-26 22:32:57 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r398932124  

@sdebionne We have moved away from Boost.Test to Boost.LT, so I refactored your tests using the `<boost/lightweight_test.hpp>`. Here is my temporary branch https://github.com/mloskot/gil/commit/31d948c9842bbe458d836c40db7cf5e459d92921 with the complete `image.cpp` file  
  
https://github.com/mloskot/gil/blob/31d948c9842bbe458d836c40db7cf5e459d92921/test/core/image/image.cpp

> Username: sdebionne  
> Created_at: 2020-03-30 13:54:23 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r400210013  

OK, sorry I did not know about the change to Boost.LIT, I'll cherry pick you commit.

> Username: mloskot  
> Created_at: 2020-03-30 13:57:42 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r400212593  

@sdebionne It's me being sorry as troublemaker. I had been dancing back and forth for a while until I decided which to marry :)

---

📁 test/core/image/image.cpp

```diff
  52 |+     {
  53 |+         Image image = fixture::create_image<Image>(dimensions.x, dimensions.y, 0);
  54 |+         Image image2 = std::move(image);
```

> Username: mloskot  
> Created_at: 2020-03-26 23:31:08 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r398952416  

This does not kick the move assignment.  
I'd also generate a distinct `image2` that will be deallocated and cleared as prep to be come a moved-to object:  
  
```cpp  
image_t image2 = fixture::create_image<image_t>(dimensions.x * 2, dimensions.y * 2, 1);  
image2 = std::move(image);  
```

> Username: sdebionne  
> Created_at: 2020-03-30 13:57:46 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r400212635  

My bad, this is indeed a construction. I'll fix this. I'm not sure what is the right way to test move semantic...

> Username: mloskot  
> Created_at: 2020-03-30 14:08:05 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r400220324  

> I'm not sure what is the right way to test move semantic  
  
I'm not 100% either, but looks like testing states before/after of moved-from/to is an obvious way that should give at least basic indication things work.


📁 include/boost/gil/image.hpp

```diff
 163 |+         if (_alloc == img._alloc) {
 164 |+             move_assign(img, equal_allocators{});
 165 |+         } else {
```

> Username: mloskot  
> Created_at: 2020-03-26 22:36:02 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r398933319  

The formatting of the `image.hpp` is still a total mess, but for future, please stick `{` and `}` in separate lines.

> Username: sdebionne  
> Created_at: 2020-03-30 13:55:16 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r400210664  

l like `{` and `}` in separate lines better as well.

> Username: mloskot  
> Created_at: 2020-03-30 14:02:32 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r400216313  

@sdebionne I just added `.clang-format` example here, https://github.com/boostorg/gil/tree/master/example/clang-format. As I describe in the README, it is not a fully-featured/complete config, but I've been finding it very useful when working with GIL.  
  
It works for most cases, manual intervention may still be required for complex templates or trailing return or when it orders alphabetically all headers but we prefer different order in groups https://github.com/boostorg/gil/wiki/Guidelines. Give it a go if you like.

---

📁 include/boost/gil/image.hpp

```diff
 107 |+       _memory(img._memory),
 108 |+       _align_in_bytes(img._align_in_bytes),
 109 |+       _alloc(std::move(img._alloc)),
```

> Username: mloskot  
> Created_at: 2020-03-26 23:03:55 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r398943336  

Quoting [Arthur O'Dwyer](https://www.packtpub.com/gb/application-development/mastering-c17-stl) here, since   
  
> [allocator is just a thin "handle"](https://www.youtube.com/watch?v=0MdSJsCTRkY) (...) Copying an allocator type should always be relatively cheap. Still, using std::move here wouldn't have hurt.  
  
So, let's assume it doesn't.

---

📁 include/boost/gil/image.hpp

```diff
 112 |+         img._memory = nullptr;
 113 |+         img._align_in_bytes = 0;
 114 |+         img._allocated_bytes = 0;
```

> Username: mloskot  
> Created_at: 2020-03-26 23:04:51 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r398943632  

I wish we had `std::exchange` in C++11 :)

> Username: sdebionne  
> Created_at: 2020-03-30 13:56:25 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r400211554  

Any chance that there is a substitute in Boost.Core somewhere?

> Username: mloskot  
> Created_at: 2020-03-30 14:06:44 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r400219265  

Ha, a brilliantly obvious question: https://github.com/boostorg/core/blob/develop/include/boost/core/exchange.hpp

---

📁 include/boost/gil/image.hpp

```diff
 158 |+         img._align_in_bytes = 0;
 159 |+         img._allocated_bytes = 0;
 160 |+     }
```

> Username: mloskot  
> Created_at: 2020-03-26 23:54:53 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r398959972  

I have to admit I still have to catch up with the hairy topic of the allocators and related container behaviours in post the C++03 era, but what I am missing in the whole move-assignment is this :  
  
- use of `propagate_on_container_move_assignment` trait  
- `// fallback to copy nothing` see below  
  
This below is complete illustration what I think we need, at least according to my current mis-or-understanding of this matter:  
  
```cpp  
image& operator=(image&& rhs)  
{  
    auto const exchange_memory = [](image& lhs, image& rhs)  
    {  
        lhs._memory = std::exchange(rhs._memory, nullptr);  
        lhs._align_in_bytes = std::exchange(rhs._align_in_bytes, 0);  
        lhs._allocated_bytes = std::exchange(rhs._allocated_bytes, 0);  
        lhs._view = std::exchange(rhs._view, image::view_t{});  
    };  
  
    constexpr bool pocma = std::allocator_traits<Alloc>::propagate_on_container_move_assignment::value;  
    if (pocma)  
    {  
        // non-sticky allocator, can adopt the memory, fast  
        destruct_pixels(this->_view);  
        this->deallocate();  
        this->_alloc = rhs._alloc;  
        exchange_memory(*this, rhs);  
    }  
    else if (_alloc == rhs._alloc)  
    {  
        // allocator stuck to the rhs, but it's equivalent of ours, we can still adopt the memory  
        destruct_pixels(_view);  
        this->deallocate();  
        exchange_memory(*this, rhs);  
    }  
    else  
    {  
        // cannot propagate the allocator and cannot adopt the memory  
        if (rhs._memory)  
        {  
            allocate_and_copy(rhs.dimensions(), rhs._view);  
            destruct_pixels(rhs._view);  
            rhs.deallocate();  
            rhs._view = image::view_t{};  
        }  
        else  
        {  
            destruct_pixels(this->_view);  
            this->deallocate();  
            this->_view = view_t{};  
        }  
    }  
    return *this;  
}  
```  
  
What do you think @sdebionne & @mocabe ?

> Username: sdebionne  
> Created_at: 2020-03-30 14:43:02 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r400247726  

I am not really qualified I am afraid! So I basically checked how standard libraries (the one from MS) implement move semantic for `std::vector`:  
  
```c++  
struct equal_allocators {};  
using propagate_allocators = std::true_type;  
using no_propagate_allocators = std::false_type;  
  
template <class Alloc>  
using pocma  = conditional_t<  
  std:allocator_traits<Alloc>::is_always_equal::value,  
  equal_allocators,  
  typename std::allocator_traits<Alloc>::propagate_on_container_move_assignment::type  
>;  
```  
  
We don't have `is_always_equal` in C++11 but could replace it with `is_empty`. If we use `propagate_on_container_move_assignment` only, then your implementation looks good to me.  
  
Your if statement is much better than my template specialization.

> Username: mloskot  
> Created_at: 2020-03-30 17:37:17 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r400372645  

It is a good idea to check what implementors do.  
I also see MSVC++ STL does this:  
  
```cpp  
template <class _Alloc>  
void _Pocma(_Alloc& _Left, _Alloc& _Right) noexcept {  
    if constexpr (allocator_traits<_Alloc>::propagate_on_container_move_assignment::value) {  
        _Left = _STD move(_Right);  
    } else {  
        (void) _Left;  
        (void) _Right;  
    }  
}  
```  
  
which is equivalent to this if-else distinguishing between these two in my version above:  
  
```cpp  
if (pocma)  
{  
    this->_alloc = rhs._alloc; // we could std::move(rhs._alloc) here  
    exchange_memory(*this, rhs);  
}  
else if (_alloc == rhs._alloc)  
{  
    // no-op for rhs._alloc  
    this->deallocate();  
    exchange_memory(*this, rhs);  
}  
```  
  
> Your if statement is much better than my template specialization.  
  
I just find the locality of all the aspects make it readable.  
  
I think the metafunctions in the MSVC++ STL are good idea as soon as we need to re-use some code in number of places, as STL does for various containers.  
  
There is one place they will be useful, for handling of `noexcept` which in STL is conditional this way:  
  
```cpp  
noexcept(noexcept(_Move_assign(_Right, _Choose_pocma<_Alty>{})))  
```

> Username: mocabe  
> Created_at: 2020-03-31 00:08:02 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r400567103  

> which is equivalent to this if-else distinguishing between these two in my version above    
  
I looks like STL's implementation actually does not call `operator=` on allocator object when it cannot be propagated on move assignment, which allows allocators to define `operator=` as deleted. `std::pmr::polymorphic_allocator` is one of example of that.

> Username: mloskot  
> Created_at: 2020-03-31 00:10:46 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r400567954  

@mocabe Yes, indeed, that's also what we do in the snippet above: in `// allocator stuck to the rhs,` and the following `else`-cases. IOW, I consider STL's and the unrolled `if-else` versions equivalent.

> Username: mocabe  
> Created_at: 2020-03-31 00:43:10 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r400577134  

@mloskot I mean, `if-else` version requires definition of `operator=` of allocator type at compile time. STL uses `if constexpr` so it will discard instantiation of `_Alloc::operator=` when the statement is not active.

> Username: mloskot  
> Created_at: 2020-03-31 00:45:42 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r400577899  

@mocabe Right. I've got too much into the C++11 mode. This is worth to consider now as future-proof indeed.  
  
Since we are stuck with C++11, we can't go for e.g. `hana::eval_if`, but can do the tag dispatching only.   
  
@sdebionne If you don't have time or don't fancy, don't worry, I will try to go back to your variant, tweaked, later this week.


---

## Comment 3

> Username: sdebionne  
> Created_at: 2020-03-30 15:45:50 UTC  
> Url: https://github.com/boostorg/gil/pull/457#issuecomment-606079926  

@mloskot I rebased on develop and made the changes we discussed. If the tests pass, I'll mark it ready for review.

---

## Comment 4

> Username: mloskot  
> Created_at: 2020-03-30 18:54:59 UTC  
> Url: https://github.com/boostorg/gil/pull/457#issuecomment-606179145  

@sdebionne I also asked for feedback on the Boost list in [[gil] Review of move assignment operator for image class](https://lists.boost.org/Archives/boost/2020/03/248582.php)

---

## Comment 5

> Username: mloskot  
> Created_at: 2020-04-01 14:56:09 UTC  
> Updated_at: 2020-04-01 14:56:18 UTC  
> Url: https://github.com/boostorg/gil/pull/457#issuecomment-607298631  

Found a good comment related to solutions we are discussing here:  
  
> If your allocator type is stateless and/or sets `is_always_equal`,  
> then the settings of POCCA/POCMA/POCS don’t really matter  
> and might just as well be inconsistent.  
> For historical reasons, `std::allocator` falls into that category.  
  
from https://quuxplusone.github.io/blog/2019/08/02/the-tough-guide-to-cpp-acronyms/#pocca-pocma-pocs

---

## Comment 6

> Username: sdebionne  
> Created_at: 2020-04-02 15:37:08 UTC  
> Url: https://github.com/boostorg/gil/pull/457#issuecomment-607921363  

It explains why MSVC's STL has this special case for `std::allocator_traits::is_always_equal` and treat it as a pocma. Since we don't have it in C++11, is it OK to approximate with `std::is_empty`?  
  
I'll give it a go by the end of the week.

---

## Comment 7

> Username: mloskot  
> Created_at: 2020-04-02 20:14:21 UTC  
> Url: https://github.com/boostorg/gil/pull/457#issuecomment-608078775  

>  is it OK to approximate with std::is_empty?  
  
I think, it is and http://eel.is/c++draft/allocator.traits#types-10 says  
  
> 10   Type: `Alloc​::​is_­always_­equal` if the qualified-id `Alloc​::​is_­always_­equal` is valid and  
> denotes a type ([temp.deduct]); otherwise `is_­empty<Alloc>​::​type`.  
  
Additionally, MSVC++ std lib has this base case template  
  
```cpp  
template <class _Ty, class = void>  
struct _Get_is_always_equal {  
    using type = typename is_empty<_Ty>::type;  
};  
```

---

## Comment 8

> Username: sdebionne  
> Created_at: 2020-04-05 08:39:31 UTC  
> Url: https://github.com/boostorg/gil/pull/457#issuecomment-609380218  

@mloskot Are you aware that "the hosted agent vs2015-win2012r2 was permanently removed on April 2nd, 2020.  See https://aka.ms/blocked-hosted-agent for more information"  (from azure pipelines)?

---

## Comment 9

> Username: mloskot  
> Created_at: 2020-04-05 17:06:57 UTC  
> Updated_at: 2020-04-05 22:57:10 UTC  
> Url: https://github.com/boostorg/gil/pull/457#issuecomment-609448877  

@sdebionne   
> Are you aware that "the hosted agent vs2015-win2012r2 was permanently removed on April 2nd, 2020  
  
Yes, I am. @sdebionne  I have removed the VS2015 job now https://github.com/boostorg/gil/commit/5d531ed6c90cb3e1e6ec039c446cb6670b211029   
  
  
BTW, I am going to use this as opportunity to switch to https://github.com/boostorg/boost-ci/ setup, but have been waiting for merge of some major refactoring of boost-ci. I will try to switch GIL AzP setup to boost-ci today/tomorrow.

---

## Review 10 [Commented]

> Username: mloskot  
> Created_at: 2020-04-05 23:19:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/457#pullrequestreview-387873048  

📁 include/boost/gil/image.hpp

```diff
 208 |+           if (this != std::addressof(img))
 209 |+               // Use rebinded alloc to choose pocma
 210 |+               move_assign(img, choose_pocma<allocator_type>{});
```

> Username: mloskot  
> Created_at: 2020-04-05 23:19:34 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r403771547  

What does the rebound mean here?

> Username: sdebionne  
> Created_at: 2020-04-06 07:35:44 UTC  
> Updated_at: 2020-04-06 07:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r403884634  

It's related to:  
```c++  
class image {  
public:  
#if defined(BOOST_NO_CXX11_ALLOCATOR)  
    using allocator_type = typename Alloc::template rebind<unsigned char>::other;  
#else  
    using allocator_type = typename std::allocator_traits<Alloc>::template rebind_alloc<unsigned char>;  
#endif  
```  
I guess allocators could behave differently for different types (i.e. `unsigned char`), so we need to take into the rebound...

> Username: mloskot  
> Created_at: 2020-04-06 13:13:26 UTC  
> Url: https://github.com/boostorg/gil/pull/457#discussion_r404079506  

Sorry, I got confused reading `allocator_type` as `Alloc`.  
  
So, it is to use the rebound allocator type to control pocma selection, instead of the `Alloc` .


---

## Review 11 [Approved]

> Username: mloskot  
> Created_at: 2020-04-05 23:22:54 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/457#pullrequestreview-387873406  

@sdebionne  Looks good to me and ready to merge. Thanks!  
  
Do you want to update this PR to get rid of the VS2015 job?

---

## Review 12 [Approved]

> Username: mloskot  
> Created_at: 2020-04-06 21:45:52 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/457#pullrequestreview-388640957  

LGTM  
  
Thanks for the contribution!

---
