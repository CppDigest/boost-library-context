# #48 - Clarify order of application for Euler angles in documentation. [Closed]

> Username: dwmckee  
> Created at: 2024-01-30 23:21:51 UTC  
> Updated at: 2024-02-04 21:03:17 UTC  
> Closed at: 2024-02-04 21:03:17 UTC  
> Url: https://github.com/boostorg/qvm/issues/48  

In the context of constructing rotation matrices from Euler (or Tait-Bryan) angles, there is an ambiguity in the order that angles are encountered between the verbal description of the process and the mathematical notation for composition. When speaking in English one might say _"we apply Rz then Ry then Rx"_ (so that the angles are encountered in order zyx), while the same process written mathematically appears as `R_total = Rx Ry Rz` (with the angles are encountered in order xyz).  
  
It appears that the names of the related QVM templates (`rot_mat_xyz`, etc) use the latter order, but this is not documented leaving room for confusion.

---

## Comment 1

> Username: zajo  
> Created at: 2024-01-30 23:34:03 UTC  
> Url: https://github.com/boostorg/qvm/issues/48#issuecomment-1918091422  

It is consistent with the way the matrix operations are applied, e.g. rot_mat_xyz<3>(x, y, z) is equivalent to rotx_mat<3>(x) * roty_mat<3>(y) * rotz_mat<3>(z).  
  
Note that if you have two matrices m1 and m2, semantically the matrix m = m1 * m2 applies the m2 transform before the m1 transform.
