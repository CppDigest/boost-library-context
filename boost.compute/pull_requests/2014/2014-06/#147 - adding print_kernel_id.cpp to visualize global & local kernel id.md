# #147 adding print_kernel_id.cpp to visualize global & local kernel id [Open]

> Username: Mageswaran1989  
> Created at: 2014-06-20 06:43:24 UTC  
> Updated at: 2016-10-19 04:04:48 UTC  
> Url: https://github.com/boostorg/compute/pull/147  

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
> Created_at: 2014-06-20 07:15:06 UTC  
> Url: https://github.com/boostorg/compute/pull/147#issuecomment-46651081  

[![Coverage Status](https://coveralls.io/builds/886733/badge)](https://coveralls.io/builds/886733)  
  
Coverage remained the same when pulling **ff558d7dc240801af45c7df615e680e3f6a61547 on Mageswaran1989:adding-print_kernel_id.cpp-example** into **4abb85705c07d13d87fe76fa93d921bc698e4d76 on kylelutz:develop**.

---

## Comment 2

> Username: Mageswaran1989  
> Created_at: 2014-11-05 18:43:36 UTC  
> Url: https://github.com/boostorg/compute/pull/147#issuecomment-61859111  

Please review

---

## Comment 3

> Username: roshanrags  
> Created_at: 2014-12-29 18:42:34 UTC  
> Url: https://github.com/boostorg/compute/pull/147#issuecomment-68284691  

To add leading zeros, use something like `%07.2f`. This gives 2 digits after decimal and total of 7 characters(including decimal point) padded with leading zeros i.e. something like 00xx.xx

---
