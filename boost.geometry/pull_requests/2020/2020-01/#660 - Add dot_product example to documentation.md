# #660 Add dot_product example to documentation [Merged]

> Username: adityamohan29  
> Created at: 2020-01-20 17:46:19 UTC  
> Updated at: 2020-03-16 13:03:13 UTC  
> Merged at: 2020-03-16 12:58:51 UTC  
> Closed at: 2020-03-16 12:58:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/660  

Proposed above is the example of a dot product arithmetic operation involving different point concepts as operands. This aims to aid the user in understanding the concept more clearly when the user goes through the documentation.  
  
### References  
  
- #658

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2020-01-20 18:07:42 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/660#pullrequestreview-345469867  

It would be helpful if you could add to the commit message line  
  
```  
Closes #658  
```

---

## Review 2 [Commented]

> Username: vissarion  
> Created_at: 2020-01-21 08:59:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/660#pullrequestreview-345716472  

Thanks for the PR.  
You can add copyright info in the beginning of the file e.g.  
```  
// Boost.Geometry  
// QuickBook Example  
  
// Copyright (c) 2020, **your-name**  
  
// Use, modification and distribution is subject to the Boost Software License,  
// Version 1.0. (See accompanying file LICENSE_1_0.txt or copy at  
// http://www.boost.org/LICENSE_1_0.txt)  
```  
Also you can add `doxygen` comments, see https://github.com/boostorg/geometry/blob/develop/doc/src/examples/algorithms/densify.cpp

📁 doc/src/examples/dot_product

```diff
  31 |+  	
  32 |+  	boost::array<double, 2> a =  {1, 2};
  33 |+   boost::array<double, 2> b =  {2, 3};
```

> Username: vissarion  
> Created_at: 2020-01-21 08:59:32 UTC  
> Updated_at: 2020-03-13 04:14:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/660#discussion_r368876752  

tab is missing

> Username: mloskot  
> Created_at: 2020-01-21 09:10:55 UTC  
> Updated_at: 2020-03-13 04:14:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/660#discussion_r368881963  

More precisely, 4 spaces as per https://github.com/boostorg/geometry/wiki/Guidelines-for-Developers#code-formatting-and-indentation

---

📁 doc/src/examples/dot_product

```diff
  33 |+   boost::array<double, 2> b =  {2, 3};
  34 |+     
  35 |+   dp3 = bg::dot_product(a, b);
```

> Username: vissarion  
> Created_at: 2020-01-21 08:59:41 UTC  
> Updated_at: 2020-03-13 04:14:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/660#discussion_r368876823  

tab

> Username: mloskot  
> Created_at: 2020-01-21 09:10:57 UTC  
> Updated_at: 2020-03-13 04:14:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/660#discussion_r368881978  

More precisely, 4 spaces as per https://github.com/boostorg/geometry/wiki/Guidelines-for-Developers#code-formatting-and-indentation


---

## Comment 3

> Username: adityamohan29  
> Created_at: 2020-01-21 09:14:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/660#issuecomment-576588605  

> It would be helpful if you could add to the commit message line  
>   
> ```  
> Closes #658  
> ```  
  
Can you please elaborate?

---

## Comment 4

> Username: mloskot  
> Created_at: 2020-01-21 10:03:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/660#issuecomment-576607637  

@spacewafer I suggested to make the commit message to read  
  
```  
A dot_product example for the documentation  
  
Closes #658  
```  
  
Then, GitHub will properly auto-link this PR with the issue this PR targets, it will also close the issue when the PR is merged. This is helpful when browsing the GitHub issues archive as well as when collecting release notes.  
  
Nevermind, this can be done during "Squash and merge".

---

## Comment 5

> Username: awulkiew  
> Created_at: 2020-02-13 11:18:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/660#issuecomment-585676947  

Something wierd is happening here. :)

---

## Comment 6

> Username: adityamohan29  
> Created_at: 2020-02-29 10:24:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/660#issuecomment-592929987  

> Something wierd is happening here. :)  
  
Can you please elaborate on this?

---

## Comment 7

> Username: adityamohan29  
> Created_at: 2020-02-29 10:36:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/660#issuecomment-592930909  

> > Something wierd is happening here. :)  
>   
> Can you please elaborate on this?  
  
I hadn't added Doxygen commands before. Does this seem right now?

---

## Comment 8

> Username: vissarion  
> Created_at: 2020-03-03 14:04:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/660#issuecomment-593966698  

Similarly to https://github.com/boostorg/geometry/pull/665 you have to verify that you can produce the documentation locally

---

## Review 9 [Changes requested]

> Username: mloskot  
> Created_at: 2020-03-05 11:07:44 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/660#pullrequestreview-369480333  

@spacewafer  `dot_product` should be named `dot_product.cpp`, no? Why not in ` `doc/src/examples/arithmetic/` as in #665?  
  
FYI, next time you update your PR, then documentation and `doc/src/examples` will be built and verified with GitHub Actions.

---

## Review 10 [Changes requested]

> Username: mloskot  
> Created_at: 2020-03-12 23:38:28 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/660#pullrequestreview-373966065  

I have only one request to delete the garbage file.  
  
I checked the documentation output and it looks good to me. Good work!

📁 doc/src/examples/dot_product

```diff
   1 |+ // Boost.Geometry
```

> Username: mloskot  
> Created_at: 2020-03-12 23:37:14 UTC  
> Updated_at: 2020-03-13 04:14:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/660#discussion_r391959549  

Please, remove this extension-less file `doc/src/examples/dot_product` which seems to be copy of the correct one `doc/src/examples/arithmetic/dot_product.cpp`.


---

## Review 11 [Approved]

> Username: mloskot  
> Created_at: 2020-03-13 08:54:58 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/660#pullrequestreview-374133813  

This one also looks good to me.  
Once merged, I am ready to apply any tweaks and improvements if necessary.  
@vissarion since you also reviewed it previously, do you have any requests, any objects to merge?

---

## Review 12 [Approved]

> Username: vissarion  
> Created_at: 2020-03-16 12:58:26 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/660#pullrequestreview-375188828  

Looks ok to me too, thanks!

---
