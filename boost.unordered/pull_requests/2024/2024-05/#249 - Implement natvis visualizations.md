# #249 Implement natvis visualizations [Merged]

> Username: k3DW  
> Created at: 2024-05-28 03:26:01 UTC  
> Updated at: 2024-06-02 18:00:18 UTC  
> Merged at: 2024-06-02 18:00:09 UTC  
> Closed at: 2024-06-02 18:00:09 UTC  
> Url: https://github.com/boostorg/unordered/pull/249  

Partial work on #162. This applies to containers with raw pointers as the allocator pointer type. This does not account for fancy pointers.

---

## Comment 1

> Username: joaquintides  
> Created_at: 2024-05-31 11:46:49 UTC  
> Url: https://github.com/boostorg/unordered/pull/249#issuecomment-2141870030  

Some preliminary comments:  
  
* Non-SIMD layout does not work for me. This can be tried defining `BOOST_UNORDERED_DISABLE_SSE2` and/or bulding on x86 mode. I understand this is known and non-regular layouts will be addressed in the future.  
* Can you explain to me how regular layout detection works? Note you can directly rely on [`table_core::group_type::regular_layout`](https://github.com/boostorg/unordered/blob/develop/include/boost/unordered/detail/foa/core.hpp#L222).  
* What does "mo" mean in `__mo_bit` etc.?  
* What does "en" mean in `__issen_regular_layout_true` etc.?  
* I'd rename `base1`, `base2` and `base3` to `base0`, `base1` and `base2`. Also, I'd add a comment these are for Natvis, because the perplexed reader may find they are not used anywhere in the code.  
* I learned from your article that Natvis doesn't allow calling code functions, yet we have [this](https://github.com/boostorg/unordered/blob/feature/natvis/extra/boost_unordered.natvis#L425-L429), where `table_iterator::operator*` is invoked?  
* Open-addressing `end()` displays as "it &lt;struct at NULL&gt;", which is not bad if compared with `std` containers ("it (&lt;Error reading characters of string.&gt;, -842150451)"). Closed-addressing `end()`, though, looks uglier ("it ({...}, ???)"). Not sure we can/should do anything about it, anyway.  
* No documentation and release notes yet. I can provide these if you'd like.  
* Proabably not worth it, but we can also display stats when `BOOST_UNORDERED_ENABLE_STATS` is defined. @pdimov @cmazakas your opinion on this?  
* For the record, fancy pointers are not supported, and probably we won't ever support them. Yet, I'm puzzled by the use of `operator*` mentioned above, which would suggest supporting fancy pointers is indeed possible.

---

## Comment 2

> Username: k3DW  
> Created_at: 2024-05-31 19:22:26 UTC  
> Updated_at: 2024-05-31 21:40:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/249#issuecomment-2142856569  

Thanks for the comments. I'll address each one of them below.  
* Non-SIMD layout *does* work for me, so we can sort that out in private. Typically when I switch, it requires an entire "rebuild" in Visual Studio, not just a "build".  
* Yes this is exactly how I detect regular layout. See [here](https://github.com/boostorg/unordered/blob/feature/natvis/extra/boost_unordered.natvis#L405-L406) for example.  
* "mo" just means "match occupied". It's a helper intrinsic for the actual `__match_occupied` intrinsic. It seems like this is too confusing, so I should use the full name instead of abbreviating.  
* Likewise "issen" means "is sentinel". These are helpers for `__is_sentinel`. Similarly, it looks like I should use the full name here for clarity.  
* The `baseN` aliases are following the same naming scheme as `boost::unordered::detail::compressed`, which has `base1` and `base2` aliases. I'll leave a comment explaining these are for natvis. Let me know if I should still change the new `baseN` aliases, given the information about `detail::compressed`.  
* The link you provided does not show `table_iterator::operator*()`. This shows dereferencing the member `table_iterator::p_`, which has the type `table_iterator::table_element_pointer`. Without fancy pointers, this is a regular pointer that can be dereferenced as a primitive operation. This is not calling any user-defined `operator*()`.  
* I'll see what I can do about the visualization of `end()` iterators.  
* I'll also work on the docs and release notes. No need to provide it. I'll take feedback on that when I do it.  
* I'm not familiar with the stats, but we can talk about that as well.  
* Supporting individual types of fancy pointers is possible, but I will make the case that support for generalized fancy pointers is impossible as a single unified implementation. As I mentioned a few points up, this is not calling any user-defined `operator*()`, which would be disallowed in natvis. I think more discussion of supporting specific types of fancy pointers (like `boost::interprocess::fancy_pointer` for example) should come after the initial natvis implementation here. Then we can reassess.

---

## Comment 3

> Username: joaquintides  
> Created_at: 2024-06-01 09:27:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/249#issuecomment-2143378513  

> Non-SIMD layout does work for me, so we can sort that out in private. Typically when I switch, it requires an entire "rebuild" in Visual Studio, not just a "build".  
  
I confirm it works for non-SIMD and/or x86 on my side after rebuilding. Thank you!  
  
> "mo" just means "match occupied" [...] Likewise "issen" means "is sentinel".  
  
OK, no need to change names if it works for you.  
  
> The `baseN` aliases are following the same naming scheme as `boost::unordered::detail::compressed`, which has `base1` and `base2` aliases.  
  
All right, makes sense. Please do add the comment, though.  
  
> The link you provided does not show table_iterator::operator*() [...]  
  
Thank you for the explanation. I stand corrected.  
  
> Supporting individual types of fancy pointers is possible, but I will make the case that support for generalized fancy pointers is impossible as a single unified implementation[...]  
  
Yes, you're right. I got sidetracked by my wrong assumption that code functions could indeed be invoked, which is not the case as discussed above. So, let's forget about fancy pointers for now.  
  
One additional observation:  
  
* I see you've written two versions of the element loop, one for regular layout and one for non-regular, basically replicating the code in `table_iterator::increment`.  Turns out that the version of `increment` for regular layout is there for performance reasons, and the non-regular-layout version should work equally well. So, you probably can retain only the non-regular-layout loop --the mo and issen functions, however, need be versioned for regular/non-regular.

---

## Comment 4

> Username: k3DW  
> Created_at: 2024-06-01 14:25:51 UTC  
> Url: https://github.com/boostorg/unordered/pull/249#issuecomment-2143467209  

> OK, no need to change names if it works for you.  
  
I disagree. If there's a point of possible confusion, I want to remove it. I don't want to be the only one who can maintain this code. I'll change the names.  
  
> So, you probably can retain only the non-regular-layout loop...  
  
Huh you're right. That removed over 100 lines. Good catch.

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-06-01 16:52:41 UTC  
> Url: https://github.com/boostorg/unordered/pull/249#issuecomment-2143512278  

An automated preview of the documentation is available at [https://249.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://249.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Review 6 [Commented]

> Username: joaquintides  
> Created_at: 2024-06-02 10:14:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/249#pullrequestreview-2092261292  

📁 extra/boost_unordered.natvis

```diff
 175 |+             <Parameter Name="i" Type="size_t" />
 176 |+         </Intrinsic>
 177 |+         <Intrinsic Name="bit_cast_to_size_t" Expression="*reinterpret_cast&lt;size_t*&gt;(&amp;d)">
```

> Username: joaquintides  
> Created_at: 2024-06-02 10:14:46 UTC  
> Updated_at: 2024-06-02 10:16:02 UTC  
> Url: https://github.com/boostorg/unordered/pull/249#discussion_r1623373443  

This crashes in x86 mode:  
  
![imagen](https://github.com/boostorg/unordered/assets/3283164/d759f936-41b2-4b9d-9ded-8b1d073af51a)  
  
I could fix it locally by rewriting as follows:  
```  
        <Intrinsic Name="bit_cast_to_double" Expression="*reinterpret_cast&lt;double*&gt;(&amp;i)">  
            <Parameter Name="i" Type="uint64_t" />  
        </Intrinsic>  
        <Intrinsic Name="bit_cast_to_uint64_t" Expression="*reinterpret_cast&lt;uint64_t*&gt;(&amp;d)">  
            <Parameter Name="d" Type="double" />  
        </Intrinsic>  
          
        <!-- https://en.wikipedia.org/wiki/Fast_inverse_square_root#Magic_number -->  
        <Intrinsic Name="__inv_sqrt_init" Expression="bit_cast_to_double(0x5FE6EB50C7B537A9ul - (bit_cast_to_uint64_t(x) &gt;&gt; 1))">  
            <Parameter Name="x" Type="double" />  
        </Intrinsic>  
```  
  
(FWIW, one of the changes (s/0x5FE6EB50C7B537A9/0x5FE6EB50C7B537A9ul) is probably not necessary, but felt it look cleaner.)

> Username: k3DW  
> Created_at: 2024-06-02 16:25:27 UTC  
> Url: https://github.com/boostorg/unordered/pull/249#discussion_r1623524105  

Thanks, I'll make those changes.


---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2024-06-02 16:52:39 UTC  
> Url: https://github.com/boostorg/unordered/pull/249#issuecomment-2143940611  

An automated preview of the documentation is available at [https://249.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://249.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---
