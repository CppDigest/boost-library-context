# #233 - [Documentation] Minor issues in HTML version [Closed]

> Username: m-j-w  
> Created at: 2016-01-03 19:20:13 UTC  
> Updated at: 2016-01-12 17:54:54 UTC  
> Closed at: 2016-01-12 17:54:54 UTC  
> Url: https://github.com/boostorg/hana/issues/233  

Please consider changing the following line in `Doxyfile.in` to enable browsing the library sources from within the documentation:  
  
```  
SOURCE_BROWSER          = YES  
```  
  
Also, it seems like the alphabetical index in the documentation doesn't work (at least for my Firefox). It only shows everything starting with 'A'.  
  
Lastly, the namespaces seem not to be correctly recognized under `Headers`. See for example http://boostorg.github.io/hana/accessors_8hpp.html where it says `boost` for Namespace and links somewhere not really reasonable.  
  
No idea for both of the above where that originates.

---

## Comment 1

> Username: m-j-w  
> Created at: 2016-01-04 06:04:25 UTC  
> Updated at: 2016-01-04 06:07:09 UTC  
> Url: https://github.com/boostorg/hana/issues/233#issuecomment-168590089  

Oh, and I'd suggest adding a group 'Algorithms' to `hana.hpp`  
  
```  
//! @defgroup group-algorithms Algorithms  
//! Algorithms provided by the library for the various data types and structures.  
```  
  
and then adding a   
  
```  
//! @ingroup group-algorithms  
```  
  
to all the algorithms like -- well like all of them.  
  
This should then add another summary to the 'Reference documentation' section giving another helpful overview.  
  
Perhaps some subdivision whether they work on sequences or scalars might also be useful.  
  
Should I create a PR for that ? I mean for the SOURCE_BROWSER and `Algorithm` section ?

---

## Comment 2

> Username: ldionne  
> Created at: 2016-01-04 16:23:07 UTC  
> Url: https://github.com/boostorg/hana/issues/233#issuecomment-168722770  

Regarding the `Algorithm` section, this is #82. I'm looking at the other issues/suggestions right now.

---

## Comment 3

> Username: m-j-w  
> Created at: 2016-01-05 13:35:44 UTC  
> Url: https://github.com/boostorg/hana/issues/233#issuecomment-168999272  

For `Algorithms` I got the following result in Reference Manual when using the above `@ingroup`. So it appears they are appearing in both their own group 'Algorithms' and in the group 'Concepts' where they are related to something. (I did just add the `@ingroup` to some algorithms, apparently.)  
  
![hana_index](https://cloud.githubusercontent.com/assets/14040010/12116314/4177a0c0-b3b9-11e5-9cd7-359ec4cc43f8.png)

---

## Comment 4

> Username: m-j-w  
> Created at: 2016-01-05 13:51:04 UTC  
> Url: https://github.com/boostorg/hana/issues/233#issuecomment-169005703  

With respect to the Alphabetical Index, it appears that one is just not enabled for HTML, yet. Setting  
  
```  
ALPHABETICAL_INDEX      = YES  
COLS_IN_ALPHA_INDEX     = 5         ('5' is the default, probably unnecessary to change)  
....  
DISABLE_INDEX           = NO  
```  
  
solves the issue.  
  
Last, for the source code browsing, I also added locally the strip comments flag, which makes it a bit more easy to read.  
  
```  
SOURCE_BROWSER          = YES  
STRIP_CODE_COMMENTS     = YES  
```

---

## Comment 5

> Username: ldionne  
> Created at: 2016-01-06 18:34:56 UTC  
> Url: https://github.com/boostorg/hana/issues/233#issuecomment-169413076  

Strangely, changing the above does not fix the issue of the alphabetical index for me. The index is still there, but it does not appear on a single page.  
  
Also, the `DISABLE_INDEX` option of Doxygen controls whether tabs appear at the top of the documentation; it is unrelated to the alphabetical index. I don't want tabs to appear at the top of the documentation, which is why I'd rather leave it disabled.  
  
Regarding code browsing, I'm not sure how useful that is. Indeed, the implementation is not in the same file as the documentation, so clicking the link to see a definition brings us to the `fwd/` header. But there's nothing in these headers that's not already covered by the documentation. Also, even if we were to see the actual implementation of things, I think it would be better to leave it out. Implementations are often hairy, and someone that truly wants to see how it works under the hood just has to go to the header files.

---

## Comment 6

> Username: ldionne  
> Created at: 2016-01-12 16:16:30 UTC  
> Url: https://github.com/boostorg/hana/issues/233#issuecomment-170961745  

Note that the issue with namespaces:  
  
> Lastly, the namespaces seem not to be correctly recognized under Headers. See for example http://boostorg.github.io/hana/accessors_8hpp.html where it says boost for Namespace and links somewhere not really reasonable.  
  
was fixed in ed308613a89f13460a070dd600d1c87acfa5b074.

---

## Comment 7

> Username: ldionne  
> Created at: 2016-01-12 16:21:20 UTC  
> Url: https://github.com/boostorg/hana/issues/233#issuecomment-170963196  

So there remains the proposal to enable the source code browser. Like I said, I'm not a fan of enabling it, as I think it adds little value and adds room for confusion. Like I said, also, it will link to the `fwd/` header instead of the actual definition of the function. Sometimes, there are even multiple definitions in different files (such as for `is_empty`, which is implemented by every `Iterable`). How should the source code browser handle that?  
  
My opinion here is that it's a reference documentation, and one should not need to look at the implementation to figure out what's going on. If one has to do this, the documentation needs to be improved. For those that want to learn about implementation techniques, looking at the source code itself is probably easier.

---

## Comment 8

> Username: ldionne  
> Created at: 2016-01-12 16:49:31 UTC  
> Url: https://github.com/boostorg/hana/issues/233#issuecomment-170971755  

It turns out that 6f1d408 does not address the issue with the Alphabetical index properly. Indeed, only functions documented as **member functions** appear there. I created separate issue #238 to address this.

---

## Comment 9

> Username: m-j-w  
> Created at: 2016-01-12 17:18:19 UTC  
> Url: https://github.com/boostorg/hana/issues/233#issuecomment-170980315  

Regarding source browsing: I'm fine with not enabling that. It's just one or two options in the local Doxyfile.in to modify manually. And you're quite right about the confusing links. I tried fiddling with some other Doxygen tags to overcome that, but the source code would then probably have something like 50% of Doxygen bloat. Every single function, overload and specialization would need to be tagged properly. That's clearly neither viable nor desirable.  
  
Thanks for those fixes !

---

## Comment 10

> Username: ldionne  
> Created at: 2016-01-12 17:54:54 UTC  
> Url: https://github.com/boostorg/hana/issues/233#issuecomment-170993143  

Alright, closing this then. We'll follow up in #238  and #82.
