# #348 - Early Out For Runtime Folds [Closed]

> Username: mmha  
> Created at: 2017-05-19 14:28:51 UTC  
> Updated at: 2017-07-05 17:52:11 UTC  
> Closed at: 2017-07-04 01:59:01 UTC  
> Url: https://github.com/boostorg/hana/issues/348  

Consider this code:  
  
```c++  
#include <boost/hana.hpp>  
#include <string>  
#include <sstream>  
  
using namespace boost::hana;  
  
auto parse(const std::string &str) {  
	return [ss = std::stringstream(str)](bool valid_stream, auto &element) mutable {  
		if(!valid_stream) {  
			return false; // Abort parsing after failure  
		}  
		ss >> element;  
		return ss.fail();  
	};  
}  
  
int main() {  
	tuple<int, std::string, double> t;  
	auto result = fold(t, true, parse("42 foo 23.0"));  
	return result;  
}  
```  
  
I need to put that really ugly condition into my callable to abort the parsing after failure. Would it be possible to create a higher-order function that works similar to `fold`, but expects the reduction state to be convertible to `bool` and breaks the fold if it evaluates to `false`?

---

## Comment 1

> Username: ricejasonf  
> Created at: 2017-05-26 04:06:28 UTC  
> Updated at: 2017-05-26 04:06:51 UTC  
> Url: https://github.com/boostorg/hana/issues/348#issuecomment-304185855  

Is there any reason a vanilla `while` loop would not be sufficient here?  
  
There is `hana::while_`, but the predicates used in Boost.Hana are all required to return compile-time bools (e.g. hana::true_, hana::false), and the iterating function doesn't necessarily call all of the predicates. It just looks at their return types.

---

## Comment 2

> Username: mmha  
> Created at: 2017-05-26 15:42:09 UTC  
> Updated at: 2017-05-26 15:42:41 UTC  
> Url: https://github.com/boostorg/hana/issues/348#issuecomment-304316197  

The example I provided is not very good.  
  
`while` is not sufficient because it is a Hana sequence and I want to abort the fold over my sequence as early as possible once an error occured. There is no iterable I could use. In my real code, it is a `result<T, E>` which may change its type after each monadic bind and therefore after each fold.  
  
All of this happens at runtime, so `hana::while_` is not sufficient, either.

---

## Comment 3

> Username: ricejasonf  
> Created at: 2017-05-26 18:41:31 UTC  
> Url: https://github.com/boostorg/hana/issues/348#issuecomment-304358994  

I have an experimental library that accomplishes exactly what you describe with "promises" on run-time values.  
  
Check it out: https://github.com/ricejasonf/nbdl/blob/master/test/nbdl/core/detail/find_index.cpp#L19  
  
The general idea is that I use "callbacks" instead of Either types which eliminates a run-time check at each step in the control flow.

---

## Comment 4

> Username: ldionne  
> Created at: 2017-06-09 04:00:21 UTC  
> Url: https://github.com/boostorg/hana/issues/348#issuecomment-307289376  

> but expects the reduction state to be convertible to `bool` and breaks the fold if it evaluates to false  
  
@mmha So, we're talking about a sort of `fold_until` function, right? Something that would have the following API:  
  
```c++  
template <typename Sequence, typename State, typename F, typename Predicate>  
auto fold_until(Sequence xs, State state, F f, Predicate pred) {  
  // essentially f(f(f(state, xs[0_c]), xs[1_c]), ...) but stop as soon as one of those  
  // intermediate results does not satisfy the predicate  
}  
```  
  
 I don't think that can be implemented in the general case, i.e. for a sequence with elements of different types with a reduction function `f` that may return different types at each step. Indeed, that would require the return type (which is different depending on when we stop) to depend on whether the predicate returns `true` or `false`, which is a runtime condition (per your requirement). This is easy to achieve if the `predicate` returns a boolean integral constant, however, since the "when to stop" point can be determined at compile-time.  
  
@mmha Is my understanding of the problem accurate?  
  
Now, if you only want to perform an action, or if the reduction function `f` always returns the same type, I think this can be implemented (and a callback-based approach would be one option). While I definitely think that this is useful (and in fact we're sorely missing a library that translates runtime to compile-time (Hana does the reverse mapping))  I don't think this has its place in Hana.

---

## Comment 5

> Username: mmha  
> Created at: 2017-06-09 17:42:47 UTC  
> Url: https://github.com/boostorg/hana/issues/348#issuecomment-307453880  

> So, we're talking about a sort of fold_until function, right?   
  
Yes. I browsed through the Hana documentation again and stumbled across `monadic_fold`, which seems do be something like that, but it only seems to work for compile-time values?  
  
> I don't think that can be implemented in the general case, i.e. for a sequence with elements of different types with a reduction function f that may return different types at each step.   
  
The way I stated the problem it would also be possible to return a variant of all return types of `f`. But I have to agree, this is probably out of scope for Hana.   
  
> Now, if you only want to perform an action, or if the reduction function f always returns the same type, I think this can be implemented (and a callback-based approach would be one option).  
  
This is indeed the case for me, and I will take a closer look on the code @ricejasonf linked me (this is particularly interesting because I need to introduce asynchronous networking to my code in the future anyways). But this would not be a generic solution for e. g. monads. Again, I agree with you that this would not be a good fit for Hana.

---

## Comment 6

> Username: ldionne  
> Created at: 2017-07-04 01:59:01 UTC  
> Url: https://github.com/boostorg/hana/issues/348#issuecomment-312762382  

>  I browsed through the Hana documentation again and stumbled across `monadic_fold`, which seems do be something like that, but it only seems to work for compile-time values?  
  
Hmm, technically, I think you should be able to make a runtime Monad like `std::optional` a model of the `hana::Monad` concept. However, I don't think a runtime monad would be sufficient to implement what you're trying to implement, since you need to somehow accumulate all the possible return types of your function. That looks like a completely different beast to me. After you've investigated other options pointed by @ricejasonf, please come back here if you do think it has its place in Hana and we can discuss this further.  
  
For now, closing this as not a problem to be solved by Hana.

---

## Comment 7

> Username: ricejasonf  
> Created at: 2017-07-04 02:19:00 UTC  
> Url: https://github.com/boostorg/hana/issues/348#issuecomment-312764584  

I was looking at mp11's implementation which specializes templates functions that switch on n values and call a user provided function with its own integral constant type. It would be cool to have this in Hana otherwise I was just going to have it as an implementation detail in my stuff when I got around to it.

---

## Comment 8

> Username: ldionne  
> Created at: 2017-07-05 17:52:11 UTC  
> Url: https://github.com/boostorg/hana/issues/348#issuecomment-313177707  

For reference, the file is [here](https://github.com/pdimov/mp11/blob/5c088f06332d723b66112c193c976d105f4b54f5/include/boost/mp11/detail/mp_with_index.hpp). I agree this is really a cool feature, however I don't think it has its place in Hana because this is touching the runtime => compile-time conversion problem, which is deep enough to mandate a full library solving the problem. Matt Calabrese's `std::call` is an instance of that. If I were to implement this in Hana, it would feel like half-solving the problem.  
  
I value highly libraries that try to completely solve a specific problem with a consistent vision throughout. I couldn't achieve this level by implementing just this functionality in Hana, so I'd rather not touch that problem at all.
