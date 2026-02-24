# #236 [experimental] printable hana::template_ [Closed]

> Username: ricejasonf  
> Created at: 2016-01-12 07:53:06 UTC  
> Updated at: 2016-01-18 20:53:59 UTC  
> Closed at: 2016-01-18 20:31:04 UTC  
> Url: https://github.com/boostorg/hana/pull/236  

If you would like, I can do this for the other `Metafunctions` for consistency, but first I wanted to gauge your reaction to giving them their own tags.  
  
I think this would be useful for more than just `Printable`.

---

## Comment 1

> Username: ricejasonf  
> Created_at: 2016-01-16 06:47:08 UTC  
> Url: https://github.com/boostorg/hana/pull/236#issuecomment-172166163  

I made the suggested changes, but did not add any more implementaions. Perhaps the default should print the output from `boost::core::demangle`.

---

## Comment 2

> Username: ldionne  
> Created_at: 2016-01-16 20:02:01 UTC  
> Url: https://github.com/boostorg/hana/pull/236#issuecomment-172250749  

Random note: It's crazy and scary to think that any comment we made on the original diff disappears when we update the diff. Not-so-good job from GitHub on this.

---

## Comment 3

> Username: ldionne  
> Created_at: 2016-01-16 20:03:06 UTC  
> Url: https://github.com/boostorg/hana/pull/236#issuecomment-172250803  

Question: Why did you not implement it for other `Metafunction`s? Is it just a question of time, or is there any underlying issue that would prevent that?

---

## Comment 4

> Username: ricejasonf  
> Created_at: 2016-01-16 22:24:23 UTC  
> Url: https://github.com/boostorg/hana/pull/236#issuecomment-172264116  

> Random note: It's crazy and scary to think that any comment we made on the original diff disappears when we update the diff. Not-so-good job from GitHub on this.  
  
I'd bet the information is still there, but the revision is no longer displayed in the ui.  
  
> Question: Why did you not implement it for other Metafunctions? Is it just a question of time, or is there any underlying issue that would prevent that?  
  
Actually I'm going to try doing a specialization for all `hana::Metafunction`s.

---

## Comment 5

> Username: ricejasonf  
> Created_at: 2016-01-17 01:03:28 UTC  
> Url: https://github.com/boostorg/hana/pull/236#issuecomment-172281598  

I tried implementing a `print_impl` with `when<hana::Metafunction<...>...`, but it was problematic getting it to match the variadic templates not to mention that `metafunction_class` has a different signature (ie it is not variadic).  
  
I went ahead and added explicit specializations for each Metafunction and switched to using a **regex** to "prettyify" the output. If you don't want it pulling in `<regex>` I can change it back, but I think it is more robust this way.

---

## Comment 6

> Username: ldionne  
> Created_at: 2016-01-18 15:09:31 UTC  
> Url: https://github.com/boostorg/hana/pull/236#issuecomment-172554963  

That seems good. I don't mind the `<regex>` include. Can you please squash those two commits and I'll merge the PR?

---

## Comment 7

> Username: ricejasonf  
> Created_at: 2016-01-18 18:56:48 UTC  
> Url: https://github.com/boostorg/hana/pull/236#issuecomment-172622635  

I won't be getting to it immediately, but I would like to add Printable to the functions in Functional. Should merging be deferred and I add that here, or should I just do a new PR later?

---

## Comment 8

> Username: ldionne  
> Created_at: 2016-01-18 19:05:42 UTC  
> Url: https://github.com/boostorg/hana/pull/236#issuecomment-172624500  

Why do you need to print the functions in Functional? Printing a function properly is impossible in the general case.. In all cases, that should be attempted in a different PR.

---

## Comment 9

> Username: ldionne  
> Created_at: 2016-01-18 20:31:04 UTC  
> Url: https://github.com/boostorg/hana/pull/236#issuecomment-172646281  

Merged in 2b0ede886e922858e5c68b3732d52cbcad89a5c4.

---

## Comment 10

> Username: ricejasonf  
> Created_at: 2016-01-18 20:39:00 UTC  
> Url: https://github.com/boostorg/hana/pull/236#issuecomment-172647677  

> Why do you need to print the functions in Functional?  
  
In my case I have trees with types and templates in them. It is useful to be able to use something like `hana::partial` on the result of `hana::template_` in some cases. So maybe it would look like:  
  
```  
partial(template<nbdl::StoreEmitter>, type<std::unordered_map>)  
```  
  
where `nbdl::StoreEmitter` is a template taking two types.  
  
My current workaround has been to wrap the whole works in `hana::type_c<...>`, because otherwise `print` explodes.

---

## Comment 11

> Username: ldionne  
> Created_at: 2016-01-18 20:41:58 UTC  
> Url: https://github.com/boostorg/hana/pull/236#issuecomment-172648223  

Hmm. It would work fine when the function is a `template_`, but what about when the function is something arbitrary? For example, what would you expect `hana::experimental::print([]{})` to output?

---

## Comment 12

> Username: ricejasonf  
> Created_at: 2016-01-18 20:48:24 UTC  
> Url: https://github.com/boostorg/hana/pull/236#issuecomment-172649500  

lol idk. I should probably play around with it and see what is feasible.

---

## Comment 13

> Username: ldionne  
> Created_at: 2016-01-18 20:53:59 UTC  
> Url: https://github.com/boostorg/hana/pull/236#issuecomment-172650577  

Sure; don't hesitate to create another PR or an issue if you think this is a valuable addition, and we'll discuss. I'm just a bit skeptical, just as I would be if someone proposed to make functions `Comparable` (impossible in the general case). I'm not saying that printing functions is not feasible, but it certainly is not obvious how to do it sensibly.

---
