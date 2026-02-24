# #2843 - Any possibility to make dependency on Boost not required(like ASIO does)? [Open]

> Username: TheStormN  
> Created at: 2024-03-27 15:31:23 UTC  
> Updated at: 2024-08-01 22:04:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2843  

### Version of Beast  
353  
  
### Steps necessary to reproduce the problem  
It is a very nice thing that ASIO can be used without Boost. Given that Beast manly needs functionality from ASIO I think the request of such support should be not that far fetched.  
  
### All relevant compiler information  
N/A

---

## Comment 1

> Username: ashtum  
> Created at: 2024-03-27 15:38:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2843#issuecomment-2023089070  

This is not planned, as there is an ongoing effort to make Boost modular. Therefore, you would only need to install the Boost libraries that Beast depends on.

---

## Comment 2

> Username: TheStormN  
> Created at: 2024-03-27 15:43:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2843#issuecomment-2023098951  

By looking at the Beast dependencies, even if Boost becomes modular, it would still require quite a lot of stuff:  
```  
target_link_libraries(boost_beast  
  INTERFACE  
    Boost::asio  
    Boost::assert  
    Boost::bind  
    Boost::config  
    Boost::container  
    Boost::core  
    Boost::endian  
    Boost::intrusive  
    Boost::logic  
    Boost::mp11  
    Boost::optional  
    Boost::smart_ptr  
    Boost::static_assert  
    Boost::static_string  
    Boost::system  
    Boost::throw_exception  
    Boost::type_traits  
    Boost::utility  
    Boost::winapi  
)  
```  
  
Most of these are already available in C++17, so I would really appreciate if I don't need to bring dozen of additional stuff which C++ supports anyway.

---

## Comment 3

> Username: TheStormN  
> Created at: 2024-07-26 21:53:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2843#issuecomment-2253557426  

@ashtum If I would spend effort when I have free time in making the library to be like `ASIO` in Boost compatibility regard, will the PRs be welcomed? Of course the standalone version will require newer C++ standard than the one which Boost requires.

---

## Comment 4

> Username: ashtum  
> Created at: 2024-07-31 08:39:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2843#issuecomment-2259972840  

Sorry for the late reply; I missed the notification. Replacing the dependencies and maintaining two versions wouldn't be trivial, especially if you want to support C++11. What is the motivating use case for this change? Modular Boost is not far off, and each library would receive only what it needs, rather than the entire Boost libraries.

---

## Comment 5

> Username: TheStormN  
> Created at: 2024-07-31 10:25:48 UTC  
> Updated at: 2024-07-31 10:29:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2843#issuecomment-2260185830  

Even if boost becomes modular, you are still using quite a lot of it. I don't plan to target C++11. Perhaps C++17. Older C++ versions will be only supported by the Boost variant.

---

## Comment 6

> Username: ashtum  
> Created at: 2024-07-31 11:20:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2843#issuecomment-2260285149  

Yes, considering that Beast targets C++11, it had to use more facilities from Boost. However, how is this an issue, and what would be the benefits of reducing their usage?

---

## Comment 7

> Username: TheStormN  
> Created at: 2024-07-31 13:38:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2843#issuecomment-2260549077  

I guess the same benefits as standalone ASIO. No need to bring tons of 3rd party stuff(even if modular) given the C++ version we are using already have these facilities.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2024-07-31 14:19:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2843#issuecomment-2260641088  

The C++ Standard cannot even connect to the Internet...

---

## Comment 9

> Username: ashtum  
> Created at: 2024-07-31 14:40:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2843#issuecomment-2260685040  

> I guess the same benefits as standalone ASIO. No need to bring tons of 3rd party stuff(even if modular) given the C++ version we are using already have these facilities.  
  
ASIO has minimal dependencies on Boost, and from the start, Chris has aimed to implement most of the facilities within ASIO itself. It's important to consider that, back then, there were no package managers for C++, and people generally disliked having dependencies.

---

## Comment 10

> Username: TheStormN  
> Created at: 2024-08-01 22:04:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2843#issuecomment-2264090218  

And now we have several package managers and all of them with their quirks or missing packages. :)  
  
In general I think a Boost only version would be acceptable if there are no Boost types leaking into public headers, so people don't end up using them by incident, but I believe this is also not the case.
