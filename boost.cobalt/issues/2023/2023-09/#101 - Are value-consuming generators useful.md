# #101 - Are value-consuming generators useful? [Open]

> Username: akrzemi1  
> Created at: 2023-09-02 06:40:01 UTC  
> Updated at: 2023-09-23 08:57:52 UTC  
> Url: https://github.com/boostorg/cobalt/issues/101  

The two-argument variant of `async::generator`, one that is able to consume values from the resumer, seems to have no practical use case. At least none has been provided in the docs or examples. I personally question its utility because of the shape of the interface. The coroutine sees it this way:  
  
```  
auto value_from_resumer = co_yield value_from_coroutine;  
```  
  
Where a natural reading is that `value_from_resumer` is *in response to* the yielded `value_from_coroutine`. But the resumer sees it this way:  
  
```  
auto next_value_from_coroutine = co_await some_genertor(value_from_resumer);  
```  
  
Here the resumer has to prepare and inject the value before it asks the coroutine to generate its next value. This is in contradiction to the previous expectation.   
  
I presented my intuition. It may be wrong. But the documentation does not offer any other. I believe that the value consuming interface of `async::generator` should be either removed, or justified in the docs.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-09-04 07:03:09 UTC  
> Url: https://github.com/boostorg/cobalt/issues/101#issuecomment-1704722171  

Well, the generator can be made lazy now, so your intuition is probably correct for eager generators, that would do a look ahead. There's probably some niche use-case, but I would agree that it's more likely that pushing values would be used for lazy ones.  
But given that this is a runtime setting now, I don't see how that'll tarnish the interface.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2023-09-16 17:36:33 UTC  
> Url: https://github.com/boostorg/cobalt/issues/101#issuecomment-1722278908  

So, it looks to me that at least the recommended practice should be that if you use a push-value generator you always put `co_await async::this_coro::initial` in the coroutine.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2023-09-16 17:45:24 UTC  
> Url: https://github.com/boostorg/cobalt/issues/101#issuecomment-1722280639  

But even the lazy push-value generators: do they have a use case?  
There is an example in the docs:  
https://klemens.dev/async/tutorial.html#generator_with_push_value  
  
It does not compile for at least two reasons (unused variable `val` and `return` in a coroutine). It should be lazy, I think.  
  
Is there a full example anywhere? I have a feeling that a code that does not use a coroutine would be simpler. In the use case you simply request new things to be serialized. There is nothing being _generated_.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2023-09-18 00:47:28 UTC  
> Url: https://github.com/boostorg/cobalt/issues/101#issuecomment-1722624908  

The example is missing a `ser.reset(&val)` call, will fix that.  
  
The generator can make complex state management easier, because you can put things like the serializer into the generator frame. You need more complexity than that however for that to pay off (which makes bad examples) so I understand your skepticism. I would however also add, that you or I thinking something is simpler doesn't mean that users will agree. Based on my knowledge of other languages I am pretty confident that there are plenty of use cases.  
  
Regarding the name: that's just the common name for the same thing in other languages (python, js).  
  
To the "should always be lazy point": well that's how python does it. But there you have to `await` the generator once without a push value. I can't do that because C++ isn't dynamic, so that would be a bad interface. Therefor I think making it a runtime decision and giving the eager case valid (although weird) semantics is a proper fit for a C++ library.

---

## Comment 5

> Username: akrzemi1  
> Created at: 2023-09-18 14:57:11 UTC  
> Url: https://github.com/boostorg/cobalt/issues/101#issuecomment-1723618305  

By "recommended practice" I mean putting a note in docs, "if you are using a push-value generator, you probably want to perform the inirial suspense."

---

## Comment 6

> Username: akrzemi1  
> Created at: 2023-09-23 08:47:45 UTC  
> Url: https://github.com/boostorg/cobalt/issues/101#issuecomment-1732257376  

> The example is missing a `ser.reset(&val)` call, will fix that.  
  
That wouldn't be enough. It still has a plain `return` in the coroutine. I would recommend adding a full compiling program to folder `examples`.  
   
> The generator can make complex state management easier, because you can put things like the serializer into the generator frame. You need more complexity than that however for that to pay off (which makes bad examples) so I understand your skepticism. I would however also add, that you or I thinking something is simpler doesn't mean that users will agree. Based on my knowledge of other languages I am pretty confident that there are plenty of use cases.  
  
I agree with this argumentation for generators that do not consume values. My criticism is for the ones that consume values.   
  
I challenge the example not because of the "size of the maintained state" alone, but because of the combination: "size of the maintained state" and "nothing is generated".  
  
>   
> Regarding the name: that's just the common name for the same thing in other languages (python, js).  
  
I think that maybe the name is fine but the example is not exemplary of the capabilities of value-consuming generators. I realize that I just dump more work on you (find a better example), but I must admit, I am lacking imagination or intuition in this domain.

---

## Comment 7

> Username: akrzemi1  
> Created at: 2023-09-23 08:57:52 UTC  
> Url: https://github.com/boostorg/cobalt/issues/101#issuecomment-1732259143  

One more observation. Given that:  
  
- you have to specialize `generator_base` for type `void`,  
- synopsis in documentaiton lists conditional members (operators `()` and `co_await`)  
- that you seem to recomend different "lazines" defaults for the two cases: eager for `Push==void` and lazy for value-consuming generators,  
  
The design of this library would be simpler if these two were separate coroutine types:  
  
1. One that simply generate values.  
2. The other that generates and consumes values.
