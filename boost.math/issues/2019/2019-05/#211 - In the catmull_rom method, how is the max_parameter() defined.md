# #211 - In the catmull_rom method, how is the max_parameter() defined? [Closed]

> Username: xarthurx  
> Created at: 2019-05-23 13:39:05 UTC  
> Updated at: 2021-09-30 02:31:38 UTC  
> Closed at: 2019-05-23 16:46:04 UTC  
> Url: https://github.com/boostorg/math/issues/211  

I'm testing the newly added interpolation function `catmull_rom` where I'm a little confused by the `max_parameter()` of a spline.  
  
For a simple case that to interpolate 11 points `Vector2d(x, x)` where x = 0 - 10.  
Then I tested the intepolated catmull_rom crv:  
```  
    for (size_t i = 0; i <= 11; i++) {  
      cout << "input: " << i << " output: " << cr(i).transpose() << endl;  
    }  
    cout << "max pram: " << cr.max_parameter() << endl;  
```  
the output is:  
```  
input: 0 output: 0 0  
input: 1 output: 0.81961 0.81961  
input: 2 output: 1.68179 1.68179  
input: 3 output: 2.52269 2.52269  
input: 4 output: 3.36359 3.36359  
input: 5 output: 4.20448 4.20448  
input: 6 output: 5.04538 5.04538  
input: 7 output: 5.88627 5.88627  
input: 8 output: 6.72717 6.72717  
input: 9 output: 7.56807 7.56807  
input: 10 output: 8.40896 8.40896  
input: 11 output: 9.29669 9.29669  
max pram: 11.8921  
```  
  
The result is quite strange to me...  
  
Is the parameter `param` of `cr(param)` supposed to be the length on the spline?  
If will it be possible to add a normalised function so that the parameter range is `[0, 1]`?  
  
Currently, I don't understand how to get the value of an interpolated point on the spline.

---

## Comment 1

> Username: NAThompson  
> Created at: 2019-05-23 14:50:32 UTC  
> Updated at: 2019-05-23 16:20:54 UTC  
> Url: https://github.com/boostorg/math/issues/211#issuecomment-495252078  

> Is the parameter param of cr(param) supposed to be the length on the spline?  
  
No. Let's start off with the chordal parametrization: Then parameter at p0 is zero, and the parameter at p1 is |p1 - p0|. But of course this isn't the length of the curve, as |p1-p0| is the straight line distance between p0 and p1, and we know that Catmull-Rom splines are cubic curves within segments. The interpretation gets even more difficult when we use the centripedal parametrization, because then s1 = |p1-p0|^{1/2}.   
  
This is why the method `parameter_at_point` is provided. Say you want to interpolate between points three and four on your curve.  
  
You would write something like:  
  
```cpp  
double s3 = cr.parameter_at_point(3);  
double s4 = cr.parameter_at_point(4);  
for (double s = s3; s < s3; s += 0.01) {  
    auto P = cr(s);  
}  
```  
  
> If will it be possible to add a normalised function so that the parameter range is [0, 1]?  
  
Sadly, no. If this was done the length of the tangent vectors would be totally wrong for the chordal parametrization.  
  
It may help to read:  
  
Cem Yuksel, Scott Schaefer, and John Keyser, Parameterization and applications of Catmull–Rom curves, Computer-Aided Design 43 (2011) 747–755.

---

## Comment 2

> Username: xarthurx  
> Created at: 2019-05-23 15:24:57 UTC  
> Updated at: 2019-05-23 15:25:16 UTC  
> Url: https://github.com/boostorg/math/issues/211#issuecomment-495266154  

@NAThompson   
Thanks for the reply. It makes more sense to me now.  
  
Would it be possible to provide a parameterization of `[0, 1]` for only the `centripedal` option?  
  
I need sth like that for a 3d spline, and the other two interpolation method that `boost` provide doesn't support 3d.  
If the above is not possible, do you have any suggestions on a library for that?

---

## Comment 3

> Username: NAThompson  
> Created at: 2019-05-23 16:18:51 UTC  
> Updated at: 2019-05-23 16:20:07 UTC  
> Url: https://github.com/boostorg/math/issues/211#issuecomment-495287212  

@xarthurx : Doing a linear change of variables to t in [0,1] is trivial:  
  
```cpp  
double s_max = cr.max_parameter();  
auto point = cr(s_max*t);  
```

---

## Comment 4

> Username: NAThompson  
> Created at: 2019-05-23 17:42:04 UTC  
> Url: https://github.com/boostorg/math/issues/211#issuecomment-495316248  

Incidentally, the expression `cr(s_max*t)` makes it clear why we [0,1] parametrization is a bad idea if we are interested in tangent vectors to the curve: We can change their magnitude by simply adding points!

---

## Comment 5

> Username: oleg-alexandrov  
> Created at: 2021-09-28 03:40:17 UTC  
> Updated at: 2021-09-28 03:41:29 UTC  
> Url: https://github.com/boostorg/math/issues/211#issuecomment-928726642  

The documentation at https://www.boost.org/doc/libs/master/libs/math/doc/html/math_toolkit/catmull_rom.html should document the parameter_at_point() function.   
  
I understand that cr(t) is motivated by the arc logic, but the interface this class provides is terrible. The user should not have to understand the implementation and the role of max_parameter(), and whatever power is used internally (that alpha between 0 and 1).   
  
Instead of this:  
  
double s3 = cr.parameter_at_point(3);  
double s4 = cr.parameter_at_point(4);  
for (double s = s3; s < s3; s += 0.01) {  
    auto P = cr(s);  
}  
  
the operator cr(i) should simply return the i-th value, and fit appropriately in between.

---

## Comment 6

> Username: oleg-alexandrov  
> Created at: 2021-09-28 03:45:25 UTC  
> Url: https://github.com/boostorg/math/issues/211#issuecomment-928732498  

At the very least, this class should provide (and document) a function which does cr.value_at(t) which for t = i would return the i-th element and fit in between according to the spline logic.

---

## Comment 7

> Username: NAThompson  
> Created at: 2021-09-28 03:51:39 UTC  
> Url: https://github.com/boostorg/math/issues/211#issuecomment-928739695  

> the operator cr(i) should simply return the i-th value, and fit appropriately in between.  
  
So each segment should have a different velocity?

---

## Comment 8

> Username: oleg-alexandrov  
> Created at: 2021-09-28 04:16:11 UTC  
> Updated at: 2021-09-28 04:26:59 UTC  
> Url: https://github.com/boostorg/math/issues/211#issuecomment-928765665  

I do understand that this class has its own intricacies. Hence my suggestion in the second post, to add a new function,  cr.value_at(t), which should be documented (the current documentation is poor), and which would return i-th value for t = i, for users who don't care about velocity.  
  
I am not fully sure how velocity is handled to start with, even on a single interval. The example above says:  
  
double s3 = cr.parameter_at_point(3);  
double s4 = cr.parameter_at_point(4);  
for (double s = s3; s < s3; s += 0.01) {  
    auto P = cr(s);  
}  
  
Is it true that as a variable u varies at constant speed between 0 and 1 then cr( (1-u)*s3 + u *s4 ) will end up having constant velocity between points 3 and 4?   
  
My apologizes for the initial terse version of this post. I appreciate your helpful and prompt response above.

---

## Comment 9

> Username: NAThompson  
> Created at: 2021-09-29 18:19:16 UTC  
> Updated at: 2021-09-29 18:36:05 UTC  
> Url: https://github.com/boostorg/math/issues/211#issuecomment-930429182  

> Is it true that as a variable u varies at constant speed between 0 and 1 then cr( (1-u)*s3 + u *s4 ) will end up having constant velocity between points 3 and 4?  
  
You'll forgive my initial question; it contained a typo: It should've read "So the velocity should be discontinuous at interpolation points?" The velocity will certainly not be constant unless special pointsets selected.  
  
My mental model which induced the question was of the arclength parametrization with points chosen on a line. If p0 is a nanometer from p1, and p1 a meter from p2, then doing integer indexing will cause the velocity to change discontinuously across segments.

---

## Comment 10

> Username: oleg-alexandrov  
> Created at: 2021-09-29 20:40:42 UTC  
> Url: https://github.com/boostorg/math/issues/211#issuecomment-930526663  

I understand your point. This spline algorithm is designed to provide a smoothly varying curve which goes through the provided points, and it tells the user how to use the parametrization to traverse that curve.  
  
What I need is a solution to the following problem (and I think other folks want the same thing):   
  
Given n points, P[0], P[1], ...., P[n-1], find a smooth function f(t) so that f(i) = P[i] for i i =0, 1, ....  
  
In other words, continuously differentiable interpolation is desired.   
  
You pointed out correctly that if one defines f(t) piecewise, in every interval [i, i+1], with the help of the parameter_at_point(i) API, this will result in a continuous function with discontinuous derivative.   
  
Hence, what is need is a smooth function s = g(t) which goes from [0, n-1] to [0, cr.max_parameter()], so that g(i) = cr.parameter_at_point(i), and then f(t) = cr(g(t)) would be the smooth interpolator.

---

## Comment 11

> Username: NAThompson  
> Created at: 2021-09-29 23:17:10 UTC  
> Url: https://github.com/boostorg/math/issues/211#issuecomment-930614622  

Hmm, I might start by trying to prove nonexistence rather than try to exhibit a function with those properties. e.g., three points on a line must have a unique C^1[0,2] parametrization, independent of where P[1] is on that line. Namely: f(t) = (2-t)p0 + t/2 p2. But the only p1 that satisfies your requirement is p1 = p0 + p2/2.  
  
I do suspect you can get [geometric continuity](https://en.wikipedia.org/wiki/Smoothness#Geometric_continuity) though.

---

## Comment 12

> Username: oleg-alexandrov  
> Created at: 2021-09-30 00:19:12 UTC  
> Updated at: 2021-09-30 02:31:38 UTC  
> Url: https://github.com/boostorg/math/issues/211#issuecomment-930640912  

Actually, inventing a monotonically increasing smooth function g(t) so that g(i) = cr.parameter_at_point(i) is straightforward. It won't be a linear function though.   
  
I must say that I agree with you, however, that what I asked for does not come naturally to Catmull-Rom splines. The lengths alpha_distance<Point>(m_pnts[i], m_pnts[i-1], alpha) are simply not equal to each other for all i, and one has to respect that.   
  
Only the special case alpha = 0, when all segments are equal to 1 will result in the kind of interpolation I need. I know in this case the spline may have self-intersections, but that likely happens only when the data is a bit contrived, rather than being samples of a smooth and slowly varying function. At least that's what I think based on this: http://www.cemyuksel.com/research/catmullrom_param/catmullrom.pdf
