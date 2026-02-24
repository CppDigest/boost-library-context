# #1397 - boost::geometry::intersection algorithm returning invalid geometry for int [Open]

> Username: vschoech  
> Created at: 2025-04-25 10:43:53 UTC  
> Updated at: 2025-05-13 20:20:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/1397  

My `tc::geo::polygon` type is actually a **multi-polygon**, using a polygon type that is based on **int**, oriented **counter-clockwise** and **open** (not closed). I am using **boost 1.87.0**. Please consider the following example:   
  
```C++  
tc::geo::polygon<int> polygonA;  
boost::geometry::read_wkt("MULTIPOLYGON(((1850 1885,1852 1841,1854 1815,1856 1796,1858 1782,1860 1770,1862 1759,1864 1751,1866 1743,1869 1735,1871 1729,1873 1723,1875 1717,1878 1712,1880 1707,1883 1702,1885 1697,1888 1693,1891 1688,1894 1684,1897 1680,1900 1676,1904 1672,1907 1668,1911 1664,1915 1660,1920 1656,1924 1652,1929 1648,1935 1643,1940 1639,1947 1635,1954 1630,1962 1626,1971 1621,1980 1616,1992 1611,1998 1608,2004 1629,2021 1682,2039 1731,2057 1777,2075 1820,2080 1832,2022 3502,1848 3502,1850 1885)),((3185 3502,3145 2700,3271 2747,3425 2799,3611 2856,3843 2918,4140 2988,4533 3068,5082 3162,5904 3277,5904 3502,3185 3502)),((2267 2130,2275 2139,2303 2171,2332 2203,2363 2234,2396 2265,2432 2297,2470 2329,2511 2361,2555 2394,2604 2427,2657 2462,2716 2497,2781 2534,2855 2572,2938 2612,3011 2645,3010 2655,2944 3502,2352 3502,2267 2130)))", polygonA); // does not throw  
boost::geometry::is_valid(polygonA); // returns true  
  
tc::geo::polygon<int> polygonB;  
boost::geometry::read_wkt("MULTIPOLYGON(((3735 3502,1848 1563,1848 957,5904 957,5904 3502,3735 3502)))", polygonB); // does not throw  
boost::geometry::is_valid(polygonB); // returns true  
  
tc::geo::polygon<int> polygonC;  
boost::geometry::intersection(polygonA, polygonB, polygonC); // does not throw  
// polygonC: MULTIPOLYGON(((1930 1647,1940 1639,1947 1635,1954 1630,1962 1626,1971 1621,1980 1616,1992 1611,1998 1608,2004 1629,2021 1682,2039 1731,2057 1777,2058 1778,1930 1647)),((3735 3502,3155 2906,3145 2700,3271 2747,3425 2799,3611 2856,3843 2918,4140 2988,4533 3068,5082 3162,5904 3277,5904 3502,3735 3502)),((2805 2546,2855 2572,2938 2612,3011 2645,3010 2655,3003 2749,2805 2546)))  
boost::geometry::is_valid(polygonC); // returns false!  
```  
  
**The intersection of two valid multi-polygons yields an invalid multi-polygon.**   
  
See also:  
- https://github.com/boostorg/geometry/issues/1034  
- https://github.com/boostorg/geometry/issues/1035  
- https://github.com/boostorg/geometry/issues/1394  
- https://github.com/boostorg/geometry/issues/1395

---

## Comment 1

> Username: vschoech  
> Created at: 2025-04-25 10:44:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/1397#issuecomment-2830065696  

@barendgehrels
