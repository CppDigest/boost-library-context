# #238 - add crop option for reduce [Closed]

> Username: HDembinski  
> Created at: 2019-10-17 11:06:27 UTC  
> Updated at: 2021-05-18 09:43:38 UTC  
> Closed at: 2021-05-18 09:43:38 UTC  
> Url: https://github.com/boostorg/histogram/issues/238  

`shrink` makes an axis more compact in memory without dropping event counts. Those counts in bins which are removed are put into underflow/overflow if those are present.  
  
`trim` would work like `shrink` but actually remove those events and drop the underflow/overflow bins from the trimmed axis.  
  
`slice` should get an option to also trim instead of doing what `shrink` does with indices.

---

## Comment 1

> Username: jpivarski  
> Created at: 2019-10-17 15:34:18 UTC  
> Url: https://github.com/boostorg/histogram/issues/238#issuecomment-543232262  

Java's `trim` is Python's `strip`, so that's another synonym to consider.

---

## Comment 2

> Username: henryiii  
> Created at: 2019-10-17 21:58:13 UTC  
> Updated at: 2019-10-17 21:58:30 UTC  
> Url: https://github.com/boostorg/histogram/issues/238#issuecomment-543378941  

`crop` is another one. That's what it would tend to be called when working with images (which are just histograms, really).

---

## Comment 3

> Username: daritter  
> Created at: 2019-10-18 10:52:08 UTC  
> Updated at: 2019-10-18 10:52:30 UTC  
> Url: https://github.com/boostorg/histogram/issues/238#issuecomment-543670736  

I tend to vote for `crop`:  
  
In my experience, `trim` is usually used to remove conditionally (e.g. trim all white space from the end) and crop is usually used to unconditionally cut at a given position.  
  
I would find `trim` more suitable to shrink the axis by dropping all bins below a certain threshold from one side

---

## Comment 4

> Username: HDembinski  
> Created at: 2019-10-21 12:24:14 UTC  
> Updated at: 2019-10-21 12:26:00 UTC  
> Url: https://github.com/boostorg/histogram/issues/238#issuecomment-544490267  

@henryiii @daritter `crop` is a very interesting idea, thank you for proposing this. ~The operation is called `crop` in image editors and an image is just a (scaled) histogram with three photon counts per spatial cell, now that I think about it.~ Actually, that's just what Henry already wrote.

---

## Comment 5

> Username: HDembinski  
> Created at: 2020-01-06 00:50:36 UTC  
> Updated at: 2020-01-06 00:51:14 UTC  
> Url: https://github.com/boostorg/histogram/issues/238#issuecomment-570967024  

I am about to add `crop`, but I need some feedback.  
`crop` is supposed to work in value space, like `shrink`. Now we also want to have a command to do what `crop` does in bin index space. How should that be called? Or would it simply an other parameter for `slice`?  
  
The signature for `slice` is `slice(axis_index, begin, end)`. I could extend it to `slice(axis_index, begin, end, cut_mode)`, where `cut_mode` is an enum class with `shrink` and `crop` as members.

---

## Comment 6

> Username: HDembinski  
> Created at: 2020-01-06 10:47:33 UTC  
> Url: https://github.com/boostorg/histogram/issues/238#issuecomment-571093194  

Feature is evolving here https://github.com/HDembinski/histogram/pull/105

---

## Comment 7

> Username: henryiii  
> Created at: 2020-01-06 19:43:23 UTC  
> Updated at: 2020-01-06 19:46:29 UTC  
> Url: https://github.com/boostorg/histogram/issues/238#issuecomment-571283743  

In a perfect world with no backward compatibility (which is not what we live in), I think it could look something like `slice(axis_index, begin, end, cut_mode)`, where `begin` and `end` can be either integers or `bh::loc(double)`, which, similar to `bh::weight` or `bh::sample`, marks the value as being in value space. (Or `bh.value`, name unimportant here). And `cut_mode` is the enum mentioned above. Then you could do something like `slice(0, 0, bh::loc(12.5*MeV))` which would slice from the histogram start to 12.5 MeV. "shrink" would be no longer needed and would simply forward on the values to slice wrapped in `bh::loc`.  
  
It is nice not to have to remember two sets of spellings based on value space or bin space, but instead have a universal way to switch between them per parameter.

---

## Comment 8

> Username: HDembinski  
> Created at: 2020-01-07 10:59:42 UTC  
> Updated at: 2020-01-07 11:02:39 UTC  
> Url: https://github.com/boostorg/histogram/issues/238#issuecomment-571539444  

This is a bit off-topic, but:  
I think the situation for C++ is different from Python. In Python we have the slicing syntax that is captured by your UHI to do new interesting things. Because of the boundary constraints of that, using something like bh.loc makes sense. In C++, it does not make so much sense. Writing `shrink(0, 0, 12.5)` is less typing than `slice(0, loc(0), loc(12.5))`. You don't really gain anything from using `loc`. I would not prefer this solution even if there were no backward compatibility to consider.

---

## Comment 9

> Username: HDembinski  
> Created at: 2020-01-07 11:10:51 UTC  
> Url: https://github.com/boostorg/histogram/issues/238#issuecomment-571543068  

I started thinking again about crop. Is crop really only useful if you want to project over the same axis or are there other uses for it?   
  
If you crop or slice makes no difference for the inner bins, only the values in the flow bins differs after the operation. crop could also be used to physically remove the extra bins to save memory, although that is not what the current implementation of crop does (it just does not fill the flow bins of the reduced histogram).

---

## Comment 10

> Username: henryiii  
> Created at: 2020-01-07 14:25:56 UTC  
> Url: https://github.com/boostorg/histogram/issues/238#issuecomment-571608236  

In C++, you still have to know the difference between slice and shrink, which is something non-experts will have to (keep?) looking up. Those words do not really mean anything about whether they are in value space or in bin space, and you can't mix them in one expression, so it's less powerful. Also, in C++, a little extra typing is not bad, since you are unlikely to be in a REPL or something like that where tying matters. Clarity does matter, and it is good to have simple concepts that are reused.  
  
I admit, I don't like the extra `()` to keep track of, but that's easily solved with literals:  `slice(0, 0_loc, 12.5_loc)`. (or "value" might be clearer, the point is the ability to select bin/value everywhere rather than duplicating the interface). It's still just a suggestion for you to consider, not something I'm convinced is correct. And it would need to handle categories reasonably well (which I *think* it could, but I haven't thought about it much).  
  
FWIW, this does not affect the Python interface, since the value -> bin conversion is done in Python in UHI. So all boost-histogram code uses the bin interface only (shrink is only provided for the `cpp` module).

---

## Comment 11

> Username: henryiii  
> Created at: 2020-01-07 14:36:06 UTC  
> Updated at: 2020-01-07 14:53:00 UTC  
> Url: https://github.com/boostorg/histogram/issues/238#issuecomment-571612677  

Currently, in boost-histogram, the use for crop is entirely for `[:5:bh.sum]` or `[4:10:bh.sum]`, where you want sum over the axis you crop, and you really do want to set a limit on one end or the other, or both. We currently work around it missing by just zeroing out the flow bins after doing the slice.  
  
It might be useful to have a way to physically remove the flow bins and convert axes as part of the reduction, though I don't think there is a way to express that in UHI. And if that is added, what about adding a flow bin to an axis that doesn't have one? This almost seems like it might be something that could be added through a "clone" function that could take a histogram and a new collection of axes that have compatible lengths to the ones in the histogram but different meanings and options, such as flow, and creates a new histogram smartly copying the data.  
  
```cpp  
using regular_noflow = bh::axis::regular<double, use_default, use_default, axis::option::none_t>;  
bh::histogram hist1(bh::axis::regular(10,-1,1));  
// Fill...  
auto hist2 = bh::clone(hist1, regular_noflow(10,-1,1));  
// Now hist2 has the content of hist1, but no flow bins  
```

---

## Comment 12

> Username: HDembinski  
> Created at: 2020-01-08 16:40:26 UTC  
> Updated at: 2020-01-08 16:40:54 UTC  
> Url: https://github.com/boostorg/histogram/issues/238#issuecomment-572153444  

The literals syntax is nice, but introducing literals to the interface is not a good idea. Like you said, Boost.Histogram in C++ will be mostly used in non-interactive form and as a backend. In this case you do not pass literals to slice or shrink. I think the case where you want to slice using an index, value pair is remote, and even if it occurs, you can just convert the other value "by hand" using the corresponding axis.index(...).  
  
In any case, all this is off-topic. It seems there are no objections to the syntax of the `slice` command to support crop and shrink mode.

---

## Comment 13

> Username: HDembinski  
> Created at: 2020-08-10 14:00:54 UTC  
> Url: https://github.com/boostorg/histogram/issues/238#issuecomment-671372335  

`crop` should be able to remove the flow bins, agreed. It cannot do that right now for technical reasons, it is difficult to add the functionality in such a way that histograms with dynamic and static axes work with it.

---

## Comment 14

> Username: HDembinski  
> Created at: 2021-05-18 09:43:38 UTC  
> Url: https://github.com/boostorg/histogram/issues/238#issuecomment-843022263  

Closing this as we have crop...
