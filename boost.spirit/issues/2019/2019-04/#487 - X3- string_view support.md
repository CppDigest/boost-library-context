# #487 - X3: string_view support [Open]

> Username: cmazakas  
> Created at: 2019-04-10 21:38:31 UTC  
> Updated at: 2025-05-10 19:59:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/487  

`std::` and `boost::string_view` are popular types. First-class native support for them in X3 would be quite nice.  
  
Are there plans to someday support `string_view` ? `boost::iterator_range` is showing its age somewhat.

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-04-10 23:02:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/487#issuecomment-481899288  

It would be nice if we can stop using `boost::iterator_range` or at least replace it with something else (because including it includes most of the Boost.Range library), but it cannot be `string_view` as it limited only to character type pointers as iterators.  
  
But the actual problem is in `x3::traits::move_to` that only supports types that can be constructed with pair of iterators and a strange decision to not have such constructor in `string_view`. The other minor problem that it is treated as a container.  
  
The good news for you that you do not need to wait until Spirit supports every span/view/range type out of the box (there are bunch of them), because you can easily make you own `raw` directive that will handle the type you are interested in. https://wandbox.org/permlink/YQwzge0rssBUPfyp

---

## Comment 2

> Username: cmazakas  
> Created at: 2019-04-11 00:29:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/487#issuecomment-481920477  

This code's amazing!  
  
I had done some digging into X3's internals and I had an idea that it'd be possible but I never would've come up with your implementation on my own :P  
  
Thank you! I'm going to study this and then likely copy-paste it into my codebase. Feel free to close, if you'd like. I'm a contented user.

---

## Comment 3

> Username: djowel  
> Created at: 2019-08-26 02:35:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/487#issuecomment-524693763  

OK, so where are we now on this? Are we expecting a PR or something?

---

## Comment 4

> Username: cmazakas  
> Created at: 2019-08-27 02:12:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/487#issuecomment-525105699  

Personally, I've been using @Kojoley's solution.  
  
But they bring up a good point about `x3::traits::move_to`. We could augment it to have an overload for view-like types such as `span` and `basic_string_view` in addition to the current range-based overloads.

---

## Comment 5

> Username: djowel  
> Created at: 2019-08-27 03:33:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/487#issuecomment-525122194  

I like string_view, let's support it.

---

## Comment 6

> Username: cmazakas  
> Created at: 2019-08-27 22:39:33 UTC  
> Url: https://github.com/boostorg/spirit/issues/487#issuecomment-525510640  

Coolio! I'll take a look this weekend!

---

## Comment 7

> Username: mrnerdhair  
> Created at: 2020-02-02 06:17:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/487#issuecomment-581103226  

Just want to add my +1 for this feature; I've been trying for two days now to hack in the support without the compiler smashing me in the face with a frying pan, but overloading function templates reliably is hard. (Did you know that `__gnu_cxx::__normal_iterator<char*, std::__cxx11::basic_string<char> >` isn't convertible to a `const char*`? I didn't. I'm not even sure it's true, but that's what my terminal is telling me right now.)

---

## Comment 8

> Username: cmazakas  
> Created at: 2020-02-02 15:41:59 UTC  
> Url: https://github.com/boostorg/spirit/issues/487#issuecomment-581147460  

Did the above Wandbox link not work for you? Hmm, I can try taking a look myself. I've been too tired after work during the week to do any real coding outside of work so far.

---

## Comment 9

> Username: mrnerdhair  
> Created at: 2020-02-02 19:01:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/487#issuecomment-581165800  

I did finally figure that out today; I had to add a `&*` to the iterator (making the construction of the `std::string_view` into `attr = { &*saved, typename Attribute::size_type(first - saved) };`). Otherwise that `__gnu_cxx` iterator garbage doesn't convert to a `const char*` and the compiler can't find a matching constructor for `std::string_view` -- and it hides the error by saying it's looking for a constructor that takes `(braced-init-list)` and helpfully not mentioning that it's just that *particular* list it's having trouble with.  
  
The main problem here, I think, is the lack of a partial specialization in raw.hpp for `parse()` where `Attribute` is `std::string_view`. (Or `std::basic_string_view<T>`, for generality.) Because you can't partially-specialize a class member template, the custom raw-directive is needed, but that duplicates boilerplate (`my::raw_gen` and `my::raw` are equivalent to the standard versions, just in a different namespace) and requires a new namespace -- which you have to name.  
  
I do think it's reasonable to support `std::string_view` via the raw operator, since unlike `std::string` it can only repesent a contiguous range, but I'd much prefer not having to hack it in myself.  
  
In case it helps, here's the set of [helpers](https://wandbox.org/permlink/ULcuFxVeHOXgXjKA) I've developed that let me specify X3 grammars with minimal verbosity. (Luckily I don't need recursive expressions yet; I haven't got an acceptably-readable solution for those yet.)

---

## Comment 10

> Username: Kojoley  
> Created at: 2020-02-06 18:39:55 UTC  
> Updated at: 2020-02-06 18:41:15 UTC  
> Url: https://github.com/boostorg/spirit/issues/487#issuecomment-583049490  

> I did finally figure that out today; I had to add a &* to the iterator (making the construction of the std::string_view into attr = { &*saved, typename Attribute::size_type(first - saved) };).  
  
Do not do `&*it`, it is generally not supported. You should not be using `std::string_view` with `std::string::(const_)iterator`, ~either~ call `parse` with raw character pointers (`str.data(), str.data() + str.size()`) ~or use `std::basic_string_view<std::string::(const_)iterator>`~.

---

## Comment 11

> Username: saki7  
> Created at: 2025-05-10 00:15:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/487#issuecomment-2868134253  

Closing this issue for several reasons  
  
1. Stale.  
2. `string_view` for inputs is already supprted via iterators  
3. `string_view` for attributes is legit feature request, but (1) Kojoley already showed a workaround, and (2) even if it would be a first-class types on X3, its usage is semantically limited on the places where the attributes consist of a pair of iterators.  
  
Feel free to reopen if you have any alternative suggestions.

---

## Comment 12

> Username: cmazakas  
> Created at: 2025-05-10 13:20:28 UTC  
> Updated at: 2025-05-10 16:05:38 UTC  
> Url: https://github.com/boostorg/spirit/issues/487#issuecomment-2868846187  

There's also no more point because Boost.Parser supersedes this library.

---

## Comment 13

> Username: saki7  
> Created at: 2025-05-10 19:59:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/487#issuecomment-2869133497  

I have changed my mind. I guess we have a clear reason to support this. I'm open for a PR.
