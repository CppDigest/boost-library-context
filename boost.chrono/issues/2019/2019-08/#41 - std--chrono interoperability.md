# #41 - std::chrono interoperability [Open]

> Username: Mike-Devel  
> Created at: 2019-08-20 09:01:57 UTC  
> Updated at: 2024-08-26 21:46:33 UTC  
> Url: https://github.com/boostorg/chrono/issues/41  

As currently discussed on the mailing list, `boost::chrono` has a poor interoperability story with the `std::chrono` types.  As a short term improvement, I'd like to add `to_std_duration`, `to_std_time_point` and `to_boost_duration`, `to_boost_time_point` functions to this library.  
  
Any comments on this idea before I start working on a PR?

---

## Comment 1

> Username: viboes  
> Created at: 2019-08-26 05:14:10 UTC  
> Url: https://github.com/boostorg/chrono/issues/41#issuecomment-524718422  

I've not followed thé discussion.  
Could you tell me what os the use case?

---

## Comment 2

> Username: Mike-Devel  
> Created at: 2019-08-26 09:02:29 UTC  
> Url: https://github.com/boostorg/chrono/issues/41#issuecomment-524782549  

Apparently some users complained (this was relayed by Howard Hinnand IIRC), that there is not easy interoperability between functions that use std::chrono and boost::chrono types.   
  
If I'm not mistaken, the following is not possible:  
  
    std::chrono::milliseconds foo();  
    void bar(boost::chrono::milliseconds);  
  
    bar(foo());  
  
and it becomes worse, when functions are templated and/or have mismatching periods (e.g. milli vs micro).   
  
I don't want to repeat the whole discussion, but long-term, my favorite solution (proposed by Alex Grund) would be to make the boost types and functions aliases for the c++11 chrono types as far as possible and only implement the extensions (such as I/O) in Boost.Chrono.   
  
Of course that may be a complete pipe-dream (no idea, what your Opinion on that matter is) and there will certainly be a lot of discussions and a deprecation period before that can happen anyway. Hence I think, it might be a nice short-term fix, to at least provide appropriate explicit conversion functions.

---

## Comment 3

> Username: viboes  
> Created at: 2019-08-26 19:30:04 UTC  
> Url: https://github.com/boostorg/chrono/issues/41#issuecomment-524997452  

Ok, l'll consider a complete  pr with tests and documentation

---

## Comment 4

> Username: ccorn  
> Created at: 2019-10-07 10:09:26 UTC  
> Url: https://github.com/boostorg/chrono/issues/41#issuecomment-538932927  

To encourage deliberations about the finer points, let me present code that I have been using so far. Abbreviations:  
~~~ c++  
    namespace sc = ::std::chrono;  
    namespace bc = ::boost::chrono;  
~~~  
  
Part 1: Type conversion between `std::ratio` and `boost::ratio`. This should supplement Boost.Ratio. The code below accepts anything with static `num` and `den` members and thus maps non-normalized ratios to normalized ratios, which may be counterintuitive, but this way I do not need to specify more detailed patterns with specific non-type template arguments. You may want to use a stricter approach instead.  
~~~ c++  
    // std::is_same_v<to_std_ratio_t<boost::ratio<N,D>>, std::ratio<N,D>>  
    // if and only if N, D are coprime and D is positive  
    template<typename Ratio>  
    using to_std_ratio_t = std::ratio<Ratio::num, Ratio::den>;  
  
    // std::is_same_v<to_boost_ratio_t<std::ratio<N,D>>, boost::ratio<N,D>>  
    // if and only if N, D are coprime and D is positive  
    template<typename Ratio>  
    using to_boost_ratio_t = boost::ratio<Ratio::num, Ratio::den>;  
~~~  
Part 2: Conversion functions between `{std,boost}::{duration,time_point}`:  
  
~~~ c++  
    template<typename Rep, typename Period> inline constexpr  
    sc::duration<Rep, to_std_ratio_t<Period>>  
    to_std_chrono(const bc::duration<Rep, Period> &d)  
    {  
        return sc::duration<Rep, to_std_ratio_t<Period>>  
                (d.count());  
    }  
  
    template<typename Rep, typename Period> inline constexpr  
    sc::time_point<sc::system_clock,  
                   sc::duration<Rep, to_std_ratio_t<Period>>>  
    to_std_chrono(const bc::time_point<bc::system_clock,  
                                       bc::duration<Rep, Period>> &t)  
    {  
        // Assuming same epoch  
        return sc::time_point<sc::system_clock,  
                              sc::duration<Rep, to_std_ratio_t<Period>>>  
                (to_std_chrono(t.time_since_epoch()));  
    }  
  
    template<typename Rep, typename Period> inline constexpr  
    bc::duration<Rep, to_boost_ratio_t<Period>>  
    to_boost_chrono(const sc::duration<Rep, Period> &d)  
    {  
        return bc::duration<Rep, to_boost_ratio_t<Period>>  
                (d.count());  
    }  
  
    template<typename Rep, typename Period> inline constexpr  
    bc::time_point<bc::system_clock,  
                   bc::duration<Rep, to_boost_ratio_t<Period>>>  
    to_boost_chrono(const sc::time_point<sc::system_clock,  
                                         sc::duration<Rep, Period>> &t)  
    {  
        // Assuming same epoch  
        return bc::time_point<bc::system_clock,  
                              bc::duration<Rep, to_boost_ratio_t<Period>>>  
                (to_boost_chrono(t.time_since_epoch()));  
    }  
~~~  
The names are just `to_std_chrono` and `to_boost_chrono`. More overloads would cover clocks other than `system_clock`, e.g. `steady_clock`. The `time_point` conversion assumes that the `boost` and `std` versions of the specified clock are functionally the same, and that their epochs are the same. Things get tricky if you cannot make such assumptions.

---

## Comment 5

> Username: Mike-Devel  
> Created at: 2019-10-08 09:47:33 UTC  
> Url: https://github.com/boostorg/chrono/issues/41#issuecomment-539438373  

Thanks for reminding me about this. I actually had a working version of this for quite some time now,  but never found the time/motivation to add the docs and write more tests.  
  
With the recent concern about anonymous contributors on the boost mailing list, I thought it was better if I ceased further work on boost altogether, but if that is not a problem for you, feel free to use that code: I just  committed my changes to https://github.com/Mike-Devel/chrono/tree/std_interop.  
Here is the diff: https://github.com/Mike-Devel/chrono/commit/9d09efb7520d974f7db86dc24b8b9ed6146ca0c0 )

---

## Comment 6

> Username: d3matt  
> Created at: 2024-08-26 21:46:32 UTC  
> Url: https://github.com/boostorg/chrono/issues/41#issuecomment-2311156971  

FWIW: as of boost 1.84, boost::ratio is a type alias of std::ratio. So Point 1 above should be done...
