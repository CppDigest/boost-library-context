# #706 - Consider moving channel_convert_to_unsigned out of detail namespace [Open]

> Username: mloskot  
> Created at: 2022-07-04 18:50:04 UTC  
> Updated at: 2024-08-20 13:02:30 UTC  
> Url: https://github.com/boostorg/gil/issues/706  

From @marco-langer https://github.com/boostorg/gil/pull/703#issuecomment-1172915455  
  
> Regarding this channel_convert_to_unsigned, which is now in the detail namespace, but also still referenced in this tutorial:  
>  
> Maybe one should think to pull it out of the detail namespace again, if it is that useful for client code? I don't know the rational why it was moved into the detail namespace at all (this was done 15 years ago!).  
  
I think, it is worth to keep this discussion open and come back to it in future.  
  
----  
  
For the records, it moved into `detail` in https://github.com/boostorg/gil/commit/2ad6747dd0472cef7c67b3438b847f347ce45212  
which refers to the "GIL 2.0 to GIL 2.1 Changes" PDF. Since I have got this [gil2.1_changes.pdf](https://github.com/boostorg/gil/files/9041592/gil2.1_changes.pdf), I've attached it for the reference. It does not explain the move rationale though.  
  
I attached also earlier [gil2.0_changes.pdf](https://github.com/boostorg/gil/files/9041602/gil2.0_changes.pdf) which lists lots of changes related to Boost'ification of the Adobe GIL library. This makes me guess that moving of the types into the `detail` namespace may be related to the unification of GIL according to Boost conventions.  
  
(both legacy documents available at https://github.com/boostorg/gil/wiki/History)
