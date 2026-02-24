# #76 - irange makes non-emtpy ranges when first is greater than last [Open]

> Username: wrenoud  
> Created at: 2018-10-04 19:25:08 UTC  
> Updated at: 2018-10-04 21:47:26 UTC  
> Url: https://github.com/boostorg/range/issues/76  

the simple example `irange(-5)` I would expect to be an empty range, i.e.  
```cpp  
int32_t end   = -5;  
int64_t count = 0;  
for (int32_t i = 0; i < end; ++i)  
    // this line will never be reached  
    ++count;  
std::cout << count << std::endl; // 0  
  
count = 0;  
for (auto i : boost::irange(end))  
    // this should never be reached, but it will   
    // iterate past int32_t max until it reaches -5  
    ++count;  
std::cout << count << std::endl; // 4294967291  
```  
  
My expectations is the following tests should pass with no iterations, but many fail on asserts in the test but will be large ranges in release  
```cpp  
test_irange(-5);  
test_irange(0, -5); // this fails on assert at irange.hpp#L210  
test_irange(5, 10, -1); // this fails on assert at irange.hpp#L219  
test_irange(10, 5, 1); // this fails on assert at irange.hpp#L219  
```

---

## Comment 1

> Username: neilgroves  
> Created at: 2018-10-04 21:47:26 UTC  
> Url: https://github.com/boostorg/range/issues/76#issuecomment-427180732  

On Thu, 4 Oct 2018 at 20:25, Weston Renoud <notifications@github.com> wrote:  
  
> the simple example irange(-5) I would expect to be an empty range, i.e.  
>  
This case could have better documentation. The single argument version of  
irange was added relatively recently and is intended to be a range starting  
at 0 until the number in the parameter, and thus the value must be >= 0 for  
the range to be valid.  
  
It is not typical that ranges and their iterators are written defensively  
so that one can call ++ on an iterator at an invalid start point. It  
doesn't become a no-op. The irange is a range of iterators and the  
behaviour is consistent with iterators throughout the STL and the  
Boost.Range library. The pre-conditions should be better documented, but  
the implementation is sound. It is a choice to not have defensive code to  
turn invalid ranges into empty ranges. For some cases turning the invalid  
ranges into empty ranges would be more likely to pass tests, but result in  
broken behaviour. The code should have additional pre-condition assertions  
added for all of the irange functions to test that last is reachable from  
first with the implicit step, or with the explicit step.  
  
> int32_t limit = -5;int64_t max   = 0;for (int32_t i = 0; i < limit; ++i)  
>     // this line will never be reached  
>     ++max;  
> std::cout << max << std::endl; // 0  
>  
> max = 0;for (auto i : boost::irange(limit))  
>     // this should never be reached, but it will  
>     // iterate past int32_t max until it reaches -5  
>     ++max;  
> std::cout << max << std::endl; // 4294967291  
>  
>  
I assume you meant to have braces to avoid the comments altering the  
semantics. Your two for loops are not equivalent since the single-argument  
version implicitly starts at 0, and implicitly has a step of 1.  
boost::irange(0, limit) would be equivalent.  
  
> My expectations is the following tests should pass with no iterations, but  
> many fail on asserts in the test but will be large ranges in release  
>  
> test_irange(-5);test_irange(0, -5); // this fails on assert at irange.hpp#L210test_irange(5, 10, -1); // this fails on assert at irange.hpp#L219test_irange(10, 5, 1); // this fails on assert at irange.hpp#L219  
>  
> All of these should fail. They must fail. In each of these cases last is  
not reachable from first with the step chosen or implied. This is by  
design. It is trivial to add a defensive wrapper. It is impossible to take  
the defensiveness away. I am pleased most of these cases fail with an  
assertion. They should all fail with an assertion and will be modified to  
do so.  
  
Regards,  
Neil Groves  
  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/range/issues/76>, or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AGaE6q5DJTa8z_4XBsqXwwXyATrjTc_lks5uhmCVgaJpZM4XIwTZ>  
> .  
>
