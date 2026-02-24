# #386 Piecewise [Open]

> Username: ryanelandt  
> Created at: 2023-06-13 20:45:00 UTC  
> Updated at: 2023-06-16 16:16:32 UTC  
> Url: https://github.com/boostorg/histogram/pull/386  

[EDITED: to better distinguish between bin space Y and bin indices, especially in the artwork.]  
  
This PR hopes to eventually close #47, adding a piecewise axis. The structure of this piecewise axis implementation is different from the others. I'm hoping to get feedback on it. Changes to this PR, and supporting PRs will likely be needed before this PR can be merged in.   
  
### Solution overview:  
  
This 1D piecewise axis implementation has two layers. The first layer has to do with creating immutable pieces that transform from input space X to bin space Y. The second layer converts from bin space Y to bin indices to create an axis. If axis growth is allowed, this second layer is mutable. I explain how this works on a high level.  
  
## Layer 1: Pieces  
  
Suppose we want to divide the input space X into 4 bins of unit spacing starting at x=3. Values between y= [0, 1) are associated with bin 0.  
```  
   y=0             y=4  
    |---|---|---|---|  
   x=3             x=7  
```  
This piece is described by three pieces of information  
  - forward transformation: y = f(x) = x - 3  
  - inverse transformation: x = f⁻¹(y) = y + 3  
  - number of bins: N = 4  
  
There are five supported piece types:  
  1. unit      (bin spacing is 1)  
  2. uniform   (bin spacing is constant)  
  3. multiply  (bin spacing is multiplied by a constant)  
  4. add       (bin spacing is added to by a constant)  
  5. variable (bin bounds are user supplied points)  
  
**Note:** Pieces are similar to the existing transformation functionality in the sqrt and log transformations of regular axis. The difference is that pieces apply offsets and scaling.   
  
### Combining pieces  
  
Suppose we wanted to combine the following two pieces.  
```  
     y=0             y=4          y=4                 y=9  
      |---|---|---|---|            |-|--|---|----|-----|  
     x=3             x=7          x=9                x=15  
         Unit spacing                   Add spacing  
```  
Typically one wouldn't want to combine two pieces with a gap between them, but this possibility is supported by the implementation. A piecewise transformation stores each of these unmodified pieces internally. The external behavior is that of piecewise transformation from y=0 to y=9 with a gap where y=nan.  
```  
     y=0             y=4 y=nan y=4                 y=9  
      |---|---|---|---|         |-|--|---|----|-----|  
     x=3             x=7       x=9                x=15  
```  
  
## Layer 2: Axis behavior  
### Bin shifting:  
  
Suppose we wanted to use the piecewise transformation above in a growable axis. This axis starts off with 9 bins (i.e., y=0 through y=9). Adding bins means shifting the input to the transformation. It also means expanding its size. Shifting is needed with underflow or overflow bins are added. Underflow bins are added below x=3. Overflow bins are added above x=15. Adding bins between x=7 and x=9 is not supported. The mutable piece `x_bin_shift` accumulates:  
  - added underflow bins, and  
  - added overflow bins.  
   
Using this information, x_bin_shift augments the behavior of the contained piecewise transformation as follows. The size (i.e., number of bins) of the piece is increased by the number of underflow and overflow bins. The output space (i.e., Y axis) is shifted by the number of underflow bins in the forward and inverse calculations.  
  
### Linear axis:  
  
Wrapping the, possibly shifted, piece in an `int_resolver_linear` makes the piece an axis. This linear int resolver implements an index method which converts from bin space Y to bin indices. Values in output space Y are converted to bin indices according to the existing machinery. For example, nan Y values are placed in the overflow bin.  
  
### Circular axis:  
  
Suppose we wanted to make the above described piecewise transformation circular with a relevant period x=3 to x=23. This piece is copied below for convenience.  
```  
     y=0             y=4 y=nan y=4                 y=9  
      |---|---|---|---|         |-|--|---|----|-----|  
     x=3             x=7       x=9                x=15  
```  
To do this, we wrap the piece in an `int_resolver_circular` with the bounds x=3 and x=23. This circular resolver augments the behavior of the contained piece. The input (i.e., the X axis) is wrapped to the domain x=3 to x=23 before being given to the contained piece This circular int resolver makes the transformation an axis by converting from bin space Y to bin indices.

---

## Comment 1

> Username: HDembinski  
> Created_at: 2023-06-15 08:52:50 UTC  
> Url: https://github.com/boostorg/histogram/pull/386#issuecomment-1592637173  

Another interesting patch. I had a quick first glance and some first comments.  
  
- The int_resolver should be a separate PR. Such a functionality should be integrated into the existing regular axis as a compile-time option.  
   - I would like to see benchmark data with and without this enabled. I hesitated from implementing this because it will make regular slower, but that's not an issue if it is a compile-time option.  
- piece should be a separate PR, but I am inclined to reject it because it looks like it reproduces behavior which is already in the existing axes.  
- piecewise should not be a completely new axis, it should allow you to piece together the existing axes, like regular, etc., for example  
```c++  
// in C++17  
auto pw1 = axis::piecewise(axis::regular(10, 0, 3), axis::regular(5, 3, 6));  
auto pw2 = axis::piecewise(axis::regular(10, 0, 3), axis::variable({3, 3.5, 6}));  
```  
The `index` function of `axis::piecewise` should check whether the argument is in the interval covered by any of the passed axis objects or in the gap inbetween. If it is inside the corresponding axis, it calls the index function of the subaxis and adds an appropriate offset if that is not the first axis. It should do the minimum amount of work and delegate as much as possible to the subaxis objects.  
  
The gaps between axes should be handled like a single overflow bin to remain compatible with the existing machinery. This generic overflow bin which contains everything that falls outside of any of the defined axes. If there are no gaps between subaxis objects, we could support an underflow and overflow bin with the standard meaning.

---

## Comment 2

> Username: ryanelandt  
> Created_at: 2023-06-16 16:16:32 UTC  
> Url: https://github.com/boostorg/histogram/pull/386#issuecomment-1594934322  

Integrating int_resolver functionality into regular axis seems like a good idea for a first step. To do this, I added PR #387. This PR eliminates some of the precision issues with regular axis. It includes a before and after indexing benchmark.  
  
My initial post for this PR (#386) did not clearly communicate that gaps between axes are handled with a single overflow bin. I’ve edited it to correct this.

---
