# #233 - Is there a identity_view? [Closed]

> Username: denzor200  
> Created at: 2021-09-24 21:45:02 UTC  
> Updated at: 2022-01-16 05:36:28 UTC  
> Closed at: 2022-01-16 05:36:27 UTC  
> Url: https://github.com/boostorg/fusion/issues/233  

Hi. I need a simple wrapper that doesn't have any side effects on the wrapped sequence. Does this library support it? Or is there a shortest way to implement it? To implement >20 tag dispatched functions - is not a short way:(  
  
What do i mean..  
  
For example:  
```  
vector<int, std::string> v(12, "Hello world");  
identity_view<vector<int, float>> view(v);  
std::cout << at_c<0>(view) << std::endl; // Will print 12  
std::cout << at_c<1>(view) << std::endl; // Will print Hello world  
```  
It's just example for a vector but i expect that identity_view will work with any valid fusion sequence.

---

## Comment 1

> Username: djowel  
> Created at: 2021-09-25 01:36:34 UTC  
> Url: https://github.com/boostorg/fusion/issues/233#issuecomment-926990554  

No, there is no `identity_view`. Would you like to contribute one?

---

## Comment 2

> Username: djowel  
> Created at: 2021-09-25 04:08:14 UTC  
> Url: https://github.com/boostorg/fusion/issues/233#issuecomment-927006839  

The easiest way is to use `transform_view`.

---

## Comment 3

> Username: denzor200  
> Created at: 2021-09-25 12:03:50 UTC  
> Url: https://github.com/boostorg/fusion/issues/233#issuecomment-927111538  

Yes, `transform_view` is a good idea. But it's view has no `at_key_impl`, `key_of_impl`, and so others for associative sequence.  
Respectively, if we get `boost::fusion::map` and wrap it into `transform_view`, then our map will lose its associative character.  
  
Is that intended, or is this a flaw on the `transform_view` side?

---

## Comment 4

> Username: denzor200  
> Created at: 2021-09-25 21:18:41 UTC  
> Url: https://github.com/boostorg/fusion/issues/233#issuecomment-927184185  

@djowel I implemented this in a rather cheeky way :smile:  
https://godbolt.org/z/3qn4x8ns5

---

## Comment 5

> Username: djowel  
> Created at: 2021-09-26 00:30:49 UTC  
> Url: https://github.com/boostorg/fusion/issues/233#issuecomment-927203700  

> Is that intended, or is this a flaw on the `transform_view` side?  
  
It is not documented (*), but the ideal behavior would be that views should inherit the properties of its subjects. Again, PR would be great. It seems you have familiarity with fusion.  
  
(* https://www.boost.org/doc/libs/1_65_1/libs/fusion/doc/html/fusion/view.html)

---

## Comment 6

> Username: denzor200  
> Created at: 2021-09-26 17:17:13 UTC  
> Updated at: 2021-09-26 17:23:14 UTC  
> Url: https://github.com/boostorg/fusion/issues/233#issuecomment-927339813  

> Again, PR would be great. It seems you have familiarity with fusion.  
  
PR in process.. I started with the tests. The way I see it:  
https://godbolt.org/z/3Y93n3dvd  
  
I propose to make a set of generic tests that will be check any `Sequence` for compliance with the `Concept`.  
Now in the fusion, view tests are absolutely not scalable:(

---

## Comment 7

> Username: denzor200  
> Created at: 2021-09-26 17:21:57 UTC  
> Updated at: 2021-09-26 17:22:49 UTC  
> Url: https://github.com/boostorg/fusion/issues/233#issuecomment-927340457  

My example above show already implemented  test for `ForwardSequence` concept.  
`filter_view` passed this test good. But the current `filter_view` will fail test for `AssociativeSequence` concept, i suppose

---

## Comment 8

> Username: denzor200  
> Created at: 2022-01-16 05:36:27 UTC  
> Url: https://github.com/boostorg/fusion/issues/233#issuecomment-1013814835  

https://github.com/boostorg/fusion/commit/13593be8070707ac85b93b15a6e3adeca53c3f6d
