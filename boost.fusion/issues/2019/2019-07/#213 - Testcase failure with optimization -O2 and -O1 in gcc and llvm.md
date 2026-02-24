# #213 - Testcase failure with optimization -O2 and -O1 in gcc and llvm [Closed]

> Username: Jac1494  
> Created at: 2019-07-25 12:29:13 UTC  
> Updated at: 2019-07-29 06:48:32 UTC  
> Closed at: 2019-07-29 06:48:32 UTC  
> Url: https://github.com/boostorg/fusion/issues/213  

Hi,  
The below testcase fails when compiled with optimization.  
https://github.com/boostorg/fusion/blob/develop/test/functional/invoke.cpp#L391  
https://github.com/boostorg/fusion/blob/develop/test/functional/invoke.cpp#L401  
  
like,  
$g++ -O2 -I./boost_1_70_0 boost_1_70_0/libs/fusion/test/functional/invoke.cpp  
$./a.out  
  
./boost_1_70_0/libs/fusion/test/functional/invoke.cpp(391): test  
'that.data == fusion::invoke(& members::data,  
fusion::join(sv_obj_c_ctx,seq))' failed in function 'void  
test_sequence_n(Sequence&, mpl_::int_<0>) [with Sequence =  
boost::fusion::vector<>]'  
  
./boost_1_70_0/libs/fusion/test/functional/invoke.cpp(401): test  
'that.data == fusion::invoke(& members::data,  
fusion::join(sv_obj_c_d_ctx,seq))' failed in function 'void  
test_sequence_n(Sequence&, mpl_::int_<0>) [with Sequence =  
boost::fusion::vector<>]'  
  
./boost_1_70_0/libs/fusion/test/functional/invoke.cpp(391): test  
'that.data == fusion::invoke(& members::data,  
fusion::join(sv_obj_c_ctx,seq))' failed in function 'void  
test_sequence_n(Sequence&, mpl_::int_<0>) [with Sequence =  
boost::fusion::list<>]'  
  
./boost_1_70_0/libs/fusion/test/functional/invoke.cpp(401): test  
'that.data == fusion::invoke(& members::data,  
fusion::join(sv_obj_c_d_ctx,seq))' failed in function 'void  
test_sequence_n(Sequence&, mpl_::int_<0>) [with Sequence =  
boost::fusion::list<>]'  
  
4 errors detected.  
  
$g++ --version  
Target: x86_64-pc-linux-gnu  
Configured with: ../gcc-9.1.0/configure --  --enable-languages=c,c++  
--disable-libquadmath --disable-libquadmath-support --disable-werror  
--disable-bootstrap --enable-gold  
Thread model: posix  
gcc version 9.1.0 (GCC)  
  
  
When analyzed further we have seen that  
this is  
[ boost_1_70_0/boost/fusion/functional/invocation/invoke.hpp:139 ]  
trying to return local address, which is undefined behavior.  
And that lead to above test-case failure.  
  
So either we have to modify test-case to pass as reference or disable  
it.  
Like to know your suggestion before going ahead?
