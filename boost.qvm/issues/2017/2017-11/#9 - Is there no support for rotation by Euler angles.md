# #9 - Is there no support for rotation by Euler angles? [Closed]

> Username: agate-pris  
> Created at: 2017-11-28 16:43:25 UTC  
> Updated at: 2022-02-16 22:36:17 UTC  
> Closed at: 2018-03-15 06:13:48 UTC  
> Url: https://github.com/boostorg/qvm/issues/9  

Boost.QVM does not seem to support rotation by Euler angles.  
  
I think that following overload should be added.  
  
* `rot_mat` by Euler angles  
* `set_rot` for matrix type by Euler angles  
* `rotate` for matrix type by Euler angles  
* `rot_quat` by Euler angles  
* `set_rot` for quaternion type by Euler angles  
* `rotate` for quaternion type by Euler angles  
  
Function template `euler_angles` should be added that convert quaternion to 3-dimensional vector type. It probably use `zero_magnitude_error`.  
  
If you like, I will fork, implement it and send a pull request.

---

## Comment 1

> Username: zajo  
> Created at: 2018-03-15 06:13:48 UTC  
> Url: https://github.com/boostorg/qvm/issues/9#issuecomment-373271729  

I've implemented this for matrices, both for Tait–Bryan angles and Euler angles.

---

## Comment 2

> Username: agate-pris  
> Created at: 2018-03-15 10:02:15 UTC  
> Url: https://github.com/boostorg/qvm/issues/9#issuecomment-373322903  

Thank you.

---

## Comment 3

> Username: ianjosephfischer  
> Created at: 2022-02-16 19:23:22 UTC  
> Url: https://github.com/boostorg/qvm/issues/9#issuecomment-1042073337  

Is this method implemented. I would love a euler_angles method or is it named something else?

---

## Comment 4

> Username: zajo  
> Created at: 2022-02-16 20:47:49 UTC  
> Url: https://github.com/boostorg/qvm/issues/9#issuecomment-1042296448  

See:  
  
- https://boostorg.github.io/qvm/#rot_mat  
- https://boostorg.github.io/qvm/#mat_set_rot  
- https://boostorg.github.io/qvm/#mat_rotate

---

## Comment 5

> Username: ianjosephfischer  
> Created at: 2022-02-16 22:05:50 UTC  
> Updated at: 2022-02-16 22:06:44 UTC  
> Url: https://github.com/boostorg/qvm/issues/9#issuecomment-1042356337  

Thank you for the response, but was hoping to either extract the euler angles from a matrix or a quaternion.  
These methods seems to only apply euler angles to the matrices.

---

## Comment 6

> Username: zajo  
> Created at: 2022-02-16 22:36:17 UTC  
> Url: https://github.com/boostorg/qvm/issues/9#issuecomment-1042377116  

Right, that's what this issue was about. There is a conversion of matrix to a quaternion, if that helps: https://boostorg.github.io/qvm/#quat_convert_to.
