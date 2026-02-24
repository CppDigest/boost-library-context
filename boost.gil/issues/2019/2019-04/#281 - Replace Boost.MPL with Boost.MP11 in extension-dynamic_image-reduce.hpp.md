# #281 - Replace Boost.MPL with Boost.MP11 in extension/dynamic_image/reduce.hpp [Closed]

> Username: mloskot  
> Created at: 2019-04-14 20:11:04 UTC  
> Updated at: 2020-03-24 23:11:35 UTC  
> Closed at: 2020-03-24 23:11:35 UTC  
> Url: https://github.com/boostorg/gil/issues/281  

Additional task related to #274 to either switch the `extension/dynamic_image/reduce.hpp` to Boost.MP11 and C++11 or to remove it completely.  
  
Currently, it is compiled on demand if `#define BOOST_GIL_REDUCE_CODE_BLOAT=1` and no `Jamfile` or `CMakeLists.txt` define it.  
  
Possibly, it's never been used by anyone - any users of this feature, please, speak up now!  
  
/cc @chhenning @stefanseefeld @lpranam   
  
### References  
  
- [Efficient Run-Time Dispatching in Generic Programming with Minimal Code Bloat](http://lubomir.org/academic/MinimizingCodeBloat.pdf) by Lubomir Bourdev, Jaakko Jarvi - as found in https://github.com/boostorg/gil/issues/281#issuecomment-601240925 discusses the features of the `reduce.hpp`

---

## Comment 1

> Username: lpranam  
> Created at: 2020-03-18 19:56:16 UTC  
> Url: https://github.com/boostorg/gil/issues/281#issuecomment-600829413  

I would like to vote in favour of removing this header file first reason is as you mentioned it is compiled on demand and the second reason would it is actually not mentioned anywhere in the official GIL(at least I could not find it anywhere) documentation so there are fewer chances of people using it.

---

## Comment 2

> Username: mloskot  
> Created at: 2020-03-18 21:14:16 UTC  
> Url: https://github.com/boostorg/gil/issues/281#issuecomment-600861887  

TL;TR: Yes, I'm inclined to remove it. If anyone arrives asking for it, it can be recovered from the history, but given it is 1) unused, 2) seems unfinished and 3) purpose is unclear, I don't think we can promise any reliable support for it.  
  
/cc @stefanseefeld   
  
------  
  
I have no idea what was the exact rationale and purpose of this code, but I can only _deduce_ it was added to workaround some MPL limitations or issues:  
  
https://github.com/boostorg/gil/blob/78b7dcfeca5f01cb51192b5e45c9c535bc912ace/include/boost/gil/extension/dynamic_image/reduce.hpp#L41  
  
https://github.com/boostorg/gil/blob/78b7dcfeca5f01cb51192b5e45c9c535bc912ace/include/boost/gil/extension/dynamic_image/reduce.hpp#L68  
  
and then there are some algorithms to reduce elements of type lists (elements of any image/view variants)  
  
https://github.com/boostorg/gil/blob/78b7dcfeca5f01cb51192b5e45c9c535bc912ace/include/boost/gil/extension/dynamic_image/reduce.hpp#L68  
  
based on reducibility and other properties of color bases of the views/images:  
  
https://github.com/boostorg/gil/blob/78b7dcfeca5f01cb51192b5e45c9c535bc912ace/include/boost/gil/extension/dynamic_image/reduce.hpp#L521  
  
The reduction seems like composing an image/view type common to all types of variant image/view (e.g. find image type that all the possible types can be converted into, and back to original type, without any losses, perhaps?)  
  
However, large chunks of the `reduce.hpp` are commented, so it feels like a work in progress.

---

## Comment 3

> Username: sdebionne  
> Created at: 2020-03-19 15:23:25 UTC  
> Url: https://github.com/boostorg/gil/issues/281#issuecomment-601240925  

For the records, [this academic paper](http://lubomir.org/academic/MinimizingCodeBloat.pdf) explains the how and the why of `reduce.hpp`. Personally, I have never used `BOOST_GIL_REDUCE_CODE_BLOAT=1`. Does it really reduce code bloat with modern compilers?

---

## Comment 4

> Username: mloskot  
> Created at: 2020-03-19 20:53:45 UTC  
> Updated at: 2020-03-19 21:17:00 UTC  
> Url: https://github.com/boostorg/gil/issues/281#issuecomment-601411680  

@sdebionne What a finding, thank you! I archived the reference in d664a4a582cdd06e7ddb4fe04ab092d33ead3265  
  
### From MinimizingCodeBloat.pdf  
  
> Our **test bed**, GIL, is deployed in a real world industrial setting (...)  
  
That could explain undocumented and untested or experimental status of the `reduce.hpp` :)  
  
> 3.2 Dynamic dispatching in GIL  
> (...)  
> 3.3 Template bloat originating from GIL’s dynamic dispatching  
> (...)  
> 4. Reducing the Number of Instantiations  
 >Our strategy for reducing the number of instantiations is based on decomposing a complex type into a set of orthogonal parameter dimensions (such as color space, channel depth, constness) and identifying which dimensions are important for a given operation.  
> (...)  
> We call this process type reduction.  
> Type reduction is implemented with metafunctions which map a given data type and a particular algorithm to the class representative of that data type for the given algorithm.  
  
I think it, especially the section titles, makes the role of the `reduce.hpp`  clearer now.   
  
### Conclusion  
  
Since there is the _"type reductions for GIL image types"_ are not used anywhere in the current GIL and there seem to be no one interested in picking it up to verify the technique for the modern compilers (newer than VS2005 (8.0) or GCC 4), I'm also voting to remove it completely (as @lpranam in https://github.com/boostorg/gil/issues/281#issuecomment-600829413).  
  
If no objections arrive soon from @stefanseefeld, @chhenning or anyone else active/interested in GIL, then let's remove.

---

## Comment 5

> Username: mloskot  
> Created at: 2020-03-24 19:59:15 UTC  
> Url: https://github.com/boostorg/gil/issues/281#issuecomment-603474488  

Anyone fancy a PR removing the `reduce.hpp`?
