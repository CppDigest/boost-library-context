# #146 adding print_kernel_id.cpp to visualize global & local kernel id [Closed]

> Username: Mageswaran1989  
> Created at: 2014-06-20 06:26:17 UTC  
> Updated at: 2014-06-20 06:54:37 UTC  
> Closed at: 2014-06-20 06:42:56 UTC  
> Url: https://github.com/boostorg/compute/pull/146  

Hi,  
Added an example to print the global and local id of kernel.  
Basically the kernel IDs are stored in float\* and printed in terminal.  
  
Output:  
$ ./print_kernel_id   
Number of groups created / grid size: 2 x 2  
Number of local threads / local size: 4 x 4  
Number of global threads: 8 x 8  
  
GlobalIdXY.LocalIdXY  
##     g_xg_y.lxl_y  
  
```  
0.00  100.10  200.20  300.30 |   400.00  500.10  600.20  700.30 |   
1.01  101.11  201.21  301.31 |   401.01  501.11  601.21  701.31 |   
2.02  102.12  202.22  302.32 |   402.02  502.12  602.22  702.32 |   
3.03  103.13  203.23  303.33 |   403.03  503.13  603.23  703.33 |   
  
4.00  104.10  204.20  304.30 |   404.00  504.10  604.20  704.30 |   
5.01  105.11  205.21  305.31 |   405.01  505.11  605.21  705.31 |   
6.02  106.12  206.22  306.32 |   406.02  506.12  606.22  706.32 |   
7.03  107.13  207.23  307.33 |   407.03  507.13  607.23  707.33 |   
```  
  
!!! Zeros pre to the number are ignored while printing !!!

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-06-20 06:54:37 UTC  
> Url: https://github.com/boostorg/compute/pull/146#issuecomment-46649899  

[![Coverage Status](https://coveralls.io/builds/886707/badge)](https://coveralls.io/builds/886707)  
  
Coverage remained the same when pulling **ff558d7dc240801af45c7df615e680e3f6a61547 on Mageswaran1989:adding-print_kernel_id.cpp-example** into **b670212ee8dc0a4cdd2fcffa90f423ee43221aad on kylelutz:master**.

---
