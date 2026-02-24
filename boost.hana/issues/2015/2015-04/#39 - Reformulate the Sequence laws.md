# #39 - Reformulate the Sequence laws [Closed]

> Username: ldionne  
> Created at: 2015-04-09 13:32:39 UTC  
> Updated at: 2015-07-28 05:34:00 UTC  
> Closed at: 2015-07-28 05:34:00 UTC  
> Url: https://github.com/boostorg/hana/issues/39  

The current `Sequence` laws (as introduced in 55e4a1d) are pretty bold. The relation between being the "unique natural isomorphism between Tuple and S" and being "equivalent to Tuple" is really not as clear as I make it out. Actually, this is probably false if we are not more precise.  
  
The laws as currently written will at least convey the intuition that `Sequence`s are _the same as_ `Tuple`s, which is currently true. However, another question is whether we actually want this, since it precludes any runtime or homogeneous sequence from modelling that concept.
