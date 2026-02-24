# #66 Feature/debug macro [Merged]

> Username: mkaravel  
> Created at: 2014-06-19 09:42:22 UTC  
> Updated at: 2014-06-26 23:49:27 UTC  
> Merged at: 2014-06-22 13:29:16 UTC  
> Closed at: 2014-06-22 13:29:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/66  

Prefix macro GEOMETRY_TEST_DEBUG with BOOST_

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-06-19 19:04:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/66#issuecomment-46602986  

I have a general remark or rather a question. How this kind of debugging code be implemented and used? Consider debug_linear.hpp and follow_linear_linear.hpp.  
This PR moves the responsibility for conditional enabling of the debugging code to the debug_linear and the algorithm itself always calls the debugging function which is empty if the debugging is disabled.  
AFAIS in other parts of the library the responsibility for conditional enabling of the debugging code is on the algorithm's side.  
  
To be honest I don't know which one I prefer more. On one hand it's nice to see in the algorithm that some parts are only for debugging. However without ifdefs the algorithm is probably more readable and the code is probably more maintainable, also the debugging code, e.g. required headers.  
  
What do you think?

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-06-19 19:22:33 UTC  
> Updated_at: 2014-06-19 19:23:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/66#issuecomment-46604990  

@awulkiew Take a look at https://github.com/boostorg/geometry/blob/master/include/boost/geometry/algorithms/detail/overlay/follow.hpp, lines 459, 465, 471, 481. I am following the same paradigm (in the PR, not the original code).  
  
My personal preference is what I implemented in the PR: I would like to keep the code of the algorithm as clean as possible from macros. I want to collect all debug-related macro usage inside the files that are for debugging. Just like you mentioned, I feel that this makes the code more readable and IMHO much more maintainable.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2014-06-19 19:25:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/66#issuecomment-46605326  

@awulkiew FYI, in the other PR (feature/is_simple) I have followed the same paradigm as above (except for detail/is_valid/polygon.hpp, which is not finished in that respect yet; will do it most probably tonight).

---

## Comment 4

> Username: mloskot  
> Created_at: 2014-06-19 19:38:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/66#issuecomment-46606744  

It looks like Qt's `qDebug` facility controlled with `QT_NO_DEBUG_OUTPUT` preprocessor constant: if defined, then qDebug is no-op and (hopefully) optimised out by compiler as empty function. Something like here:  
  
```  
#ifdef MY_DEBUG  
    class null_stream {};  
    typedef null_stream debug_stream;  
    // a good compiler should optimise this to nothing  
    template <typename T>  
    null_stream& operator<<(null_stream&os, T const&) { return os; }  
#else  
    typedef std::ostream debug_stream;  
#endif  
```  
  
Was that the idea?

---

## Comment 5

> Username: mkaravel  
> Created_at: 2014-06-19 20:19:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/66#issuecomment-46611396  

@mloskot I think there are several aspects to be considered here:  
- code style  
- code readability  
- code maintenance  
- efficiency  
  and maybe more.  
  
My understanding is that Adam was commenting with respect to the first three, and you refer to the fourth item.  
From the efficiency point of view, yes, this is the idea. So having a function call in the algorithm code is expected to be optimized out when the function is empty.

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2014-06-19 20:52:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/66#issuecomment-46615056  

See also my recent change resulting in:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/buffer/buffer_inserter.hpp  
  
Search for lines visit_pieces_default_policy  
  
You will see that a specific visitor (a template function) is passed to the whole function. This visitor can do anything:  
- print lines  
- create a specific SVG with a debug-map  
  
In some cases the last is essential.  
  
See also the previous code which did have same the debug-map but then with if-defs, it was much more cluttered (especially in this case - but it was in development phase)  
  
https://github.com/boostorg/geometry/blob/rescale_to_integer/include/boost/geometry/extensions/algorithms/buffer/buffered_piece_collection_with_mapper.hpp  
https://github.com/boostorg/geometry/blob/rescale_to_integer/include/boost/geometry/extensions/algorithms/buffer/buffer_inserter.hpp  
  
Exactly the same pattern (with visitor) was also done in partition:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/partition.hpp  
  
The code stays clean, though an extra argument is passed. But that visitor can also be used for purposes other than debugging.  
  
Passing a visitor to each algorithm is probably too much. However, having a specific debug struct which defaults to an empty method is often quite readable and useful, I think (and if necessary can later be passed). Note that the passing is necessary for SVG maps - they are created / completed completely outside that function.  
  
@mloskot @mkaravel  At the acceptance of Boost.Log I requirested a similar function, for example BOOST_DEBUG_LOG("current point" << wkt(p));  
in which also all debug code optimizes away. Such a function is easy to implement. I don't know if Boost.Log in the end enabled this. However we might provide it ourselves too, the code itself keeps then the BOOST_DEBUG_LOG statements but is cleaner w.r.t. #ifdef/#endif

---

## Comment 7

> Username: mloskot  
> Created_at: 2014-06-19 21:30:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/66#issuecomment-46619404  

On 19 June 2014 22:19, Menelaos Karavelas notifications@github.com wrote:  
  
> @mloskot https://github.com/mloskot I think there are several aspects  
> to be considered here:  
> - code style  
> - code readability  
> - code maintenance  
> - efficiency and maybe more.  
>   
> My understanding is that Adam was commenting with respect to the first  
> three, and you refer to the fourth item.  
  
Yes, indeed.  
I didn't comment on the first 3 as that could easily open discussion about  
personal preferences which is better to be avoided.

---

## Comment 8

> Username: mkaravel  
> Created_at: 2014-06-19 21:36:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/66#issuecomment-46620051  

@barendgehrels This seems very very promising...  
One of the things that I like about the visitor approach is that we can move out of the include directory all debug related code.

---

## Comment 9

> Username: mloskot  
> Created_at: 2014-06-19 21:37:35 UTC  
> Updated_at: 2014-06-19 21:37:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/66#issuecomment-46620216  

On 19 June 2014 22:52, Barend Gehrels notifications@github.com wrote:  
  
> @mloskot https://github.com/mloskot @mkaravel  
> https://github.com/mkaravel At the acceptance of Boost.Log I  
> requirested a similar function, for example BOOST_DEBUG_LOG("current point"  
> << wkt(p));  
>   
> in which also all debug code optimizes away. Such a function is easy to  
> implement. I don't know if Boost.Log in the end enabled this. However we  
> might provide it ourselves too, the code itself keeps then the  
> BOOST_DEBUG_LOG statements but is cleaner w.r.t. #ifdef/#endif  
  
I think that would be a good option indeed.  
It would improve [code quality of transparency](http://accu.org/index.php/journals/1572), and avoid (re)inventing the wheel, I guess.

---

## Comment 10

> Username: mkaravel  
> Created_at: 2014-06-19 21:44:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/66#issuecomment-46620938  

So with respect to this PR, are there any concrete suggestions as to how to proceed?

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2014-06-19 21:52:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/66#issuecomment-46621773  

I'm OK with merging (though I did not yet go through all commits)

---

## Comment 12

> Username: mkaravel  
> Created_at: 2014-06-19 21:55:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/66#issuecomment-46622155  

@barendgehrels Please take your time. It does not have to happen right now.  
I basically asking if you (or somebody else) would expect a different approach/implementation before the merge.

---
