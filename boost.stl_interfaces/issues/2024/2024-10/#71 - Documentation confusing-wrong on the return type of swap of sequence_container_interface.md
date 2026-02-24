# #71 - Documentation confusing/wrong on the return type of swap of sequence_container_interface [Closed]

> Username: thebrandre  
> Created at: 2024-10-11 05:55:06 UTC  
> Updated at: 2024-10-21 23:37:32 UTC  
> Closed at: 2024-10-21 23:37:32 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/71  

The documentation states that the retun type of the user-provided swap should be convertible to bool, see [here](https://www.boost.org/doc/libs/1_86_0/doc/html/boost_stlinterfaces/tutorial___sequence_container_interface_.html#boost_stlinterfaces.tutorial___sequence_container_interface_.container) or [here](https://github.com/boostorg/stl_interfaces/blob/develop/doc/tutorial.qbk#L653).  
  
This would be rather unusual and seems not to be tha case. It is also inconsistent with the docs.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-10-21 23:12:57 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/71#issuecomment-2427902666  

Yup, totally wrong.  Thanks for reporting this!
