# #76 - regression with scatter and intel19 [Closed]

> Username: aminiussi  
> Created at: 2018-10-31 15:43:35 UTC  
> Updated at: 2019-10-22 14:44:16 UTC  
> Closed at: 2019-10-22 14:44:16 UTC  
> Url: https://github.com/boostorg/mpi/issues/76  

```  
capture-output ../../../bin.v2/libs/mpi/test/scatter_test-7.test/intel-linux-std11/debug/visibility-hidden/scatter_test-7-run  
====== BEGIN OUTPUT ======  
Scattering integers from root 0...  
Scattering integers from root 1...  
Scattering integers from root 2...  
Scattering integers from root 3...  
Scattering integers from root 4...  
Scattering integers from root 5...  
Scattering integers from root 6...  
Scattering GPS positions from root 0...  
Scattering GPS positions from root 1...  
Scattering GPS positions from root 2...  
Scattering GPS positions from root 3...  
Scattering GPS positions from root 4...  
Scattering GPS positions from root 5...  
Scattering GPS positions from root 6...  
Scattering string from root 0...  
Scattering string from root 1...  
../../../boost/test/minimal.hpp(136): exception "memory access violation at address: 0x75d631c3: no mapping at fault address" caught in function: 'int main(int, char **)'  
```  
  
It seems that process zero get a slightly shifted archive buffer, just enough to screw the size wrong.  
  
Issues 03708514 was file to Intel.

---

## Comment 1

> Username: aminiussi  
> Created at: 2018-10-31 15:46:04 UTC  
> Updated at: 2018-10-31 15:46:54 UTC  
> Url: https://github.com/boostorg/mpi/issues/76#issuecomment-434738762  

A minimal test case, withou Boost.MPI involved, was provided to Intel support.  
[plain_cpp.txt](https://github.com/boostorg/mpi/files/2535186/plain_cpp.txt)  
```  
[alainm@castor test]$ mpiicpc -std=c++11 -g  ./plain.cpp -o plain  
[alainm@castor test]$ mpirun -n 6 ./plain  
Scattering strings from root 1...  
0 got fghijklmnabcde  
1 got abcdefghijklmn  
2 got abcdefghijklmn  
3 got abcdefghijklmn  
4 got abcdefghijklmn  
5 got abcdefghijklmn  
[alainm@castor test]$ mpirun -n 7 ./plain  
Scattering strings from root 1...  
0 got @  
1 got abcdefghijklmn  
2 got abcdefghijklmn  
3 got abcdefghijklmn  
4 got abcdefghijklmn  
5 got abcdefghijklmn  
6 got abcdefghijklmn  
[alainm@castor test]$ mpirun -n 8 ./plain  
Scattering strings from root 1...  
0 got   
1 got abcdefghijklmn  
2 got abcdefghijklmn  
3 got abcdefghijklmn  
4 got abcdefghijklmn  
5 got abcdefghijklmn  
6 got abcdefghijklmn  
7 got abcdefghijklmn  
[alainm@castor test]$ mpirun -n 9 ./plain  
Scattering strings from root 1...  
0 got abcdefghijklmn  
1 got abcdefghijklmn  
2 got abcdefghijklmn  
3 got abcdefghijklmn  
4 got abcdefghijklmn  
5 got abcdefghijklmn  
6 got abcdefghijklmn  
7 got abcdefghijklmn  
8 got abcdefghijklmn  
[alainm@castor test]$   
```

---

## Comment 2

> Username: aminiussi  
> Created at: 2018-12-09 19:13:18 UTC  
> Url: https://github.com/boostorg/mpi/issues/76#issuecomment-445563139  

Intel agreed there is a bug and provided possible workaround:  
[alainm@pollux test]$ export  I_MPI_ADJUST_SCATTERV=1

---

## Comment 3

> Username: aminiussi  
> Created at: 2019-10-22 14:44:16 UTC  
> Url: https://github.com/boostorg/mpi/issues/76#issuecomment-544997667  

Fixed with intel 2019.4
