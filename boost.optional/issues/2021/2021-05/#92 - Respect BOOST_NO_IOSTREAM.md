# #92 - Respect BOOST_NO_IOSTREAM [Closed]

> Username: lebdron  
> Created at: 2021-05-19 16:50:08 UTC  
> Updated at: 2021-05-20 05:28:59 UTC  
> Closed at: 2021-05-20 05:28:59 UTC  
> Url: https://github.com/boostorg/optional/issues/92  

It would be great to respect BOOST_NO_IOSTREAM by not defining `operator<<` in `optional.hpp` to avoid compilation issues with Google Test, similar to how it is done in Boost::Variant  
https://github.com/boostorg/variant/blob/d80cb48f0294310ccba477923eedff93f9d73aa4/include/boost/variant/variant.hpp#L2452

---

## Comment 1

> Username: akrzemi1  
> Created at: 2021-05-19 21:32:04 UTC  
> Url: https://github.com/boostorg/optional/issues/92#issuecomment-844489074  

Thanks for reporting this. I have just pushed a commit to develop. Does this address your problem?

---

## Comment 2

> Username: lebdron  
> Created at: 2021-05-20 05:28:58 UTC  
> Url: https://github.com/boostorg/optional/issues/92#issuecomment-844708937  

Yes, looks perfect, thank you for the quick response!
