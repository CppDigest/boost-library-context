# #66 - serialization for boost/optional [Open]

> Username: robertramey  
> Created at: 2018-12-18 17:47:15 UTC  
> Updated at: 2018-12-19 00:03:08 UTC  
> Url: https://github.com/boostorg/optional/issues/66  

The boost serialization library includes an implementation for boost/optional.  This breaks the general rule that serialization of boost components be implemented as part of that component and that only the boost serialization only contain implementations of serialization for std components.  Now that there is a std::optional as part of C++17 we want to add another optional implementation to serialization.  But on order to do that we want to move the current implementation here  in order to "make room".  I need to know if the maintainers of boost::optional are OK with this and will be willing to merge the PR. My inclination is to do this via a fork/pull request.  Please let me know if this is all OK.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2018-12-18 23:07:31 UTC  
> Url: https://github.com/boostorg/optional/issues/66#issuecomment-448405776  

Let me check if I got it right. There is a "glue" code that makes Boost.Serialization work with Boost.Optional and it is currently part of Boost.Serialization. You want this glue now to become part of Boost.Optional. Is that right?

---

## Comment 2

> Username: robertramey  
> Created at: 2018-12-18 23:28:58 UTC  
> Url: https://github.com/boostorg/optional/issues/66#issuecomment-448411140  

Hmmm - I'm not sure I'd call it "glue" code.  It's the header/code which implements serialization for boost::optional.  It currently resides in the boost serialization library.  see https://github.com/boostorg/serialization/blob/develop/include/boost/serialization/optional.hpp along with a corresponding test.  Generally, we've left serialization of std types in the serialization library and boost types in the corresponding boost library.  Now we want to create a header/test for C++17 optional.  So this would go in the serialization library.  But the "slot" is already occupied by the boost version.  So I want to move the boost optional serializer into the boost optional library to "make room" to put new std optional serialization into the place where it should have gone in the first place.  Somehow this sounds more complicated than I think it should.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2018-12-18 23:56:04 UTC  
> Url: https://github.com/boostorg/optional/issues/66#issuecomment-448416648  

I am reluctant to do this. The way I see it, the code that instructs Boost.Serialization how to serialize Boost.Optional does not belong to either library. They should not know about each other. I can see why you do not want it in Boost.Serialization, but moving it on Boost.Optional doesn't seem to fix the problem. This problem is usually solved by providing a "glue" library: it is the library that is aware of both libraries it glues together. Providing a yet another submodule for this would be silly, I know. But I would like to see a more systematic policy for addressing this problem in Boost.  
  
I also do not understand this argument with "making room" in the "slots". I can see that you have one slot for `array` and one for `boost_array` and they seem to coexist just fine.

---

## Comment 4

> Username: robertramey  
> Created at: 2018-12-19 00:02:26 UTC  
> Url: https://github.com/boostorg/optional/issues/66#issuecomment-448418093  

> I can see that you have one slot for array and one for boost_array and they seem to coexist just fine.  
Right and that created a fair amount of confusion which I want to avoid this time.  
  
My underlying motivation is the fixation of responsibility. I want the person familiar with the internals of the optional library - or other library - to be the responsible for the serialization of it as well.  Putting it into serialization doesn't scale.  
  
But the question of "glue" libraries has come up has come up in the context of dependency management  - with no resolution.    
  
the serialization implementation must know about both the serialization library AND the library being serialized.  
  
This should really be raised on the mailing list.  The serialization library might be the biggest problem - but I don't think it's the only one.

---

## Comment 5

> Username: robertramey  
> Created at: 2018-12-19 00:03:08 UTC  
> Url: https://github.com/boostorg/optional/issues/66#issuecomment-448418277  

> moving it on Boost.Optional doesn't seem to fix the problem  
well it gets that particular monkey off MY back
