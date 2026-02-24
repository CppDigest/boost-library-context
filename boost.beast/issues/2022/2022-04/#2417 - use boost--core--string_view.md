# #2417 - use boost::core::string_view [Closed]

> Username: vinniefalco  
> Created at: 2022-04-27 01:41:41 UTC  
> Updated at: 2023-05-07 09:20:33 UTC  
> Closed at: 2022-05-10 00:47:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2417  

We should use `boost::core::string_view` instead of `boost::utility::string_view`, because it has better interoperability with `std::string_view`. However, before we make this change public we need to write a short matrix of tests that mix the three principle string views and the standard string `std::string`, `std::string_view`, `boost::utility::string_view`, `boost::core::string_view` in order to understand what compatibility problems users may experience (if any).

---

## Comment 1

> Username: sehe  
> Created at: 2022-05-02 05:10:36 UTC  
> Updated at: 2022-05-02 05:11:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2417#issuecomment-1114502467  

I've created a whole slew of test-cases to excavate differences between various implementations. These are not up to standard for inclusion, but perhaps someone sees some useful bits?  
  
Goggles advised: https://github.com/sehe/beast/blob/core_string_view/test/beast/core/string.cpp  
  
Note it uses c++17 for brevity - not in the last place to be able to compare with c++17 std::string_view, but liberally using contexpr and traits that are specific to c++17.  
  
Differences between implementations that might be observed are marked OBSERVABLE

---

## Comment 2

> Username: sehe  
> Created at: 2022-05-04 18:21:16 UTC  
> Updated at: 2022-05-04 18:26:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2417#issuecomment-1117662392  

@vinniefalco Just realized another loose end: despite `std::string_view` compatibility, `core::string_view` still doesn't support some templated free functions, like  
  
 - std::quoted (too bad I guess)  
 - asio::buffer  
 - asio::const_buffer [demo](https://compiler-explorer.com/z/TxPx4qKv8)  
  
The latter are a shame. They circle back to your discussion about custom buffer types in boost-http somewhat.  
  
No regression or impact from the issue, just something we might want a solution to.

---

## Comment 3

> Username: sehe  
> Created at: 2022-05-05 15:16:54 UTC  
> Updated at: 2022-05-05 15:18:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2417#issuecomment-1118683371  

Status:   
  
 - IMO This should remain draft until https://github.com/boostorg/core/issues/110 lands, to avoid inconveniencing Beast users with breaking change in their own code using `beast::string_view`  
  
 - @vinniefalco / @pdimov Do you see any mechanism to aid with the compatiblity with std::string_view for free function templates (std::quoted, asio::buffer and possible others in thirdparty code)? Perhaps there's already a niebloid like `beast::buffer_bytes` that I'm not aware of for this?

---

## Comment 4

> Username: pdimov  
> Created at: 2022-05-05 15:23:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2417#issuecomment-1118692121  

What does "land" mean here?

---

## Comment 5

> Username: pdimov  
> Created at: 2022-05-05 15:27:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2417#issuecomment-1118697209  

What are we supposed to do about `std::quoted` or `asio::const_buffer`?

---

## Comment 6

> Username: sehe  
> Created at: 2022-05-05 15:44:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2417#issuecomment-1118722543  

The goal of this ticket is to make beast::string_view more compatible with std::string_view.  
  
Both these free functions are std::string_view features, but not compatible with core::string_view. I'm just asking in case I'm missing some fancy way to address these?

---

## Comment 7

> Username: sehe  
> Created at: 2022-05-05 15:46:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2417#issuecomment-1118724099  

@pdimov  "landing a ticket" means when it ships in the next release (it will merge to master, basically). Wasn't aware that was company specific lingo :/

---

## Comment 8

> Username: pdimov  
> Created at: 2022-05-05 16:09:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2417#issuecomment-1118752324  

It has been merged to master. Next release, well that's another story.  
  
We can't change `std::quoted`, so I'm not sure what we can possibly do about it.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2022-05-05 16:45:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2417#issuecomment-1118810265  

What the heck is `std::quoted`

---

## Comment 10

> Username: pdimov  
> Created at: 2022-05-05 17:10:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2417#issuecomment-1118846370  

It's a manipulator that quotes strings on op<< and unquotes them on op>>. https://godbolt.org/z/3srYsno9K

---

## Comment 11

> Username: sehe  
> Created at: 2022-05-05 23:28:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2417#issuecomment-1119136507  

@pdimov That's awesome. Yeah, I had expected the merge to be visible on boostorg/core#110 but that wasn't a PR in the first place. Are you saying that things that are on master do not /automatically/ get pulled into the superproject next release?

---

## Comment 12

> Username: pdimov  
> Created at: 2022-05-05 23:50:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2417#issuecomment-1119146100  

Yes, whatever's on master automatically goes into the next release. But the release itself is months from now.

---

## Comment 13

> Username: sehe  
> Created at: 2022-05-06 00:00:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2417#issuecomment-1119149888  

That's fine, the Beast change would be in the same release. As long as they go together or in the right order, I'm not in a hurry.
