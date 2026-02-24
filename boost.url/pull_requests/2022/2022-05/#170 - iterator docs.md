# #170 iterator docs [Merged]

> Username: alandefreitas  
> Created at: 2022-05-16 17:20:18 UTC  
> Updated at: 2022-06-08 13:37:38 UTC  
> Merged at: 2022-06-04 00:26:51 UTC  
> Closed at: 2022-06-04 00:26:51 UTC  
> Url: https://github.com/boostorg/url/pull/170  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-16 17:21:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/170#pullrequestreview-974315914  

📁 include/boost/url/params_encoded.hpp

```diff
 270 |     //--------------------------------------------
 271 | 
 272 |     /** Checks whether the container is empty
```

> Username: vinniefalco  
> Created_at: 2022-05-16 17:21:54 UTC  
> Url: https://github.com/boostorg/url/pull/170#discussion_r873978858  

"Return true if the container is empty"


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-05-16 20:29:42 UTC  
> Updated_at: 2022-06-04 00:01:44 UTC  
> Url: https://github.com/boostorg/url/pull/170#issuecomment-1128105664  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/170?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#170](https://codecov.io/gh/CPPAlliance/url/pull/170?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (36b7482) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/fed8b009f65c4098e252e4e375bcea06ec892915?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (fed8b00) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head 36b7482 differs from pull request most recent head a7b742c. Consider uploading reports for the commit a7b742c to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/170/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/170?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #170   +/-   ##  
========================================  
  Coverage    96.73%   96.73%             
========================================  
  Files          119      119             
  Lines         6056     6056             
========================================  
  Hits          5858     5858             
  Misses         198      198             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/170?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/params.hpp](https://codecov.io/gh/CPPAlliance/url/pull/170/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW1zLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/params\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/170/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW1zX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/segments.hpp](https://codecov.io/gh/CPPAlliance/url/pull/170/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvc2VnbWVudHMuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/segments\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/170/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvc2VnbWVudHNfdmlldy5ocHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/170?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/170?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [fed8b00...a7b742c](https://codecov.io/gh/CPPAlliance/url/pull/170?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-16 23:39:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/170#pullrequestreview-974675061  

📁 include/boost/url/params_encoded.hpp

```diff
  64 |+ #endif
  65 | 
  66 |     /** A read-only random-access iterator to an encoded query parameter.
```

> Username: vinniefalco  
> Created_at: 2022-05-16 23:39:34 UTC  
> Updated_at: 2022-05-16 23:39:35 UTC  
> Url: https://github.com/boostorg/url/pull/170#discussion_r874239560  

well is it random access or is it forward?

> Username: alandefreitas  
> Created_at: 2022-05-16 23:41:53 UTC  
> Url: https://github.com/boostorg/url/pull/170#discussion_r874240528  

Oh...


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-19 01:50:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/170#pullrequestreview-977828384  

📁 include/boost/url/params_encoded.hpp

```diff
  65 | 
  56 |-     /** A read-only random-access iterator to an encoded query parameter.
  66 |+     /** A read-only forward iterator to an encoded query parameter.
```

> Username: vinniefalco  
> Created_at: 2022-05-19 01:50:11 UTC  
> Updated_at: 2022-05-19 01:50:20 UTC  
> Url: https://github.com/boostorg/url/pull/170#discussion_r876515130  

What about here? Does this inherit the javadoc since its an alias?

> Username: alandefreitas  
> Created_at: 2022-05-19 02:25:13 UTC  
> Updated_at: 2022-05-19 02:25:14 UTC  
> Url: https://github.com/boostorg/url/pull/170#discussion_r876528030  

No. If we remove the javadoc from `const_iterator`, it goes from this  
  
![image](https://user-images.githubusercontent.com/5369819/169189960-435c2882-b6c4-487a-b902-4dad31bf20b8.png)  
  
![image](https://user-images.githubusercontent.com/5369819/169190727-49361676-34ee-481e-ace6-88a6693765ee.png)  
  
to this  
  
![image](https://user-images.githubusercontent.com/5369819/169190119-f7ad59fc-8c25-4ca7-b2f4-e95138034608.png)  
  
![image](https://user-images.githubusercontent.com/5369819/169190271-95c26f85-4db6-4cf4-a045-c62d73c7f199.png)

> Username: sehe  
> Created_at: 2022-05-21 23:13:51 UTC  
> Updated_at: 2022-05-21 23:15:02 UTC  
> Url: https://github.com/boostorg/url/pull/170#discussion_r878770501  

This can be a simple  
  
```c++  
    /// @copydoc iterator  
    using const_iterator = iterator;  
```  
  
It renders exactly as before on `iterator`:  
  
![image](https://user-images.githubusercontent.com/324097/169671834-f4a82ca8-aa8f-4191-be88-4ebefad7f6ec.png)

> Username: vinniefalco  
> Created_at: 2022-05-22 15:36:00 UTC  
> Url: https://github.com/boostorg/url/pull/170#discussion_r878889032  

`copydoc` works?

> Username: sehe  
> Created_at: 2022-05-22 16:44:09 UTC  
> Url: https://github.com/boostorg/url/pull/170#discussion_r878897354  

I posted (local) picture proof!


---

## Review 5 [Changes requested]

> Username: sehe  
> Created_at: 2022-05-21 23:15:02 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/url/pull/170#pullrequestreview-980883338  

Yay. I finally found the time. I think it was actually worth-while. I learned about the customs around iterator implementations and documentation, and you found out about the very useful `@copydoc` :)  
  
See also https://www.doxygen.nl/manual/commands.html

📁 include/boost/url/params_encoded.hpp

```diff
  57 |+         type @ref query_param_view.
  58 |+ 
  59 |+      */
```

> Username: sehe  
> Created_at: 2022-05-21 23:12:55 UTC  
> Updated_at: 2022-05-21 23:15:02 UTC  
> Url: https://github.com/boostorg/url/pull/170#discussion_r878770434  

Okay here goes nothing.  
  
I would probably not repeat the reference/value types here, they're documented on the container already, and the standard container model expects iterators to match those. [That's even true in the case of `vector<bool>`](https://en.cppreference.com/w/cpp/container/vector_bool/reference).  
  
Compare e.g. [here](https://think-async.com/Asio/asio-1.18.1/doc/asio/reference/ip__basic_address_range_lt__address_v4__gt_.html) in asio (a rare example of a public `iterator` typedef):  
  
```c++  
  /// The type of an iterator that points into the range.  
  typedef basic_address_iterator<address_v4> iterator;  
```  
  
Now, that seems unfair, because `basic_address_iterator` if fully documented itself. However, here's `buffer_registration::[const_]iterator`:  
  
```c++  
  /// The type of an iterator over the registered buffers.  
  typedef unspecified iterator;  
  
  /// The type of a const iterator over the registered buffers.  
  typedef unspecified const_iterator;  
```  
  
If you insist, I'd _maybe_  link like so:  
  
```c++  
    /** A read-only forward iterator over encoded parameters  
  
        Indirection returns @ref reference  
     */  
```  
  
I don't think `value_type` actually occurs in the interface of the iterator implementation.  
  
Which results in:  
  
![image](https://user-images.githubusercontent.com/324097/169671771-3c80d976-b372-4da7-94e1-305a723619f6.png)

> Username: vinniefalco  
> Created_at: 2022-05-22 21:00:46 UTC  
> Url: https://github.com/boostorg/url/pull/170#discussion_r878925245  

> I don't think value_type actually occurs in the interface of the iterator implementation.  
  
It has to, otherwise a generic function cannot utter the name of a type that takes ownership of the value returned from `operator*`. For example:  
  
```  
template< class FwdIt >  
void f( FwdIt it, FwdIt end )  
{  
    while( it != end )  
    {  
        typename FwdIt::value_type v( *it++ );  
        f( std::move(v) );  
    }  
}  
```  
  
`FwdIt::operator*` could return `string_view`, in which case it would specify `value_type` as `std::string`.

> Username: vinniefalco  
> Created_at: 2022-05-22 21:01:04 UTC  
> Url: https://github.com/boostorg/url/pull/170#discussion_r878925268  

Although in theory my snippet should use `iterator_traits`

> Username: sehe  
> Created_at: 2022-05-22 22:29:47 UTC  
> Updated_at: 2022-05-22 22:29:48 UTC  
> Url: https://github.com/boostorg/url/pull/170#discussion_r878934671  

The indirected type is a depends on `reference` only. The `value_type` literally doesn't appear in the implementation of the iterator. In the broader sense one can still claim it is part of the interface, maybe (more Herb Sutter channeling).  
  
My point is not that `value_type` is irrelevant, rather that it makes more sense to keep that knowledge with `reference`, or indeed the container.  
  
I'm trying to think of valid reasons to repeat the `value_type` and reference with each member of the container. I'm not coming up with reasons that outweigh the noise. My rule of thumb would be: no need to repeat the obvious, unless there's a deviation from normal expectations.  
  
By the way, I fear I'm veering into philosophical bikeshedding. I'm happy to just drop my $0.02 and leave it to others :)

> Username: vinniefalco  
> Created_at: 2022-05-22 23:31:46 UTC  
> Url: https://github.com/boostorg/url/pull/170#discussion_r878941505  

> My point is not that value_type is irrelevant, rather that it makes more sense to keep that knowledge with reference, or indeed the container.  
  
Okay, how would that look in the code sample I gave above?


---

## Review 6 [Commented]

> Username: sehe  
> Created_at: 2022-05-21 23:18:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/170#pullrequestreview-980883532  

📁 include/boost/url/params_encoded.hpp

```diff
 263 |- 
 264 |-         @return True if container is empty
 272 |+     /** Return true if the container is empty
```

> Username: sehe  
> Created_at: 2022-05-21 23:18:15 UTC  
> Url: https://github.com/boostorg/url/pull/170#discussion_r878770821  

Prefer `///` for single line doc comments


---

## Comment 7

> Username: alandefreitas  
> Created_at: 2022-05-23 16:25:19 UTC  
> Url: https://github.com/boostorg/url/pull/170#issuecomment-1134885760  

I've updated the PR considering all the comments about not restating the obvious and replicated the pattern in other containers.   
The result is looking really nice, although I think the previews are not working for PRs for some reason.  
  
@copydoc helped a lot. Thanks for the suggestion, @sehe!  
I was a little confused when @vinniefalco told me to ask @sehe to review that single line of javadoc.  
I think I get it now. :)  
  
About "Prefer /// for single line doc comments", I've inferred @vinniefalco prefers `/** */` even for single-line comments by looking at other javadocs. Is that correct?

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2022-05-23 16:35:46 UTC  
> Url: https://github.com/boostorg/url/pull/170#issuecomment-1134896275  

>  Is that correct?  
  
Use whatever you feel is best

---
