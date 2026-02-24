# #6 added boost_bloom.natvis [Merged]

> Username: joaquintides  
> Created at: 2025-05-02 09:45:43 UTC  
> Updated at: 2025-05-06 14:43:46 UTC  
> Merged at: 2025-05-06 14:13:58 UTC  
> Closed at: 2025-05-06 14:13:58 UTC  
> Url: https://github.com/boostorg/bloom/pull/6  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-05-02 09:51:34 UTC  
> Url: https://github.com/boostorg/bloom/pull/6#issuecomment-2846824296  

An automated preview of the documentation is available at [https://6.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html](https://6.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html)

---

## Comment 2

> Username: joaquintides  
> Created_at: 2025-05-02 20:15:06 UTC  
> Updated_at: 2025-05-03 07:14:08 UTC  
> Url: https://github.com/boostorg/bloom/pull/6#issuecomment-2848034568  

Hi @k3DW, thanks for reviewing this! In particular, I've got no clue about what attributes `ExcludeView` and `Inheritable` do, and I just merely copied what I saw around.  
  
It'd be nice, if the lib is accepted, to have a GDB pretty-printer, but that's definitely beyond my skills :-)

---

## Review 3 [Commented]

> Username: k3DW  
> Created_at: 2025-05-03 21:04:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/bloom/pull/6#pullrequestreview-2813303287  

📁 extra/boost_bloom.natvis

```diff
  22 |+ 	<DisplayString>{{ capacity={capacity()} }}</DisplayString>
  23 |+ 	<Expand>
  24 |+ 		<Item Name="[hash_function]" ExcludeView="simple">
```

> Username: k3DW  
> Created_at: 2025-05-03 20:49:54 UTC  
> Updated_at: 2025-05-03 21:04:14 UTC  
> Url: https://github.com/boostorg/bloom/pull/6#discussion_r2072464200  

You have `ExcludeView="simple"` on every item, meaning that the "simple" view will be empty and therefore meaningless. What would be the point of that? I would recommend removing the `ExcludeView="simple"` attributes altogether, assuming each of these items is equally relevant. Otherwise, I would recommend only putting this attribute on some of the items

> Username: joaquintides  
> Created_at: 2025-05-04 09:04:31 UTC  
> Url: https://github.com/boostorg/bloom/pull/6#discussion_r2072562251  

97df326621e86def940994beccc12e230c0ed6d4

---

📁 extra/boost_bloom.natvis

```diff
  23 |+ 	<Expand>
  24 |+ 		<Item Name="[hash_function]" ExcludeView="simple">
  25 |+ 			*reinterpret_cast&lt;hasher*&gt;(static_cast&lt;hash_base*&gt;(this))
```

> Username: k3DW  
> Created_at: 2025-05-03 20:50:54 UTC  
> Updated_at: 2025-05-03 21:04:14 UTC  
> Url: https://github.com/boostorg/bloom/pull/6#discussion_r2072464395  

I notice you do `reinterpret_cast<..>(static_cast<..>(..))` as opposed to just a one-step cast. Is this to account for base class offsets?

> Username: joaquintides  
> Created_at: 2025-05-04 09:02:03 UTC  
> Updated_at: 2025-05-04 09:04:57 UTC  
> Url: https://github.com/boostorg/bloom/pull/6#discussion_r2072561731  

Yes, exactly, `filter` has two base classes, and the hasher is contained in the second one.

---

📁 extra/boost_bloom.natvis

```diff
  29 |+ 		</Item>
  30 |+ 		<Item Name="[data]" ExcludeView="simple">
  31 |+ 			has_array()?core().ar.buckets:nullptr
```

> Username: k3DW  
> Created_at: 2025-05-03 20:55:46 UTC  
> Updated_at: 2025-05-03 21:04:14 UTC  
> Url: https://github.com/boostorg/bloom/pull/6#discussion_r2072465119  

From a user perspective who is not very familiar with the library, what is the relevance of this `[data]`? Would it help a user more to see this in another form, other than `unsigned char*`?

> Username: joaquintides  
> Created_at: 2025-05-04 09:07:38 UTC  
> Updated_at: 2025-05-04 09:08:01 UTC  
> Url: https://github.com/boostorg/bloom/pull/6#discussion_r2072562911  

`[data]` is just the internal bit array, seen as an array of `unsigned char`s.  This is the memory region the member function `array` returns a pointer to (wrapped in a span object). I don't think I can provide a more meaningful view into this, as a bit array is, well, a bit array.

> Username: joaquintides  
> Created_at: 2025-05-04 11:12:35 UTC  
> Url: https://github.com/boostorg/bloom/pull/6#discussion_r2072588220  

Well, thanks for challenging me! Learning from your MapHelper view in Boost.Unordered I managed to get this (76bac473e18bb0621d7cd80a15919c4b0bec2f44):  
  
![natvis](https://github.com/user-attachments/assets/10470990-69cf-4670-8b6f-65717998b94a)

---

📁 extra/boost_bloom.natvis

```diff
  20 |+ 				0"
  21 |+ 	/>
  22 |+ 	<DisplayString>{{ capacity={capacity()} }}</DisplayString>
```

> Username: k3DW  
> Created_at: 2025-05-03 20:59:21 UTC  
> Updated_at: 2025-05-03 21:04:14 UTC  
> Url: https://github.com/boostorg/bloom/pull/6#discussion_r2072465693  

Is `[capacity]` the most salient property of this type? At a glance, does it give the most relevant information to the user, without needing to expand the dropdown? Why is it in the `DisplayString` instead of being another `Item`? I would also like to see this as an `Item`, regardless of whether it's in the `DisplayString`

> Username: joaquintides  
> Created_at: 2025-05-04 11:13:52 UTC  
> Url: https://github.com/boostorg/bloom/pull/6#discussion_r2072588469  

Yes, it is the most salient property. Also, I'm mimicking what the view for `std::vector` does (it shows just the size in the `DisplayString`). Added [capacity] as an item in 76bac473e18bb0621d7cd80a15919c4b0bec2f44.


---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2025-05-04 09:11:44 UTC  
> Url: https://github.com/boostorg/bloom/pull/6#issuecomment-2849099523  

An automated preview of the documentation is available at [https://6.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html](https://6.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2025-05-04 11:16:41 UTC  
> Url: https://github.com/boostorg/bloom/pull/6#issuecomment-2849154791  

An automated preview of the documentation is available at [https://6.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html](https://6.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2025-05-04 16:11:24 UTC  
> Url: https://github.com/boostorg/bloom/pull/6#issuecomment-2849295186  

An automated preview of the documentation is available at [https://6.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html](https://6.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html)

---

## Review 7 [Commented]

> Username: k3DW  
> Created_at: 2025-05-05 04:18:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/bloom/pull/6#pullrequestreview-2813770369  

📁 extra/boost_bloom.natvis

```diff
  35 |+ 		</Item>
  36 |+ 		<Item Name="[array]">
  37 |+ 			*this,view(ArrayView)
```

> Username: k3DW  
> Created_at: 2025-05-05 04:14:52 UTC  
> Updated_at: 2025-05-05 04:18:26 UTC  
> Url: https://github.com/boostorg/bloom/pull/6#discussion_r2072825591  

When showing the array items, it's more idiomatic Natvis to write it with the `ArrayItems` tag in this expansion directly, rather than as an `Item` that expands to a separate overload of the `Type`. See [these lines of STL.natvis for `std::vector`](https://github.com/microsoft/STL/blob/main/stl/debugger/STL.natvis#L1193-L1205) as an example  
  
Then you could remove the other overload of this `Type` tag on line 42

> Username: joaquintides  
> Created_at: 2025-05-05 07:43:33 UTC  
> Updated_at: 2025-05-05 11:10:56 UTC  
> Url: https://github.com/boostorg/bloom/pull/6#discussion_r2072983873  

I think I like my previous version better:  
  
With `ArrayView`:  
![natvis](https://github.com/user-attachments/assets/8f7727c2-9b39-47ad-beec-f6b37e9cfe0b)  
  
Without `ArrayView`:  
![imagen](https://github.com/user-attachments/assets/3beba46e-3f3a-44e0-9d99-67fe0c20c5ec)  
  
The second version:  
  
* Expands directly, which I'm not very happy about (the array is very inscrutable as it consists of random bits, so I don't think inspecting it is the thing users will want to do foremost).  
* Does not show the size (distinct from capacity, in particular size = capacity/8).  
* May give the false impresion that `boost::bloom::filter` has an `operator []`,  which is not the case --it's `boost::bloom::filter::array()` that has it.

> Username: k3DW  
> Created_at: 2025-05-06 03:25:50 UTC  
> Updated_at: 2025-05-06 03:26:47 UTC  
> Url: https://github.com/boostorg/bloom/pull/6#discussion_r2074673271  

Understood. In that case, the idiomatic way to express what you want is through a `Synthetic` element. It is confusing and may lead to buggy usage in the future if you overload the same type you're already in  
  
```xml  
<Synthetic Name="[array]">  
    <DisplayString>{{ data={(void*)data()} size={array_size()} }}</DisplayString>  
    <Expand>  
        <ArrayItems>  
            <Size>array_size()</Size>  
            <ValuePointer>data()</ValuePointer>  
        </ArrayItems>  
    </Expand>  
</Synthetic>  
```

> Username: joaquintides  
> Created_at: 2025-05-06 07:27:10 UTC  
> Url: https://github.com/boostorg/bloom/pull/6#discussion_r2074878950  

Yes, that's it! 60f3908710551e2d8a126a016402b9fbb2088fae:  
  
![natvis](https://github.com/user-attachments/assets/ea85c004-90c6-47b7-998f-e28cba161f89)


---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2025-05-06 07:36:39 UTC  
> Url: https://github.com/boostorg/bloom/pull/6#issuecomment-2853560076  

An automated preview of the documentation is available at [https://6.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html](https://6.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html)

---

## Review 9 [Approved]

> Username: k3DW  
> Created_at: 2025-05-06 13:43:52 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/bloom/pull/6#pullrequestreview-2818343863  

Looks great!

---

## Comment 10

> Username: joaquintides  
> Created_at: 2025-05-06 14:13:51 UTC  
> Updated_at: 2025-05-06 14:43:46 UTC  
> Url: https://github.com/boostorg/bloom/pull/6#issuecomment-2854738353  

> Looks great!  
  
Thanks for your review! It has greatly improved the original PR.

---
