# #518 - Cubic roots [Closed]

> Username: NAThompson  
> Created at: 2021-02-03 17:19:49 UTC  
> Updated at: 2022-01-03 17:57:48 UTC  
> Closed at: 2022-01-03 17:57:48 UTC  
> Url: https://github.com/boostorg/math/issues/518  

The following code recovers the real roots of a cubic:  
  
```cpp  
template <typename Real> int sgn(Real val) {  
    return (Real(0) < val) - (val < Real(0));  
}  
  
// Solves ax³ + bx² + cx + d = 0.  
// Only returns the real roots, as these are the only roots of interest in ray intersection problems.  
// Follows Numerical Recipes, Chapter 5, section 6.  
template<typename Real>  
std::vector<Real> cubic_roots(Real a, Real b, Real c, Real d) {  
    using std::sqrt;  
    using std::acos;  
    using std::cos;  
    using std::cbrt;  
    if (a == 0) {  
        return quadratic_roots(b, c, d);  
    }  
    Real roots(3, std::numeric_limits<Real>::quiet_NaN());  
    Real p = b/a;  
    Real q = c/a;  
    Real r = d/a;  
    Real Q = (p*p - 3*q)/9;  
    Real R = (2*p*p*p - 3*p*q + 27*r)/54;  
    if (R*R < Q*Q*Q) {  
        Real rtQ = sqrt(Q);  
        Real theta = acos(R/(Q*rtQ));  
        roots[0] = -2*rtQ*cos(theta/3) - p/3;  
        roots[1] = -2*rtQ*cos((theta + 2*M_PI)/3) - p/3;  
        roots[2] = -2*rtQ*cos((theta - 2*M_PI)/3) - p/3;  
        return roots;  
    }  
    // There is only 1 real root:  
    roots.resize(1);  
    Real arg = R*R - Q*Q*Q;  
    Real A = -sgn(R)*cbrt(abs(R) + sqrt(arg));  
    if (A == 0) {  
        roots[0] = A - a/3;  
    } else {  
        roots[0] = A + Q/A - a/3;  
    }  
    return roots;  
}  
```  
  
Is this interesting enough to be added the library? If so, it has a problem in that it very naturally seems to have a different design that the `quadratic_roots` code, which returns a `std::pair`.  
  
Note: It appears a better algorithm [exists](https://dl.acm.org/doi/pdf/10.1145/2699468).

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-02-03 18:30:38 UTC  
> Url: https://github.com/boostorg/math/issues/518#issuecomment-772724797  

Why not return a tuple for consistency?

---

## Comment 2

> Username: NAThompson  
> Created at: 2021-02-03 20:36:43 UTC  
> Url: https://github.com/boostorg/math/issues/518#issuecomment-772805739  

Well, I had an idea to only return real roots for real coefficients. For quadratics the tuple works well for this, but for cubics, that's a bit more awkward because there's always one real root, but could be 3.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-02-05 19:18:03 UTC  
> Url: https://github.com/boostorg/math/issues/518#issuecomment-774235543  

Isn't this an issue for quadratics as well where there could be 0,1 or 2 real valued roots?

---

## Comment 4

> Username: NAThompson  
> Created at: 2021-02-05 20:28:06 UTC  
> Url: https://github.com/boostorg/math/issues/518#issuecomment-774270525  

Not for quadratics with real coefficients, it's either zero or two. But point taken-I think the return of a pair with nans in the complex root case was somewhat of a bad design.

---

## Comment 5

> Username: NAThompson  
> Created at: 2021-02-06 17:44:24 UTC  
> Updated at: 2021-02-07 15:21:56 UTC  
> Url: https://github.com/boostorg/math/issues/518#issuecomment-774513773  

Here's a debugged cubic and quartic roots solver:  
  
```  
template <typename Real> int sgn(Real val) {  
    return (Real(0) < val) - (val < Real(0));  
}  
  
// Solves ax³ + bx² + cx + d = 0.  
// Only returns the real roots, as these are the only roots of interest in ray intersection problems.  
// Follows Numerical Recipes, Chapter 5, section 6.  
template<typename Real>  
std::vector<Real> cubic_roots(Real a, Real b, Real c, Real d) {  
    using std::sqrt;  
    using std::acos;  
    using std::cos;  
    using std::cbrt;  
    using std::abs;  
    if (abs(a) <= std::numeric_limits<Real>::min()) {  
        return quadratic_roots(b, c, d);  
    }  
    if (abs(d) <= std::numeric_limits<Real>::min()) {  
        auto v = quadratic_roots(a, b, c);  
        v.push_back(0);  
        std::sort(v.begin(), v.end());  
        return v;  
    }  
    std::vector<Real> roots(3, std::numeric_limits<Real>::quiet_NaN());  
    Real p = b/a;  
    Real q = c/a;  
    Real r = d/a;  
    Real Q = (p*p - 3*q)/9;  
    Real R = (2*p*p*p - 9*p*q + 27*r)/54;  
    if (R*R < Q*Q*Q) {  
        Real rtQ = sqrt(Q);  
        Real theta = acos(R/(Q*rtQ));  
        roots[0] = -2*rtQ*cos(theta/3) - p/3;  
        roots[1] = -2*rtQ*cos((theta + 2*M_PI)/3) - p/3;  
        roots[2] = -2*rtQ*cos((theta - 2*M_PI)/3) - p/3;  
        std::sort(roots.begin(), roots.end());  
        return roots;  
    }  
    // There is only 1 real root:  
    roots.resize(1);  
    Real arg = R*R - Q*Q*Q;  
    Real A = -sgn(R)*cbrt(abs(R) + sqrt(arg));  
    if (abs(A) <= std::numeric_limits<Real>::min()) {  
        roots[0] = A - p/3;  
    } else {  
        roots[0] = A + Q/A - p/3;  
    }  
    return roots;  
}  
  
  
// Solves ax⁴ + bx³ + cx² + dx + e = 0.  
// Only returns the real roots, as these are the only roots of interest in ray intersection problems.  
// Follows Graphics Gems V: https://github.com/erich666/GraphicsGems/blob/master/gems/Roots3And4.c  
template<typename Real>  
std::vector<Real> quartic_roots(Real a, Real b, Real c, Real d, Real e) {  
    using std::abs;  
    using std::sqrt;  
    if (std::abs(a) <= std::numeric_limits<Real>::min()) {  
        return cubic_roots(b, c, d, e);  
    }  
    if (std::abs(e) <= std::numeric_limits<Real>::min()) {  
        auto v = cubic_roots(a, b, c, d);  
        v.push_back(Real(0));  
        return v;  
    }  
    // Now solve x⁴ + Ax³ + Bx² + Cx + D = 0.  
    Real A = b/a;  
    Real B = c/a;  
    Real C = d/a;  
    Real D = e/a;  
    Real Asq = A*A;  
    // Let x = y - A/4:  
    // Mathematica: Expand[(y - A/4)^4 + A*(y - A/4)^3 + B*(y - A/4)^2 + C*(y - A/4) + D]  
    // We now solve y⁴ + py² + qy + r = 0.  
    Real p = B - 3*Asq/8;  
    Real q = C - A*B/2 + Asq*A/8;  
    Real r = D - A*C/4 + Asq*B/16 - 3*Asq*Asq/256;  
    if (std::abs(r) <= std::numeric_limits<Real>::min()) {  
        auto v = cubic_roots(Real(1), Real(0), p, q);  
        v.push_back(Real(0));  
        for (auto & y : v) {  
            y -= A/4;  
        }  
        std::sort(v.begin(), v.end());  
        return v;  
    }  
    // Biquadratic case:  
    if (std::abs(q) <= std::numeric_limits<Real>::min()) {  
        auto w = quadratic_roots(Real(1), p, r);  
        std::vector<Real> v;  
        for (auto r : w) {  
            if (r >= 0) {  
                Real rtr = sqrt(r);  
                v.push_back(rtr - A/4);  
                v.push_back(-rtr - A/4);  
            }  
        }  
        std::sort(v.begin(), v.end());  
        return v;  
    }  
  
    // Now split the depressed cubic into two quadratics:  
    // y⁴ + py² + qy + r = (y² + sy + u)(y² - sy + v) = y⁴ + (v+u-s²)y² + s(v - u)y + uv  
    // So p = v+u-s², q = s(v - u), r = uv.  
    // Then (v+u)² - (v-u)² = 4uv = 4r = (p+s²)² - q²/s².  
    // Multiply through by s² to get s²(p+s²)² - q² - 4rs² = 0, which is a cubic in s².  
    // Then we let z = s², to get  
    // z³ + 2pz² + (p² - 4r)z - q² = 0.  
    auto z_roots = cubic_roots(Real(1), 2*p, p*p - 4*r, -q*q);  
    // z = s², so s = sqrt(z).  
    // No real roots:  
    if (z_roots.back() <= 0) {  
        std::vector<Real> v(0);  
        return v;  
    }  
    Real s = std::sqrt(z_roots.back());  
  
    // s is nonzero, because we took care of the biquadratic case.  
    Real v = (p + s*s + q/s)/2;  
    Real u = v - q/s;  
    // Now solve y² + sy + u = 0:  
    auto roots1 = quadratic_roots(Real(1), s, u);  
  
    // Now solve y² - sy + v = 0:  
    auto roots2 = quadratic_roots(Real(1), -s, v);  
    for (auto root : roots2) {  
        roots1.push_back(root);  
    }  
  
    for (auto& r : roots1) {  
        r -= A/4;  
    }  
  
    // This is not super accurate. Clean up the roots with a Halley iterate.  
    for (auto &r : roots1) {  
        Real df = 4*a*r + 3*b;  
        df = df*r + 2*c;  
        df = df*r + d;  
        Real d2f = 12*a*r + 6*b;  
        d2f = d2f*r + 2*c;  
        Real f = a*r + b;  
        f = f*r + c;  
        f = f*r + d;  
        f = f*r + e;  
        Real denom = 2*df*df - f*d2f;  
        if (std::abs(denom) > std::numeric_limits<Real>::min())  
        {  
            r -= 2*f*df/denom;  
        }  
    }  
  
    std::sort(roots1.begin(), roots1.end());  
    return roots1;  
}  
```

---

## Comment 6

> Username: NAThompson  
> Created at: 2022-01-03 17:57:48 UTC  
> Url: https://github.com/boostorg/math/issues/518#issuecomment-1004257986  

Finished.
