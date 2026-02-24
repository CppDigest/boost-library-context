# #1084 - Buffer Issue - return a filled multipolygone [Closed]

> Username: Oximore  
> Created at: 2022-11-07 15:50:58 UTC  
> Updated at: 2022-11-09 14:16:17 UTC  
> Closed at: 2022-11-09 14:16:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/1084  

Hello,  
  
I have an issue with `boost::geometry::buffer`.  
Running this command on the given line don't give me the expected multipolygon output.  
  
Disclaimer, as I am not an intensive user of boost::geometry I may have miss something with my usage.  
  
Boost version:   
`BOOST_VERSION: 1.74.0`  
  
  
Input:  
`"LINESTRING(269.3 -7.03, 270.23 -3.57, 270.54 0, 270.54 100, 270.23 103.57, 269.3 107.03, 267.79 110.27, 265.73 113.2, 263.2 115.73, 258.89 118.43, 254.28 108.54, 248.52 109, 80 97.21, 72.71 95.87, 67.46 93.82, 62.61 90.92, 58.32 87.27, 54.69 82.95, 51.83 78.08, 49.81 72.81, 48.7 67.28, 48.45 63.38, 48.52 34.87, 49.29 29.26, 50.96 23.87, 54.69 17.05, 58.32 12.73, 62.61 9.08, 65.57 7.18, 70.68 4.81, 76.12 3.31, 80 2.79, 248.52 -9, 254.28 -8.54, 258.89 -18.43, 263.2 -15.73, 265.73 -13.2, 267.79 -10.27, 269.3 -7.03, 270.23 -3.57, 270.54 0, 270.54 100, 270.23 103.57, 269.3 107.03, 267.79 110.27, 265.73 113.2, 264 114.93, 256.24 107.17, 251.86 108.16, 248.58 108.2, 76.24 95.9, 70.93 94.43, 65.94 92.11, 61.4 88.99, 57.44 85.16, 54.18 80.72, 51.69 75.8, 50.06 70.54, 49.25 63.38, 49.32 34.93, 50.06 29.46, 51.69 24.2, 55.36 17.49, 58.9 13.28, 63.1 9.71, 67.83 6.89, 70.93 5.57, 76.24 4.1, 80.06 3.59, 248.58 -8.2, 251.86 -8.16, 256.24 -7.17, 258.89 -18.43)";`  
  
Call:  
```c++  
  const int ppc = 10;  
  const distance_symmetric<double> distance_strategy(10);  
  const join_round join_strategy(ppc);  
  const end_round end_strategy(ppc);  
  const point_circle circle_strategy(ppc);  
  const side_straight side_strategy;  
  
  int r = 10;  
  boost::geometry::buffer(inLine, outPoly, distance_symmetric<double>(r),  
                          side_strategy, join_strategy, end_strategy,  
                          circle_strategy);  
```  
Output:  
  
![geom-r10](https://user-images.githubusercontent.com/1199442/200349706-42abcd25-59a6-42e8-b6d0-9204d3aba952.png)  
  
Expected output:  
(Something looking like)  
![geom-r9](https://user-images.githubusercontent.com/1199442/200349806-1e74c5e1-5bad-430d-a33a-cf64fd98b6ce.png)  
  
<details>  
  <summary>Comparison of both</summary>  
    
  Results with `r=9` (orange) and `r=10` (blue)  
  ![both](https://user-images.githubusercontent.com/1199442/200352089-e609d135-1188-49b9-8832-e29552b86092.png)  
</details>  
  
  
Some pointers:  
 - it work as expected with a `distance_symmetric<double>(r)` buffer strategy where r is less than 10.  
   
 Some code to test:  
[Code gist]( https://gist.github.com/Oximore/c65419980aba7e853bfceeab3d11db2f)  
   
Remark:  
It seems to work well with Boost `1.65.1`,   
  
<details>  
  <summary>Result of given code for boost versions</summary>  
  
On my computer  
```  
BOOST_VERSION: 1.74.0  
is original geometry valid? yes  
aera of output (with r=9):	11942.7  
aera of output (with r=10):	29854.3  
```  
  
On https://rextester.com/  
```  
BOOST_VERSION: 1.65.1  
is original geometry valid? yes  
aera of output (with r=9):	11942.7  
aera of output (with r=10):	13200.8  
```  
</details>

---

## Comment 1

> Username: barendgehrels  
> Created at: 2022-11-08 08:43:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/1084#issuecomment-1306838789  

Thanks for the report I will look tomorrow

---

## Comment 2

> Username: barendgehrels  
> Created at: 2022-11-09 09:05:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/1084#issuecomment-1308434340  

With the latest version (~ Boost 1.80) I cannot reproduce your problem.  
```  
BOOST_VERSION: 1.80.0  
is original geometry valid? yes  
aera of output (with r=9):      11942.7  
aera of output (with r=10):     13200.8  
```  
I used three different compilers.  
  
Can you check with Boost 1.80?

---

## Comment 3

> Username: Oximore  
> Created at: 2022-11-09 14:16:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/1084#issuecomment-1308831521  

It works as expected with Boost 1.80.  
  
Sorry for any inconvenience.  
Thanks you for the quick feedback.  
  
Have a nice day.
