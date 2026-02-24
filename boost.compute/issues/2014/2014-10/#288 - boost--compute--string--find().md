# #288 - boost::compute::string::find() [Closed]

> Username: Mageswaran1989  
> Created at: 2014-10-30 07:25:48 UTC  
> Updated at: 2014-11-05 15:57:34 UTC  
> Closed at: 2014-11-05 15:57:34 UTC  
> Url: https://github.com/boostorg/compute/issues/288  

# When I try following snippet:  
  
compute::string bc_str = "boost::compute::string";  
    std::size_t found_pos = bc_str.find('c');  
    std::cout << "c found at pos   : " << found_pos <<"\n";  
# I get following error:  
  
Boost.Compute: kernel compilation failed (-11)  
--- source ---  
# define boost_pair_type(t1, t2) _pair_ ## t1 ## _ ## t2 ## _t  
# define boost_pair_get(x, n) (n == 0 ? x.first ## x.second)  
# define boost_make_pair(t1, x, t2, y) (boost_pair_type(t1, t2)) { x, y }  
# define boost_tuple_get(x, n) (x.v ## n)  
  
__kernel void find_if(__global int\* index, __global char\* _buf0)  
{  
const uint i = get_global_id(0);  
const char value=_buf0[i];  
if(value==c){  
    atomic_min(index, i);  
}  
  
}  
  
--- build log ---  
:11:11: error: use of undeclared identifier 'c'  
if(value==c){  
          ^  
  
terminate called after throwing an instance of 'boost::exception_detail::clone_implboost::exception_detail::error_info_injector<boost::compute::opencl_error >'  
  what():  Build Program Failure  
Aborted (core dumped).  
# If I am right, the find_if() i.e find_if_with_atomics() kernel is not considering the char type. Isn't it?

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-11-05 15:57:34 UTC  
> Url: https://github.com/boostorg/compute/issues/288#issuecomment-61829560  

Fixed in PR #295.
