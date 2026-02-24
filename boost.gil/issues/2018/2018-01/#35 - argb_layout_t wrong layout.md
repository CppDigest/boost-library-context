# #35 - argb_layout_t wrong layout [Closed]

> Username: ohhmm  
> Created at: 2018-01-06 10:07:28 UTC  
> Updated at: 2018-04-17 22:28:39 UTC  
> Closed at: 2018-04-01 15:13:12 UTC  
> Url: https://github.com/boostorg/gil/issues/35  

`typedef layout<rgba_t, mpl::vector4_c<int,1,2,3,0> > argb_layout_t;`  
I guess it should be `typedef layout<rgba_t, mpl::vector4_c<int,3,0,1,2> > argb_layout_t;`  
sure it does not matter if use `get_color(d,alpha_t())` but frustrates everyone who use `a(i,j) = px[0];`

---

## Comment 1

> Username: ohhmm  
> Created at: 2018-03-07 18:48:55 UTC  
> Url: https://github.com/boostorg/gil/issues/35#issuecomment-371242822  

This is about wrong hardcode and should be resolved with all dependent fixes.

---

## Comment 2

> Username: chhenning  
> Created at: 2018-03-31 15:14:09 UTC  
> Url: https://github.com/boostorg/gil/issues/35#issuecomment-377699933  

I think gil is working as expected.  
```  
bits8 red   = 11;  
bits8 green = 22;  
bits8 blue  = 33;  
bits8 alpha = 99;  
  
typedef layout<rgba_t, mpl::vector4_c<int, 1, 2, 3, 0> > layout_1_t;  
typedef pixel<bits8, layout_1_t> pixel_1_t;  
  
typedef layout<rgba_t, mpl::vector4_c<int, 3, 0, 1, 2> > layout_2_t;  
typedef pixel<bits8, layout_2_t> pixel_2_t;  
  
pixel_1_t p1(alpha, red, green, blue);  
pixel_2_t p2(green, blue, alpha, red);  
  
auto a1 = get_color(p1, alpha_t());  
auto a2 = get_color(p2, alpha_t());  
  
assert(get_color(p1, alpha_t()) == get_color(p2, alpha_t()));  
assert(p1[0] == p2[2]);  
```  
  
The design is explained here:  
http://boostorg.github.io/gil/develop/doc/html/design_guide.html#color-space-and-layout  
  
Please provide a minimal reproducible code sample.

---

## Comment 3

> Username: mloskot  
> Created at: 2018-04-01 13:45:50 UTC  
> Url: https://github.com/boostorg/gil/issues/35#issuecomment-377787977  

In other words:  
  
- The `rgba_t` specifies order of sequence of colors buckets for  
  
- To derive new order of sequence of colors from the same set of colors, fill the `rgba_t` ordered sequence with corresponding colors in the new sequence. Fill it always in the same order as the original template sequence `rgba_t`: first R, second G, third B, fourth A.  
  
```  
typedef layout<rgba_t, mpl::vector4_c<int, r, g, b, a>> argb_layout_t;  
                                           |  |  |  |      
typedef layout<rgba_t, mpl::vector4_c<int, 1, 2, 3, 0>> argb_layout_t;  
                                           r  g  b  a  
```  
  
I have to admit, the design document takes some time to grasp it.

---

## Comment 4

> Username: ohhmm  
> Created at: 2018-04-02 20:06:53 UTC  
> Updated at: 2018-04-02 20:08:13 UTC  
> Url: https://github.com/boostorg/gil/issues/35#issuecomment-378028802  

As I remember, I created this bug because in argb_layout_t p[0] was not alpha channel value, but its was green channel value. And this seemed not expected for me. But `get_color` works as expected.

---

## Comment 5

> Username: ohhmm  
> Created at: 2018-04-02 20:10:25 UTC  
> Updated at: 2018-04-02 20:11:21 UTC  
> Url: https://github.com/boostorg/gil/issues/35#issuecomment-378029705  

@mloskot argb_layout_t has first value green?   
`argb_layout_t c;`  
`c[0] - green`

---

## Comment 6

> Username: ohhmm  
> Created at: 2018-04-02 20:12:42 UTC  
> Url: https://github.com/boostorg/gil/issues/35#issuecomment-378030256  

maybe this operator should be private then

---

## Comment 7

> Username: mloskot  
> Created at: 2018-04-17 22:28:11 UTC  
> Updated at: 2018-04-17 22:28:39 UTC  
> Url: https://github.com/boostorg/gil/issues/35#issuecomment-382176089  

@ohhmm  No, it is intentionally part of the public interface. This bit from the tests should clarify the issue, I think:  
  
https://github.com/boostorg/gil/blob/06be7959c09d0cf08afd640b862d508b4ebd15e2/test/pixel.cpp#L314-L320
