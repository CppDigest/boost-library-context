# #832 Area strategies for non-cartesian boxes. [Merged]

> Username: awulkiew  
> Created at: 2021-03-23 15:07:51 UTC  
> Updated at: 2021-06-30 21:04:07 UTC  
> Merged at: 2021-06-30 21:04:07 UTC  
> Closed at: 2021-06-30 21:04:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/832  

The intention of this PR is to fix issue: https://github.com/boostorg/geometry/issues/439  
  
The approach for spherical and geographic coordinate systems is as follows:  
- describe the infinitesimal area of an element on the surface of a sphere/spheroid  
- put the integral into maxima to get the formula  
- implement the formula  
  
See the comments in the files defining strategies for the sources and maxima scripts.  
  
The surprising part is that it seems that there is a closed equation for spheroidal element and no integration nor expressing the integral as a series is needed.  
  
For now I verified the results only for some specific cases, i.e. 1/8 of the globe or the whole globe because it's possible to create a corresponding polygon. For WGS84 spheroid and sphere of mean radius of WGS84 spheroid the results are:  
```  
box b({ 0, 0 }, { 90, 90 });  
BG-sph  box: 63758234549478.125  
BG-sph poly: 63758234549478.117188  
BG-geo  box: 63758202715511.0625  
BG-geo poly: 63758202715511.046875  
GeoLib poly: 63758202715511.054688  
  
box b({ -180, -90 }, { 180, 90 });  
BG-sph  box: 510065876395825  
BG-sph poly: 510065876395824.9375  
BG-geo  box: 510065621724088.4375  
BG-geo poly: 510065621724088.375  
GeoLib poly: 510065621724088.4375  
```  
`poly` are calculated for polygon covering 1/8 of a globe multiplied by 8.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2021-03-23 15:17:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/832#issuecomment-804988837  

This is not my primary area of expertise and I'm suspicious about the result. In particular I'd be happy if you could double-check the math.  
  
@cffk would you be willing to review this PR?

---

## Comment 2

> Username: cffk  
> Created_at: 2021-03-23 17:55:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/832#issuecomment-805108865  

> This is not my primary area of expertise and I'm suspicious about the result. In particular I'd be happy if you could double-check the math.  
>   
> @cffk would you be willing to review this PR?  
  
Sure, I'll take a look.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2021-03-23 17:59:03 UTC  
> Updated_at: 2021-03-23 19:10:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/832#issuecomment-805111643  

EDIT: Before I showed different results for GeographicLib. This was my fault. I switched lat and lon when passing them to `AddPoint`. These are the correct results:  
```  
box b({ 0, 0 }, { 90, 90 });  
BG-sph box   : 63758234549478.125  
BG-sph poly  : 63758234549478.117188  
BG-geo box   : 63758202715511.0625  
BG-geo poly a: 63758202715511.046875  
BG-geo poly v: 63758202715511.046875  
GeoLib poly  : 63758202715511.054688  
  
box b({ 0, 0 }, { 45, 90 });  
BG-sph box   : 31879117274739.0625  
BG-sph poly  : 31879117274739.058594  
BG-geo box   : 31879101357755.53125  
BG-geo poly a: 31879101357755.527344  
BG-geo poly v: 31879101357755.527344  
GeoLib poly  : 31879101357755.527344  
  
box b({ 0, 0 }, { 10, 90 });  
BG-sph box   : 7084248283275.3476563  
BG-sph poly  : 7084248283275.3457031  
BG-geo box   : 7084244746167.8955078  
BG-geo poly a: 7084244746167.8876953  
BG-geo poly v: 7084244746167.8876953  
GeoLib poly  : 7084244746167.8955078  
```  
  
In case you were wondering what's the polygon. E.g. in the last case it's `POLYGON((0 0,0 90,10 0,0 0))`. The area with GeographicLib is calculated like that:  
```  
double gl_area(ring const& r)  
{  
    GeographicLib::PolygonAreaT<> pa(GeographicLib::Geodesic::WGS84());  
    for (point const& p : r)  
        pa.AddPoint(bg::get<1>(p), bg::get<0>(p));  
    double foo, a;  
    pa.Compute(true, true, foo, a);  
    return a;  
}  
```

---

## Comment 4

> Username: awulkiew  
> Created_at: 2021-03-23 18:55:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/832#issuecomment-805151745  

Now I approximate boxes with polygons. The number of additional points for horizontal edges depends on `cos(lat)` to keep lengths of added segments similar.  
```  
box b({ -1, -1 }, { 1, 1 });  
BG-sph box   : 49454872136.939735413  
BG-sph poly  : 49454872138.19190979  
BG-geo box   : 49233855575.901062012  
BG-geo poly a: 49233855529.048690796  
BG-geo poly v: 49233855550.54221344  
GeoLib poly  : 49233855577.159233093  
  
box b({ 1, 1 }, { 2, 2 });  
BG-sph box   : 12359951925.951681137  
BG-sph poly  : 12359951926.264892578  
BG-geo box   : 12304814950.07283783  
BG-geo poly a: 12304814983.331821442  
BG-geo poly v: 12304815055.35602951  
GeoLib poly  : 12304814950.386703491  
  
box b({ 0, 88 }, { 1, 89 });  
BG-sph box   : 323656731.73189043999  
BG-sph poly  : 323656730.76636141539  
BG-geo box   : 326564202.31182825565  
BG-geo poly a: 326564191.98450493813  
BG-geo poly v: 326564201.34215426445  
GeoLib poly  : 326564201.33717727661  
  
box b({ 179, -89 }, { 181, -1 });  
BG-sph box   : 1391906427521.2453613  
BG-sph poly  : 1391906427556.3400879  
BG-geo box   : 1392014288082.3757324  
BG-geo poly a: 1392014288148.09375  
BG-geo poly v: 1392014288131.0981445  
GeoLib poly  : 1392014288117.814209  
  
box b({ 175, -89 }, { 185, 89 });  
BG-sph box   : 14166338635896.939453  
BG-sph poly  : 14166338636254.617188  
BG-geo box   : 14166312158703.046875  
BG-geo poly a: 14166312159126.431641  
BG-geo poly v: 14166312159063.943359  
GeoLib poly  : 14166312159063.939453  
  
box b({ 179, -1 }, { 181, 1 });  
BG-sph box   : 49454872136.940437317  
BG-sph poly  : 49454872138.192527771  
BG-geo box   : 49233855575.901756287  
BG-geo poly a: 49233855529.15196228  
BG-geo poly v: 49233855550.541778564  
GeoLib poly  : 49233855577.159240723  
```  
  
The results are not exactly the same but this is probably caused by the fact that it is not possible to represent a box with a polygon.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2021-03-23 19:04:45 UTC  
> Updated_at: 2021-03-23 19:07:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/832#issuecomment-805157813  

How does the approximation influence the results?  
```  
box b({ -1, -1 }, { 1, 1 });  
BG-sph box   : 49454872136.939735413  
BG-geo box   : 49233855575.901062012  
  
The same number of approximating points as above  
BG-sph poly  : 49454872138.19190979  
BG-geo poly a: 49233855529.048690796  
BG-geo poly v: 49233855550.54221344  
GeoLib poly  : 49233855577.159233093  
  
10x more points:  
BG-sph poly  : 49454872136.976295471  
BG-geo poly a: 49233852904.589157104  
BG-geo poly v: 49233870391.519317627  
GeoLib poly  : 49233855575.91394043  
  
100x more points:  
BG-sph poly  : 49454872137.082992554  
BG-geo poly a: 49232814828.290130615  
BG-geo poly v: 49233525468.32774353  
GeoLib poly  : 49233855575.901939392  
```  
  
It seems that in the second case vincenty is the most different but in the last case andoyer's difference skyrockets while GeographicLib becomes closer each time.  
  
In the last case the difference for spherical increases as well.

---

## Comment 6

> Username: cffk  
> Created_at: 2021-03-23 19:12:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/832#issuecomment-805162798  

GeographicLib allows you to compute the area of a "box" (edges are circles of latitude + meridians) using rhumb lines.  E.g., use the `-R` option on Planimeter:  
```sh  
Planimeter -w -p 10 -R <<EOF  
0 88  
1 88  
1 89  
0 89   
EOF  
```  
gives  
```sh  
4 229233.7288327920 326564202.31213  
```

---

## Comment 7

> Username: cffk  
> Created_at: 2021-03-23 19:41:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/832#issuecomment-805182767  

I recommend using using the formula in terms of `atanh` instead of `log`.  It's less cumbersome and more accurate.  Also you need to deal with prolate ellipsoids too.  
  
The area from of a box of longitude width `dlambda` extending from the equator to latitude, `phi` is  
```  
dlambda*b^2*integrate(cos(phi)/(1-e^2*sin(phi)^2)^2,phi,0,phi)  
```  
For `e^2 > 0`, this is  
```  
dlambda*b^2*sin(phi)/2*  
  (1/(1-e^2*sin(phi)^2) + atanh(e*sin(phi))/(e*sin(phi)))  
```  
For `e^2 < 0`, we have  
```  
dlambda*b^2*sin(phi)/2*  
  (1/(1-e^2*sin(phi)^2) + atan(ea*sin(phi))/(ea*sin(phi)))  
```  
where `b` is the polar semi-axis and `ea = sqrt(-e^2)`.  
  
I'm not sure how boost deals with the longitude wrapping around.  You'll need to make sure that the box  
```  
({179, 0}, {-179, 1})  
```  
returns the expected result.

---

## Comment 8

> Username: awulkiew  
> Created_at: 2021-03-23 19:44:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/832#issuecomment-805184475  

Right, thanks!  
  
So mixing both libraries would look like this:  
```  
double gl_area(box const& b)  
{  
    GeographicLib::PolygonAreaRhumb pa(GeographicLib::Rhumb::WGS84());  
    double x0 = bg::get<bg::min_corner, 0>(b);  
    double y0 = bg::get<bg::min_corner, 1>(b);  
    double x1 = bg::get<bg::max_corner, 0>(b);  
    double y1 = bg::get<bg::max_corner, 1>(b);  
    pa.AddPoint(y0, x0);  
    pa.AddPoint(y1, x0);  
    pa.AddPoint(y1, x1);  
    pa.AddPoint(y0, x1);  
    pa.AddPoint(y0, x0);  
    double foo, a;  
    pa.Compute(true, true, foo, a);  
    return a;  
}  
```  
Plus maybe normalization of longitudes because BG boxes may have max longitude > 180.  
  
Ok:  
```  
box b({ -15, -7 }, { 1, 73 });  
BG-geo box   : 12210630635795.742188  
GeoLib box   : 12210630635795.744141  
```

---

## Comment 9

> Username: awulkiew  
> Created_at: 2021-03-23 20:40:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/832#issuecomment-805236915  

@cffk Thanks!  
  
> The area from of a box of longitude width dlambda extending from the equator to latitude, phi is (...)  
  
Right, this is the same integral that I'm using but with `b^2` replacing `a^2(1-e^2)` and different bounds of integration.   
  
> I recommend using using the formula in terms of atanh instead of log. It's less cumbersome and more accurate.  
  
That's interesting. When I use the exact script you pasted maxima still generates formula with logarithms. What am I missing?

---

## Comment 10

> Username: cffk  
> Created_at: 2021-03-23 21:02:01 UTC  
> Updated_at: 2021-03-23 22:59:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/832#issuecomment-805257424  

> > I recommend using using the formula in terms of atanh instead of log. It's less cumbersome and more accurate.  
>   
> That's interesting. When I use the exact script you pasted maxima still generates formula with logarithms. What am I missing?  
  
You're missing the fact that maxima doesn't always give you the most simplified form!  The `atanh` formula can be checked by doing an `ev(..., logarc)` on it.  (And in a pinch, I use: `fpprec:100$ ev([expr1, expr2], e=0.1b0, phi=1b0, ...);` and verifying that the relative difference is about `1b-100`.)

---

## Comment 11

> Username: awulkiew  
> Created_at: 2021-03-23 23:09:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/832#issuecomment-805339924  

`logarc` is set to `false` by default and atanh should've been in the output from the start. I verified this in practice for some other simple integral. `ev(..., logarc)` would enable logarithms, not disable them. Anyway, I'll play with it more later. Thanks for the help!

---

## Comment 12

> Username: cffk  
> Created_at: 2021-03-24 00:45:12 UTC  
> Updated_at: 2021-03-24 11:03:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/832#issuecomment-805384223  

> `logarc` is set to `false` by default and atanh should've been in the output from the start. I verified this in practice for some other simple integral. `ev(..., logarc)` would enable logarithms, not disable them. Anyway, I'll play with it more later. Thanks for the help!  
  
I don't expect that maxima can always get an expression into the form I want.  Sometimes I have to nurse it along.  That's the case here.  You can use `logarc` to check that the `atanh` result is equivalent to the result of the `integrate` command.  Alternatively, you can differentiate the `atanh` expression and confirm that it matches the integrand.

---

## Comment 13

> Username: cffk  
> Created_at: 2021-03-24 16:12:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/832#issuecomment-805959375  

I recommend sanitizing your PR by removing trailing blanks (and untabifying if necessary).

---

## Comment 14

> Username: awulkiew  
> Created_at: 2021-03-24 16:41:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/832#issuecomment-805981647  

@cffk What do you mean by "removing trailing blanks"? Do you mean spaces at the end of the line? If not maybe you could point out the place in the PR e.g. by commenting in a specific line?

---

## Comment 15

> Username: cffk  
> Created_at: 2021-03-24 17:43:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/832#issuecomment-806029307  

Yes, I just mean spaces at the end of the line (such as are removed by Emacs' M-x delete-trailing-whitespace).

---

## Comment 16

> Username: awulkiew  
> Created_at: 2021-06-18 10:21:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/832#issuecomment-863932541  

@barendgehrels @vissarion Are you ok with this change?

---

## Review 17 [Approved]

> Username: vissarion  
> Created_at: 2021-06-24 09:16:54 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/832#pullrequestreview-691526105  

I am OK with merging.

---
