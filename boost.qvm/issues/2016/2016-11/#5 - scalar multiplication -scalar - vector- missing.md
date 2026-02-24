# #5 - scalar multiplication "scalar * vector" missing [Closed]

> Username: federkamm  
> Created at: 2016-11-03 12:50:12 UTC  
> Updated at: 2016-11-04 03:37:36 UTC  
> Closed at: 2016-11-04 03:37:36 UTC  
> Url: https://github.com/boostorg/qvm/issues/5  

It is possible to scale a vector using  
  
    vector * scalar  
  
but not using  
  
    scalar * vector  
  
Is there a rationale for the missing "scalar * vector" operator?  Beside me prefering to write the scalar in front of the vector it is also more efficient in regard of the evaluation order without parantesis  
  
    scalar1 * scalar2 * vector == (scalar1 * scalar2) * vector  
  
evaluates in optimal order (n+1 flops where n is the dimension of vector).  But  
  
    vector * scalar1 * scalar 2 == (vector * scalar1) * scalar2  
  
causes extra evaluations (2n flops) and one is forced to write parantesis for an optimal evaluation order  
  
    vector * (scalar1 * scalar2)   [ == scalar1 * scalar2 * vector ]  
    vector * (scalar1 / scalar2)   [ == scalar1 / scalar2 * vector ]  
  
Please consider adding the "scalar * vector" operator.

---

## Comment 1

> Username: zajo  
> Created at: 2016-11-04 03:37:36 UTC  
> Url: https://github.com/boostorg/qvm/issues/5#issuecomment-258337712  

Done, see latest develop branch.
