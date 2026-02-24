# #1103 - Regression 1.79.0: union operation of two polygons outputs zero polygons [Closed]

> Username: paulk125  
> Created at: 2022-12-16 22:39:46 UTC  
> Updated at: 2023-09-14 17:29:05 UTC  
> Closed at: 2023-09-14 11:00:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/1103  

I have two polygons. Both are nearly unit squares. One is from (0,epsilon)-(1,1) and the other is from (0,-1)-(1,0). So these two squares are not quite touching. I would expect the geometry union operation to either return one merged polygon or two separate polygons. Instead it returns zero polygons.  
  
Here is a godbolt link to duplicate the problem:  
https://godbolt.org/z/P16oKM6Pn  
  
Note that this bug affects 1.79 and later. If you change the compiler option in the godbolt link to include the 1.78 boost headers, then the union operation works fine:  
  
`-I/opt/compiler-explorer/libs/boost_1_78_0`

---

## Comment 1

> Username: barendgehrels  
> Created at: 2023-09-14 11:00:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/1103#issuecomment-1719233489  

Fixed by commit b1bebca45  
which also fixed #1100

---

## Comment 2

> Username: barendgehrels  
> Created at: 2023-09-14 16:59:00 UTC  
> Updated at: 2023-09-14 17:29:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/1103#issuecomment-1719815327  

Unit test is added in #1202
