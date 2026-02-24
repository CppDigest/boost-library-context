# #67 - IO error should be a warning [Closed]

> Username: mwpowellhtx  
> Created at: 2019-01-14 19:44:46 UTC  
> Updated at: 2019-01-15 01:30:13 UTC  
> Closed at: 2019-01-15 01:30:13 UTC  
> Url: https://github.com/boostorg/optional/issues/67  

I am using *Boost.Optional*, and, while I do want to "include it in an output", the library which I am doing so has another mechanism to do so.  
  
Translation, for *Optional* purposes, the following assertion [should be a warning, IMO, and not an error](/boostorg/optional/blob/develop/include/boost/optional/optional.hpp#L1591):  
  
```C++  
BOOST_STATIC_ASSERT_MSG(sizeof(CharType) == 0, "If you want to output boost::optional, include header <boost/optional/optional_io.hpp>");  
```  
  
Specifically, I am handling "stringification" of the Optional for Catch unit test purposes, for which I have a String Maker setup for what I am doing.  
  
Unless, mind you, it really is an error-error and should be covered, in which case, I suppose, yes, reporting an error assertion is appropriate.  
  
That being said, when I ensure that the *String Maker* is included, the compiler is smart enough to detect this and it does not assert.  
  
At the very least I do not think that the "error" should be quite as strong; i.e. when there are clearly alternatives to filling the output gap.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2019-01-14 20:02:04 UTC  
> Url: https://github.com/boostorg/optional/issues/67#issuecomment-454141842  

Could you illustrate the problem you are facing in more detail? Can you explain what you mean by "String Marker"?

---

## Comment 2

> Username: mwpowellhtx  
> Created at: 2019-01-14 20:06:35 UTC  
> Url: https://github.com/boostorg/optional/issues/67#issuecomment-454143230  

*String Maker* is a [Catch-ism](http://github.com/catchorg/Catch2/blob/master/docs/tostring.md#catchstringmaker-specialisation), so to speak. In this case, where ``convert(T const&)`` is ``convert(boost::optional<...> const&)``.  
  
When the include is made to my string maker, the assertion ("correctly") goes away; use case covered, I suppose?  
  
However, when the gap exists, unless it's truly show stopping, I think it should be a warning and not an error assertion.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2019-01-14 20:13:30 UTC  
> Url: https://github.com/boostorg/optional/issues/67#issuecomment-454145376  

So, if I understand correctly, you do not have a problem with this static assertion.  
  
I fail to imagine a use case where somebody would like to go with serializing an optional without having included the declarations for serialization functions.

---

## Comment 4

> Username: mwpowellhtx  
> Created at: 2019-01-14 20:18:35 UTC  
> Url: https://github.com/boostorg/optional/issues/67#issuecomment-454146820  

No, I think the assertion (error) should be a warning, notwithstanding above caveats. That said, I think the "strength" of the assertion could be softened up; i.e. I've illustrated the "recommended" solution is clearly not the only path forward. I also do not need to include the ``_io`` header, at least in this case, when a *String Maker* is provided. Anyway, FWIW.

---

## Comment 5

> Username: mwpowellhtx  
> Created at: 2019-01-14 20:35:20 UTC  
> Updated at: 2019-01-14 20:36:25 UTC  
> Url: https://github.com/boostorg/optional/issues/67#issuecomment-454151838  

I think I see what may be going on there. *String Maker* is converting the *Optional* value to a ``std::string``, which *does* have IO comprehension... My apologies for the confusion. To clarify, when I comment out the *String Maker* include, the *Optional* is standing there directly facing the Catch reporting mechanism, so the assertion happens.

---

## Comment 6

> Username: mwpowellhtx  
> Created at: 2019-01-15 01:30:13 UTC  
> Url: https://github.com/boostorg/optional/issues/67#issuecomment-454233812  

I'm satisfied this is probably a mis-read on my part.
