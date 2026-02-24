# #366 First cut at new rational_adaptor. [Merged]

> Username: jzmaddock  
> Created at: 2021-09-02 09:55:06 UTC  
> Updated at: 2021-10-04 15:54:05 UTC  
> Merged at: 2021-10-04 11:29:59 UTC  
> Closed at: 2021-10-04 11:29:59 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366  

This is a first cut at a new rational_adaptor that doesn't use Boost.Rational and is better optimised for arbitrary precision integers.  It passes our current tests, but more tests are required.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-09-07 08:39:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#issuecomment-914108120  

If anyone (@ckormanyos ?) can take a look and review/double check this I'd really appreciate it.  This removes the dependency to Boost.Rational.  I still need to run some performance comparisons, but other than that it should be ready to go now.

---

## Review 2 [Commented]

> Username: NAThompson  
> Created_at: 2021-09-07 16:43:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/366#pullrequestreview-748195032  

📁 include/boost/multiprecision/rational_adaptor.hpp

```diff
   7 |- #define BOOST_MATH_RATIONAL_ADAPTER_HPP
   6 |+ #ifndef BOOST_MATH_SKELETON_BACKEND_HPP
   7 |+ #define BOOST_MATH_SKELETON_BACKEND_HPP
```

> Username: NAThompson  
> Created_at: 2021-09-07 16:43:41 UTC  
> Updated_at: 2021-09-07 16:43:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#discussion_r703672231  

Doesn't match the filename?

> Username: jzmaddock  
> Created_at: 2021-09-07 18:36:45 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#discussion_r703743105  

Good catch, will push that fix shortly.


---

## Review 3 [Commented]

> Username: NAThompson  
> Created_at: 2021-09-07 16:44:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/366#pullrequestreview-748195497  

📁 include/boost/multiprecision/rational_adaptor.hpp

```diff
  21 |- #pragma warning(pop)
  22 |- #endif
  10 |+ #include <boost/container_hash/hash.hpp>
```

> Username: NAThompson  
> Created_at: 2021-09-07 16:44:12 UTC  
> Updated_at: 2021-09-07 16:44:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#discussion_r703672556  

So this is just one step along the way to a standalone?

> Username: jzmaddock  
> Created_at: 2021-09-07 18:39:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#discussion_r703744483  

Baby steps yes, but as it happens that #include can be removed now :)


---

## Review 4 [Commented]

> Username: NAThompson  
> Created_at: 2021-09-07 16:45:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/366#pullrequestreview-748196248  

📁 include/boost/multiprecision/rational_adaptor.hpp

```diff
 167 |    }
 160 |-    void negate()
 168 |+    void        negate()
```

> Username: NAThompson  
> Created_at: 2021-09-07 16:45:02 UTC  
> Updated_at: 2021-09-07 16:45:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#discussion_r703673089  

looks like a weird whitespace thing happened here . . .

> Username: jzmaddock  
> Created_at: 2021-09-07 18:41:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#discussion_r703745703  

Grrr, editor "magic" going on there, will fix, thanks!


---

## Review 5 [Commented]

> Username: NAThompson  
> Created_at: 2021-09-07 16:48:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/366#pullrequestreview-748199275  

📁 include/boost/multiprecision/rational_adaptor.hpp

```diff
 954 |+ // Multiply and add/subtract as one:
 955 |+ //
 956 |+ #if 0
```

> Username: NAThompson  
> Created_at: 2021-09-07 16:48:27 UTC  
> Updated_at: 2021-09-07 16:48:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#discussion_r703675420  

Don't wanna just remove these?

> Username: jzmaddock  
> Created_at: 2021-09-07 18:42:34 UTC  
> Updated_at: 2021-09-07 18:42:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#discussion_r703746477  

I'm conflicted, there is a simplified formula for multiply&add of rationals, so I might yet implement these....


---

## Comment 6

> Username: ckormanyos  
> Created_at: 2021-09-07 17:37:00 UTC  
> Updated_at: 2021-09-07 17:39:24 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#issuecomment-914495235  

>  anyone (@ckormanyos ?) can take a look and review/double  
  
Thank you , John for diving into this topic and trying to remove the rational dependency --- a great step toward potential standalone.  
  
- In lines  
[like these](https://github.com/boostorg/multiprecision/blob/1b31fbea21bcf5b5b1e7d5ef4a8870aabed2161e/include/boost/multiprecision/mpc.hpp#L398), I have gotten into the habit of setting the default parameter to `nullptr` instead of `0`. This is really just a matter of style and I'm not sure if/or when this should/could be done consistently. To me `nullptr`makes more sense.  
- When I saw [this line](https://github.com/boostorg/multiprecision/blob/1b31fbea21bcf5b5b1e7d5ef4a8870aabed2161e/include/boost/multiprecision/rational_adaptor.hpp#L214), I wondered if `std::is_arithmetic` would confidently capture all the necessary cases?  
- This [preprocessor line](https://github.com/boostorg/multiprecision/blob/1b31fbea21bcf5b5b1e7d5ef4a8870aabed2161e/include/boost/multiprecision/cpp_int/misc.hpp#L419) is always catching the `0`, as has also been pointed out above

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2021-09-07 18:49:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#issuecomment-914542675  

>I have gotten into the habit of setting the default parameter to nullptr instead of 0. This is really just a matter of style and I'm not sure if/or when this should/could be done consistently. To me nullptrmakes more sense.  
  
Will fix.  
  
>When I saw this line, I wondered if std::is_arithmetic would confidently capture all the necessary cases?  
  
The definition in here: https://github.com/boostorg/multiprecision/blob/1b31fbea21bcf5b5b1e7d5ef4a8870aabed2161e/include/boost/multiprecision/traits/std_integer_traits.hpp is almost the same as `std::is_arithmetic` but adds consistent support for __Int128 etc.  Ironically, this workaround was only needed because we switched from boost type_traits to the std ones!  
  
> This preprocessor line is always catching the 0, as has also been pointed out above  
  
Not good, good catch, will fix shortly.

---

## Comment 8

> Username: afabri  
> Created_at: 2021-09-09 14:56:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#issuecomment-916177087  

At the end of arithmetic operations, instead of simplifying by dividing with the gcd,  the gcd function is called several times to simplify intermediate expressions. Does anybody know if that pays off for all sizes of integers or if there is a threshold.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2021-09-10 18:39:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#issuecomment-917126573  

>At the end of arithmetic operations, instead of simplifying by dividing with the gcd, the gcd function is called several times to simplify intermediate expressions. Does anybody know if that pays off for all sizes of integers or if there is a threshold.  
  
Good question, I'll experiment.  
  
In the mean time, here's the preliminary performance comparisons, mostly the new code does OK, but there are a couple of strange outliers I need to look into:  
  
<html><body>  
<!--StartFragment--><p class="title"><b>Table 1.72. Operator *</b></p>  
<div class="table-contents">  
  
Backend | 128 Bits | 256 Bits | 512 Bits | 1024 Bits  
-- | -- | -- | -- | --  
cpp_rational | 1.10175 (1.29208s) | 1.17481 (3.01313s) | 1.26352 (6.78634s) | 1.37821 (16.0559s)  
cpp_rational(develop) | 1.18315 (1.4364s) | 1.39656 (3.60312s) | 1.40385 (7.58984s) | 1.41806 (17.0363s)  
mpq_rational | 1 (1.17276s) | 1 (2.56478s) | 1 (5.37097s) | 1 (11.6499s)  
  
</div><!--EndFragment-->  
</body>  
</html>  
  
<html><body>  
<!--StartFragment--><p class="title"><b>Table 1.73. Operator *(int)</b></p>  
<div class="table-contents">  
  
Backend | 128 Bits | 256 Bits | 512 Bits | 1024 Bits  
-- | -- | -- | -- | --  
cpp_rational | 1 (0.0440046s) | 1 (0.0849735s) | 1 (0.148665s) | 1 (0.278337s)  
cpp_rational(develop) | [4.1] (0.181914s) | 5.6 (0.387531s) | 2.9 (0.573863s) | 3.12 (0.870819s)  
mpq_rational | 7.87033 (0.346331s) | 5.60552 (0.476321s) | 2.71581 (0.403744s) | 1.66801 (0.464269s)  
  
</div><!--EndFragment-->  
</body>  
</html>  
  
<html><body>  
<!--StartFragment--><p class="title"><b>Table 1.74. Operator *(unsigned long long)</b></p>  
<div class="table-contents">  
  
Backend | 128 Bits | 256 Bits | 512 Bits | 1024 Bits  
-- | -- | -- | -- | --  
cpp_rational | 1 (0.245235s) | 1 (0.482828s) | 1.16943 (0.893633s) | 2.11565 (1.67612s)  
cpp_rational(develop) | 1.8 (0.447167s) | 1.7 (0.844429s) | 1.90081 (1.35896s) | 2.97039 (2.37626s)  
mpq_rational | 2.72477 (0.668207s) | 1.42855 (0.689746s) | 1 (0.764158s) | 1 (0.792249s)  
  
</div><!--EndFragment-->  
</body>  
</html>  
  
<html><body>  
<!--StartFragment--><p class="title"><b>Table 1.75. Operator *=(unsigned long long)</b></p>  
<div class="table-contents">  
  
Backend | 128 Bits | 256 Bits | 512 Bits | 1024 Bits  
-- | -- | -- | -- | --  
cpp_rational | 1 (0.29657s) | 1 (0.549148s) | 1.2823 (0.96603s) | 2.38235 (1.9245s)  
cpp_rational(develop) | 1.45 (0.435778s) | 1.23469 (0.847931s) | 2.20072 (1.57936s) | 3.02908 (2.41011s)  
mpq_rational | 2.28287 (0.677029s) | 1.24615 (0.684319s) | 1 (0.753355s) | 1 (0.807814s)  
  
</div><!--EndFragment-->  
</body>  
</html>  
  
<html><body>  
<!--StartFragment--><p class="title"><b>Table 1.76. Operator +</b></p>  
<div class="table-contents">  
  
Backend | 128 Bits | 256 Bits | 512 Bits | 1024 Bits  
-- | -- | -- | -- | --  
cpp_rational | 1.49938 (0.784278s) | 1.34743 (1.6984s) | 1.37717 (3.84149s) | 1.41546 (9.19853s)  
cpp_rational(develop) | 2.17171 (1.18719s) | 1.91454 (2.43372s) | 1.69452 (4.78894s) | 1.64027 (10.7223s)  
mpq_rational | 1 (0.52307s) | 1 (1.26047s) | 1 (2.78941s) | 1 (6.49862s)  
  
</div><!--EndFragment-->  
</body>  
</html>  
  
<html><body>  
<!--StartFragment--><p class="title"><b>Table 1.77. Operator +(int)</b></p>  
<div class="table-contents">  
  
Backend | 128 Bits | 256 Bits | 512 Bits | 1024 Bits  
-- | -- | -- | -- | --  
cpp_rational | 5.57552 (0.641649s) | 10.796 (1.46357s) | 20.894 (3.25673s) | 35.3507 (7.36671s)  
cpp_rational(develop) | 1.81035 (0.212141s) | 3.50398 (0.443488s) | 3.88847 (0.733996s) | 4.12376 (0.902819s)  
mpq_rational | 1 (0.115083s) | 1 (0.135566s) | 1 (0.155869s) | 1 (0.208389s)  
  
</div><!--EndFragment-->  
</body>  
</html>  
  
<html><body>  
<!--StartFragment--><p class="title"><b>Table 1.78. Operator +(unsigned long long)</b></p>  
<div class="table-contents">  
  
Backend | 128 Bits | 256 Bits | 512 Bits | 1024 Bits  
-- | -- | -- | -- | --  
cpp_rational | 2.29109 (0.681809s) | 4.62899 (1.50387s) | 8.4593 (3.33481s) | 21.2893 (8.21041s)  
cpp_rational(develop) | 1.14587 (0.331196s) | 1.59063 (0.467973s) | 1.95391 (0.650182s) | 2.52932 (0.971168s)  
mpq_rational | 1 (0.297591s) | 1 (0.324881s) | 1 (0.394219s) | 1 (0.385659s)  
  
</div><!--EndFragment-->  
</body>  
</html>  
  
<html><body>  
<!--StartFragment--><p class="title"><b>Table 1.79. Operator +=(unsigned long long)</b></p>  
<div class="table-contents">  
  
Backend | 128 Bits | 256 Bits | 512 Bits | 1024 Bits  
-- | -- | -- | -- | --  
cpp_rational | 1 (0.0627029s) | 1 (0.0695193s) | 1 (0.0885983s) | 1 (0.119314s)  
cpp_rational(develop) | 1.04679 (0.305264s) | 1.48998 (0.453898s) | 1.95382 (0.64145s) | 2.2881 (0.974988s)  
mpq_rational | 4.63843 (0.290843s) | 4.47252 (0.310927s) | 3.82819 (0.339171s) | 3.40139 (0.405833s)  
  
</div><!--EndFragment-->  
</body>  
</html>  
  
<html><body>  
<!--StartFragment--><p class="title"><b>Table 1.80. Operator -</b></p>  
<div class="table-contents">  
  
Backend | 128 Bits | 256 Bits | 512 Bits | 1024 Bits  
-- | -- | -- | -- | --  
cpp_rational | 1.4821 (0.781748s) | 1.34429 (1.7058s) | 1.37777 (3.89057s) | 1.41853 (9.23662s)  
cpp_rational(develop) | 1.92786 (1.05892s) | 1.88654 (2.40996s) | 1.7012 (4.8025s) | 1.61842 (10.7252s)  
mpq_rational | 1 (0.52746s) | 1 (1.26893s) | 1 (2.82382s) | 1 (6.51138s)  
  
</div><!--EndFragment-->  
</body>  
</html>  
  
<html><body>  
<!--StartFragment--><p class="title"><b>Table 1.81. Operator -(int)</b></p>  
<div class="table-contents">  
  
Backend | 128 Bits | 256 Bits | 512 Bits | 1024 Bits  
-- | -- | -- | -- | --  
cpp_rational | 5.3698 (0.638218s) | 8.3919 (1.46922s) | 20.5619 (3.25605s) | 34.2709 (7.42821s)  
cpp_rational(develop) | 1.47683 (0.19s) | 3.42541 (0.440702s) | 3.26896 (0.675656s) | 3.98653 (0.917965s)  
mpq_rational | 1 (0.118853s) | 1 (0.175076s) | 1 (0.158353s) | 1 (0.21675s)  
  
</div><!--EndFragment-->  
</body>  
</html>  
  
<html><body>  
<!--StartFragment--><p class="title"><b>Table 1.82. Operator -(unsigned long long)</b></p>  
<div class="table-contents">  
  
Backend | 128 Bits | 256 Bits | 512 Bits | 1024 Bits  
-- | -- | -- | -- | --  
cpp_rational | 2.28341 (0.680618s) | 4.66372 (1.4809s) | 8.5964 (3.26282s) | 19.2239 (7.52569s)  
cpp_rational(develop) | 1.08424 (0.330445s) | 1.55894 (0.4649s) | 2.03842 (0.673936s) | 2.60596 (1.03343s)  
mpq_rational | 1 (0.298071s) | 1 (0.317537s) | 1 (0.379557s) | 1 (0.391477s)  
  
</div><!--EndFragment-->  
</body>  
</html>  
  
<html><body>  
<!--StartFragment--><p class="title"><b>Table 1.83. Operator -=(unsigned long long)</b></p>  
<div class="table-contents">  
  
Backend | 128 Bits | 256 Bits | 512 Bits | 1024 Bits  
-- | -- | -- | -- | --  
cpp_rational | 1 (0.0616676s) | 1 (0.0716217s) | 1 (0.0915789s) | 1 (0.123615s)  
cpp_rational(develop) | 1.11705 (0.321743s) | 1.5533 (0.471277s) | 1.99514 (0.669887s) | 2.5145 (0.994094s)  
mpq_rational | 4.84987 (0.29908s) | 4.31996 (0.309403s) | 3.79471 (0.347516s) | 3.25761 (0.402688s)  
  
</div><!--EndFragment-->  
</body>  
</html>  
  
<html><body>  
<!--StartFragment--><p class="title"><b>Table 1.84. Operator /</b></p>  
<div class="table-contents">  
  
Backend | 128 Bits | 256 Bits | 512 Bits | 1024 Bits  
-- | -- | -- | -- | --  
cpp_rational | 1.34343 (3.80757s) | 1.3953 (7.11993s) | 1.46997 (15.1096s) | 1.65132 (34.8349s)  
cpp_rational(develop) | 1.3776 (3.98234s) | 1.61088 (8.19393s) | 1.62661 (17.2913s) | 1.73601 (37.1927s)  
mpq_rational | 1 (2.83421s) | 1 (5.10279s) | 1 (10.2789s) | 1 (21.0952s)  
  
</div><!--EndFragment-->  
</body>  
</html>  
  
<html><body>  
<!--StartFragment--><p class="title"><b>Table 1.85. Operator /(int)</b></p>  
<div class="table-contents">  
  
Backend | 128 Bits | 256 Bits | 512 Bits | 1024 Bits  
-- | -- | -- | -- | --  
cpp_rational | 1 (0.0450395s) | 1 (0.0887703s) | 1 (0.145568s) | 1 (0.26773s)  
cpp_rational(develop) | 4.122 (0.185692s) | 4.39 (0.389876s) | 3.9 (0.574013s) | 3.2 (0.866507s)  
mpq_rational | 9.33538 (0.420461s) | 6.45783 (0.573264s) | 3.99751 (0.581909s) | 2.04621 (0.547832s)  
  
</div><!--EndFragment-->  
</body>  
</html>  
  
<html><body>  
<!--StartFragment--><p class="title"><b>Table 1.86. Operator /(unsigned long long)</b></p>  
<div class="table-contents">  
  
Backend | 128 Bits | 256 Bits | 512 Bits | 1024 Bits  
-- | -- | -- | -- | --  
cpp_rational | 1 (0.248277s) | 1 (0.477218s) | 1.11799 (0.900395s) | 1.77994 (1.70253s)  
cpp_rational(develop) | 1.81 (0.449759s) | 1.77 (0.846367s) | 1.71944 (1.3685s) | 2.40262 (2.38789s)  
mpq_rational | 3.01522 (0.74861s) | 1.60033 (0.763707s) | 1 (0.80537s) | 1 (0.956506s)  
  
</div><!--EndFragment-->  
</body>  
</html>  
  
<html><body>  
<!--StartFragment--><p class="title"><b>Table 1.87. Operator /=(unsigned long long)</b></p>  
<div class="table-contents">  
  
Backend | 128 Bits | 256 Bits | 512 Bits | 1024 Bits  
-- | -- | -- | -- | --  
cpp_rational | 1 (0.293623s) | 1 (0.5531s) | 1.10816 (0.964113s) | 2.15991 (1.90076s)  
cpp_rational(develop) | 1.48 (0.4348s) | 1.52 (0.839948s) | 1.71274 (1.41427s) | 2.66951 (2.37585s)  
mpq_rational | 2.54365 (0.746874s) | 1.39243 (0.770152s) | 1 (0.870013s) | 1 (0.880021s)  
  
</div><!--EndFragment-->  
</body>  
</html>  
  
<html><body>  
<!--StartFragment--><p class="title"><b>Table 1.88. Operator construct</b></p>  
<div class="table-contents">  
  
Backend | 128 Bits | 256 Bits | 512 Bits | 1024 Bits  
-- | -- | -- | -- | --  
cpp_rational | 1.6 (0.0073899s) | 1.8 (0.0077339s) | 1.46 (0.0073439s) | 1.65 (0.0075128s)  
cpp_rational(develop) | 1 (0.0045752s) | 1 (0.0042987s) | 1 (0.0050471s) | 1 (0.0045598s)  
mpq_rational | 19.6985 (0.14557s) | 25.3958 (0.196408s) | 25.4413 (0.186838s) | 18.6016 (0.13975s)  
  
</div><!--EndFragment-->  
</body>  
</html>  
  
<html><body>  
<!--StartFragment--><p class="title"><b>Table 1.89. Operator construct(unsigned long long)</b></p>  
<div class="table-contents">  
  
Backend | 128 Bits | 256 Bits | 512 Bits | 1024 Bits  
-- | -- | -- | -- | --  
cpp_rational | 1 (0.0041005s) | 1 (0.0042113s) | 1 (0.004049s) | 1 (0.004131s)  
cpp_rational(develop) | 2.66 (0.0109136s) | 2.7 (0.0115913s) | 2.66 (0.0107292s) | 2.47 (0.0102217s)  
mpq_rational | 93.727 (0.384328s) | 107.33 (0.452s) | 114.492 (0.463578s) | 91.5304 (0.378112s)  
  
</div><!--EndFragment-->  
</body>  
</html>  
  
<html><body>  
<!--StartFragment--><p class="title"><b>Table 1.90. Operator construct(unsigned)</b></p>  
<div class="table-contents">  
  
Backend | 128 Bits | 256 Bits | 512 Bits | 1024 Bits  
-- | -- | -- | -- | --  
cpp_rational | 1 (0.0038144s) | 1 (0.0040615s) | 1 (0.0040119s) | 1 (0.0043592s)  
cpp_rational(develop) | 2.8 (0.01064s) | 2.7 (0.0108306s) | 2.7 (0.0107739s) | 2.6 (0.0112458s)  
mpq_rational | 37.6055 (0.143443s) | 43.7209 (0.177572s) | 45.7938 (0.18372s) | 30.8193 (0.134348s)  
  
</div><!--EndFragment-->  
</body>  
</html>  
  
<html><body>  
<!--StartFragment--><p class="title"><b>Table 1.91. Operator str</b></p>  
<div class="table-contents">  
  
Backend | 128 Bits | 256 Bits | 512 Bits | 1024 Bits  
-- | -- | -- | -- | --  
cpp_rational | 2.71214 (0.001483s) | 2.99688 (0.0037521s) | 4.5722 (0.0085148s) | 5.01046 (0.0215971s)  
cpp_rational | 5.79066 (0.0033111s) | 4.84023 (0.0046534s) | 4.92879 (0.0105407s) | 5.34843 (0.0224324s)  
mpq_rational | 1 (0.0005468s) | 1 (0.001252s) | 1 (0.0018623s) | 1 (0.0043104s)  
  
</div><!--EndFragment-->  
</body>  
</html>

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2021-09-10 19:19:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#issuecomment-917151674  

Update: there's a bug in the add-scalar routine that does an unnecessary gcd.  Testing fixes now.

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2021-09-11 09:33:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#issuecomment-917375800  

> At the end of arithmetic operations, instead of simplifying by dividing with the gcd, the gcd function is called several times to simplify intermediate expressions. Does anybody know if that pays off for all sizes of integers or if there is a threshold.  
  
Leaving aside experiments for a moment, there is a good theoretical reason for this: if we assume that gcd is O(N^2) then it is better to do an O(N) operation twice, than an O(2N) operation once, ie 2N^2 is better than (2N)^2.

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2021-09-11 18:08:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#issuecomment-917448779  

Much better for +- a scalar now:  
  
<html><body>  
<!--StartFragment--><p class="title"><b>Operator +(int)</b></p>  
<div class="table-contents">  
  
Backend | 128 Bits | 256 Bits | 512 Bits | 1024 Bits  
-- | -- | -- | -- | --  
cpp_rational | 1 (0.0147207s) | 1 (0.0151953s) | 1 (0.018852s) | 1 (0.0308647s)  
cpp_rational(develop) | 14.4 (0.212141s) | 29.2 (0.443488s) | 39.8 (0.733996s) | 29.2 (0.902819s)  
mpq_rational | 7.85834 (0.11568s) | 8.38258 (0.127376s) | 8.62888 (0.162672s) | 6.65762 (0.205485s)  
  
</div><!--EndFragment-->  
</body>  
</html>

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2021-09-11 19:20:40 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#issuecomment-917462600  

>At the end of arithmetic operations, instead of simplifying by dividing with the gcd, the gcd function is called several times to simplify intermediate expressions. Does anybody know if that pays off for all sizes of integers or if there is a threshold.  
  
I coded this up quickly for multiply/divide, and while the code is noticeably simpler, it's also slower at all the bit-counts I tested:  
  
|Backend|128 Bits|256 Bits|512 Bits|1024 Bits|  
|-------|--------|--------|--------|--------------------|  
|cpp_rational, 1x gcd|1.54878s|5.27546s)|8.08625s|19.7865s|  
|cpp_rational, 2x gcd|1.29208s|3.01313s|6.78634s|16.0559s|

---

## Comment 14

> Username: afabri  
> Created_at: 2021-09-11 20:09:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#issuecomment-917471206  

Thank you for the benchmarks. Could you put them in this PR or on gist.github.com?  
It might be interesting to also compare directly with [`gmpq_class`](https://gmplib.org/manual/C_002b_002b-Interface-General) and maybe even make a benchmark for an expression like `a = b * c + d * e`.

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2021-09-12 15:44:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#issuecomment-917660201  

> Thank you for the benchmarks. Could you put them in this PR or on gist.github.com?  
  
The benchmark program is libs/multiprecision/performance/performance_test.cpp (though this does need updating and documenting a bit better).  
  
For the benefit of future googlers, the alternative multiply divide code is:  
  
```  
template <class Backend>   
void eval_multiply_imp(rational_adaptor<Backend>& result, const rational_adaptor<Backend>& a, const Backend& b_num, const Backend& b_denom)  
{  
   using default_ops::eval_multiply;  
   using default_ops::eval_divide;  
   using default_ops::eval_gcd;  
   using default_ops::eval_get_sign;  
   using default_ops::eval_eq;  
  
   eval_multiply(result.num(), a.num(), b_num);  
   eval_multiply(result.denom(), a.denom(), b_denom);  
  
   Backend gcd;  
   eval_gcd(gcd, result.num(), result.denom());  
   if (!eval_eq(gcd, rational_adaptor<Backend>::one()))  
   {  
      Backend t;  
      eval_divide(t, result.num(), gcd);  
      result.num().swap(t);  
      eval_divide(t, result.denom(), gcd);  
      result.denom() = std::move(t);  
   }  
   //  
   // We may have b_denom negative if this is actually division, if so just correct things now:  
   //  
   if (eval_get_sign(b_denom) < 0)  
   {  
      result.num().negate();  
      result.denom().negate();  
   }  
}  
```

---

## Comment 16

> Username: jzmaddock  
> Created_at: 2021-09-12 17:19:24 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#issuecomment-917675866  

>It might be interesting to also compare directly with gmpq_class and maybe even make a benchmark for an expression like a = b * c + d * e.  
  
What I've been keeping my eye out for, but haven't found yet, is a nice example of a rational number sequence - not too crazy hard to compute, but hard enough to properly exercise the arithmetic operators.  Any one any ideas?

---

## Comment 17

> Username: ckormanyos  
> Created_at: 2021-09-12 17:31:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#issuecomment-917677571  

> nice example of a rational number sequence  
  
Bernoulli numbers. But they diverge rapidly.  
  
There are some really neat results [here](https://functions.wolfram.com/ZetaFunctionsandPolylogarithms/Zeta/03/02/). I wonder if something like `zeta(18)` would work out?

---

## Comment 18

> Username: ckormanyos  
> Created_at: 2021-09-12 18:54:32 UTC  
> Updated_at: 2021-09-12 18:58:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#issuecomment-917690336  

Consider 10 terms of the expansion of `zeta(18)` via `Table[1/(1 - (1/Prime[n]^18)), {n, 1, 10}]`. It looks really neat. Also consider `Product[1/(1 - (1/Prime[n]^18)), {n, 1, 10}]`  
  
The table is, for instance, [here](https://www.wolframalpha.com/input/?i=Table%5B1%2F%281+-+%281%2FPrime%5Bn%5D%5E18%29%29%2C+%7Bn%2C+1%2C+10%7D%5D)  
  
The result of the product is, I believe:  
  
```  
11920749906148903974622678329766235165644684303503019936856106078371960015175135631236820267948711321333682716658606986520111083984375 / 11920704401324278745505365929928231339092941389995016138294384892997997915658307322817405715785120303841876752017118685699841898110976  
```

---

## Comment 19

> Username: afabri  
> Created_at: 2021-09-12 19:18:00 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#issuecomment-917694362  

> a nice example of a rational number sequence  
  
What we have in our project (www.cgal,org)  are things like orientation tests for [plane/point](https://github.com/CGAL/cgal/blob/master/Cartesian_kernel/include/CGAL/predicates/kernel_ftC3.h#L116), or    [sphere/point ](https://github.com/CGAL/cgal/blob/master/Cartesian_kernel/include/CGAL/predicates/kernel_ftC3.h#L336), both computing essentially signs of [determinants ](https://github.com/CGAL/cgal/blob/master/Kernel_23/include/CGAL/determinant.h#L57) of  small matrices.

---

## Comment 20

> Username: jzmaddock  
> Created_at: 2021-09-15 18:36:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#issuecomment-920277826  

OK, there are a couple of new Google benchmark programs added, one calculates Bernoulli(m) using the Louis Saalschütz explicit definition, the other calculates zeta(18) using Chris' equivalence.  
  
These results were run on Windows, with the unoptimized "generic" MPIR library, I'd expect gmp to perform much better once architecture specific optimizations are enabled: if someone would like to run these on other platforms that would be cool.  
  
zeta[18] calculation:  
  
  
<BODY TEXT="#000000">  
<TABLE FRAME=VOID CELLSPACING=0 COLS=2 RULES=NONE BORDER=0>  
	<COLGROUP><COL WIDTH=402><COL WIDTH=86></COLGROUP>  
	<TBODY>  
		<TR>  
			<TD WIDTH=402 HEIGHT=17 ALIGN=LEFT>Type</TD>  
			<TD WIDTH=86 ALIGN=LEFT>Time(ns)</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=18 ALIGN=LEFT>BM_zeta18&lt;cpp_rational&gt;</TD>  
			<TD ALIGN=RIGHT SDVAL="54688" SDNUM="2057;">54688</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=18 ALIGN=LEFT>BM_zeta18&lt;mpq_rational&gt;</TD>  
			<TD ALIGN=RIGHT SDVAL="76730" SDNUM="2057;">76730</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=18 ALIGN=LEFT>BM_zeta18&lt;number&lt;rational_adaptor&lt;gmp_int&gt;&gt;&gt;</TD>  
			<TD ALIGN=RIGHT SDVAL="68011" SDNUM="2057;">68011</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=18 ALIGN=LEFT>BM_zeta18&lt;mpq_class,mpz_class&gt;</TD>  
			<TD ALIGN=RIGHT SDVAL="59375" SDNUM="2057;">59375</TD>  
		</TR>  
	</TBODY>  
</TABLE>  
  
This is a relatively trivial calculation where the numbers don't grow too large, I suspect cpp_int's better memory usage for small values is the main driver here, though mpq_class is doing rather well - I need to check to see if it's eluding some temporaries somewhere that rational_adaptor isn't.  
  
Bernoulli calculation is a rather sterner test of arithmetic performance, here's what I'm seeing, all the result except those for cpp_rational are relative to cpp_rational's performance, so smaller is better:  
  
  
<TABLE FRAME=VOID CELLSPACING=0 COLS=5 RULES=NONE BORDER=0>  
	<COLGROUP><COL WIDTH=86><COL WIDTH=86><COL WIDTH=86><COL WIDTH=167><COL WIDTH=86></COLGROUP>  
	<TBODY>  
		<TR>  
			<TD WIDTH=86 HEIGHT=18 ALIGN=LEFT>m</TD>  
			<TD WIDTH=86 ALIGN=LEFT>cpp_rational</TD>  
			<TD WIDTH=86 ALIGN=LEFT>mpq_rational</TD>  
			<TD WIDTH=167 ALIGN=LEFT>rational_adaptor&lt;gmp_int&gt;</TD>  
			<TD WIDTH=86 ALIGN=LEFT>mpq_class</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="10" SDNUM="2057;">10</TD>  
			<TD ALIGN=RIGHT SDVAL="40806" SDNUM="2057;">40806</TD>  
			<TD ALIGN=RIGHT SDVAL="4.23052982404548" SDNUM="2057;">4.230529824</TD>  
			<TD ALIGN=RIGHT SDVAL="4.71842376121159" SDNUM="2057;">4.7184237612</TD>  
			<TD ALIGN=RIGHT SDVAL="3.1453217664069" SDNUM="2057;">3.1453217664</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="12" SDNUM="2057;">12</TD>  
			<TD ALIGN=RIGHT SDVAL="59375" SDNUM="2057;">59375</TD>  
			<TD ALIGN=RIGHT SDVAL="4.13534315789474" SDNUM="2057;">4.1353431579</TD>  
			<TD ALIGN=RIGHT SDVAL="4.44058947368421" SDNUM="2057;">4.4405894737</TD>  
			<TD ALIGN=RIGHT SDVAL="3.17227789473684" SDNUM="2057;">3.1722778947</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="14" SDNUM="2057;">14</TD>  
			<TD ALIGN=RIGHT SDVAL="83702" SDNUM="2057;">83702</TD>  
			<TD ALIGN=RIGHT SDVAL="3.85083988435163" SDNUM="2057;">3.8508398844</TD>  
			<TD ALIGN=RIGHT SDVAL="4.4081145014456" SDNUM="2057;">4.4081145014</TD>  
			<TD ALIGN=RIGHT SDVAL="2.86677737688466" SDNUM="2057;">2.8667773769</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="16" SDNUM="2057;">16</TD>  
			<TD ALIGN=RIGHT SDVAL="106027" SDNUM="2057;">106027</TD>  
			<TD ALIGN=RIGHT SDVAL="3.90849500598904" SDNUM="2057;">3.908495006</TD>  
			<TD ALIGN=RIGHT SDVAL="4.4736718005791" SDNUM="2057;">4.4736718006</TD>  
			<TD ALIGN=RIGHT SDVAL="2.9605194903185" SDNUM="2057;">2.9605194903</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="18" SDNUM="2057;">18</TD>  
			<TD ALIGN=RIGHT SDVAL="147524" SDNUM="2057;">147524</TD>  
			<TD ALIGN=RIGHT SDVAL="3.59354410129877" SDNUM="2057;">3.5935441013</TD>  
			<TD ALIGN=RIGHT SDVAL="4.53921395840677" SDNUM="2057;">4.5392139584</TD>  
			<TD ALIGN=RIGHT SDVAL="2.70473278924107" SDNUM="2057;">2.7047327892</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="20" SDNUM="2057;">20</TD>  
			<TD ALIGN=RIGHT SDVAL="185904" SDNUM="2057;">185904</TD>  
			<TD ALIGN=RIGHT SDVAL="3.60209032618986" SDNUM="2057;">3.6020903262</TD>  
			<TD ALIGN=RIGHT SDVAL="4.27557233841122" SDNUM="2057;">4.2755723384</TD>  
			<TD ALIGN=RIGHT SDVAL="2.68956020311559" SDNUM="2057;">2.6895602031</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="22" SDNUM="2057;">22</TD>  
			<TD ALIGN=RIGHT SDVAL="245857" SDNUM="2057;">245857</TD>  
			<TD ALIGN=RIGHT SDVAL="3.33370617879499" SDNUM="2057;">3.3337061788</TD>  
			<TD ALIGN=RIGHT SDVAL="3.82850600145613" SDNUM="2057;">3.8285060015</TD>  
			<TD ALIGN=RIGHT SDVAL="2.61022057537512" SDNUM="2057;">2.6102205754</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="24" SDNUM="2057;">24</TD>  
			<TD ALIGN=RIGHT SDVAL="304813" SDNUM="2057;">304813</TD>  
			<TD ALIGN=RIGHT SDVAL="3.2252528599502" SDNUM="2057;">3.22525286</TD>  
			<TD ALIGN=RIGHT SDVAL="3.56995928651337" SDNUM="2057;">3.5699592865</TD>  
			<TD ALIGN=RIGHT SDVAL="2.51727780639277" SDNUM="2057;">2.5172778064</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="26" SDNUM="2057;">26</TD>  
			<TD ALIGN=RIGHT SDVAL="374930" SDNUM="2057;">374930</TD>  
			<TD ALIGN=RIGHT SDVAL="3.25581575227376" SDNUM="2057;">3.2558157523</TD>  
			<TD ALIGN=RIGHT SDVAL="3.43102979222788" SDNUM="2057;">3.4310297922</TD>  
			<TD ALIGN=RIGHT SDVAL="2.45471954764889" SDNUM="2057;">2.4547195476</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="28" SDNUM="2057;">28</TD>  
			<TD ALIGN=RIGHT SDVAL="464965" SDNUM="2057;">464965</TD>  
			<TD ALIGN=RIGHT SDVAL="3.03656834385384" SDNUM="2057;">3.0365683439</TD>  
			<TD ALIGN=RIGHT SDVAL="3.2254492273612" SDNUM="2057;">3.2254492274</TD>  
			<TD ALIGN=RIGHT SDVAL="2.41533663824159" SDNUM="2057;">2.4153366382</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="30" SDNUM="2057;">30</TD>  
			<TD ALIGN=RIGHT SDVAL="585938" SDNUM="2057;">585938</TD>  
			<TD ALIGN=RIGHT SDVAL="2.81736122251842" SDNUM="2057;">2.8173612225</TD>  
			<TD ALIGN=RIGHT SDVAL="2.94840068403142" SDNUM="2057;">2.948400684</TD>  
			<TD ALIGN=RIGHT SDVAL="2.14285470476399" SDNUM="2057;">2.1428547048</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="32" SDNUM="2057;">32</TD>  
			<TD ALIGN=RIGHT SDVAL="732422" SDNUM="2057;">732422</TD>  
			<TD ALIGN=RIGHT SDVAL="2.56838680432865" SDNUM="2057;">2.5683868043</TD>  
			<TD ALIGN=RIGHT SDVAL="2.78260756776831" SDNUM="2057;">2.7826075678</TD>  
			<TD ALIGN=RIGHT SDVAL="2.05622441707103" SDNUM="2057;">2.0562244171</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="34" SDNUM="2057;">34</TD>  
			<TD ALIGN=RIGHT SDVAL="889369" SDNUM="2057;">889369</TD>  
			<TD ALIGN=RIGHT SDVAL="2.41568797653168" SDNUM="2057;">2.4156879765</TD>  
			<TD ALIGN=RIGHT SDVAL="2.64410947536962" SDNUM="2057;">2.6441094754</TD>  
			<TD ALIGN=RIGHT SDVAL="1.94247831889801" SDNUM="2057;">1.9424783189</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="36" SDNUM="2057;">36</TD>  
			<TD ALIGN=RIGHT SDVAL="1074219" SDNUM="2057;">1074219</TD>  
			<TD ALIGN=RIGHT SDVAL="2.33766205959865" SDNUM="2057;">2.3376620596</TD>  
			<TD ALIGN=RIGHT SDVAL="2.39503397351937" SDNUM="2057;">2.3950339735</TD>  
			<TD ALIGN=RIGHT SDVAL="1.83280504254719" SDNUM="2057;">1.8328050425</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="38" SDNUM="2057;">38</TD>  
			<TD ALIGN=RIGHT SDVAL="1283482" SDNUM="2057;">1283482</TD>  
			<TD ALIGN=RIGHT SDVAL="2.20010487096819" SDNUM="2057;">2.200104871</TD>  
			<TD ALIGN=RIGHT SDVAL="2.37288173889466" SDNUM="2057;">2.3728817389</TD>  
			<TD ALIGN=RIGHT SDVAL="1.75076393747633" SDNUM="2057;">1.7507639375</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="40" SDNUM="2057;">40</TD>  
			<TD ALIGN=RIGHT SDVAL="1506024" SDNUM="2057;">1506024</TD>  
			<TD ALIGN=RIGHT SDVAL="2.09448388604697" SDNUM="2057;">2.094483886</TD>  
			<TD ALIGN=RIGHT SDVAL="2.28860297047059" SDNUM="2057;">2.2886029705</TD>  
			<TD ALIGN=RIGHT SDVAL="1.70446420508571" SDNUM="2057;">1.7044642051</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="42" SDNUM="2057;">42</TD>  
			<TD ALIGN=RIGHT SDVAL="1727580" SDNUM="2057;">1727580</TD>  
			<TD ALIGN=RIGHT SDVAL="2.0407975318075" SDNUM="2057;">2.0407975318</TD>  
			<TD ALIGN=RIGHT SDVAL="2.17647055418562" SDNUM="2057;">2.1764705542</TD>  
			<TD ALIGN=RIGHT SDVAL="1.72457599648063" SDNUM="2057;">1.7245759965</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="44" SDNUM="2057;">44</TD>  
			<TD ALIGN=RIGHT SDVAL="2083333" SDNUM="2057;">2083333</TD>  
			<TD ALIGN=RIGHT SDVAL="1.88547534167605" SDNUM="2057;">1.8854753417</TD>  
			<TD ALIGN=RIGHT SDVAL="1.96220959395354" SDNUM="2057;">1.962209594</TD>  
			<TD ALIGN=RIGHT SDVAL="1.61971849915496" SDNUM="2057;">1.6197184992</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="46" SDNUM="2057;">46</TD>  
			<TD ALIGN=RIGHT SDVAL="2299331" SDNUM="2057;">2299331</TD>  
			<TD ALIGN=RIGHT SDVAL="1.85330602683998" SDNUM="2057;">1.8533060268</TD>  
			<TD ALIGN=RIGHT SDVAL="2.00671151739354" SDNUM="2057;">2.0067115174</TD>  
			<TD ALIGN=RIGHT SDVAL="1.63526999809945" SDNUM="2057;">1.6352699981</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="48" SDNUM="2057;">48</TD>  
			<TD ALIGN=RIGHT SDVAL="2678571" SDNUM="2057;">2678571</TD>  
			<TD ALIGN=RIGHT SDVAL="1.80089532814325" SDNUM="2057;">1.8008953281</TD>  
			<TD ALIGN=RIGHT SDVAL="1.92500030800005" SDNUM="2057;">1.925000308</TD>  
			<TD ALIGN=RIGHT SDVAL="1.56007774294577" SDNUM="2057;">1.5600777429</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="50" SDNUM="2057;">50</TD>  
			<TD ALIGN=RIGHT SDVAL="3045551" SDNUM="2057;">3045551</TD>  
			<TD ALIGN=RIGHT SDVAL="1.74068304881448" SDNUM="2057;">1.7406830488</TD>  
			<TD ALIGN=RIGHT SDVAL="1.87810547254011" SDNUM="2057;">1.8781054725</TD>  
			<TD ALIGN=RIGHT SDVAL="1.51502765837775" SDNUM="2057;">1.5150276584</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="52" SDNUM="2057;">52</TD>  
			<TD ALIGN=RIGHT SDVAL="3216912" SDNUM="2057;">3216912</TD>  
			<TD ALIGN=RIGHT SDVAL="1.82142843820409" SDNUM="2057;">1.8214284382</TD>  
			<TD ALIGN=RIGHT SDVAL="1.95153053611662" SDNUM="2057;">1.9515305361</TD>  
			<TD ALIGN=RIGHT SDVAL="1.65142845063838" SDNUM="2057;">1.6514284506</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="54" SDNUM="2057;">54</TD>  
			<TD ALIGN=RIGHT SDVAL="3760027" SDNUM="2057;">3760027</TD>  
			<TD ALIGN=RIGHT SDVAL="1.7067459887921" SDNUM="2057;">1.7067459888</TD>  
			<TD ALIGN=RIGHT SDVAL="1.81805556183506" SDNUM="2057;">1.8180555618</TD>  
			<TD ALIGN=RIGHT SDVAL="1.5212300337205" SDNUM="2057;">1.5212300337</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="56" SDNUM="2057;">56</TD>  
			<TD ALIGN=RIGHT SDVAL="3997093" SDNUM="2057;">3997093</TD>  
			<TD ALIGN=RIGHT SDVAL="1.74512977306257" SDNUM="2057;">1.7451297731</TD>  
			<TD ALIGN=RIGHT SDVAL="1.82424251825014" SDNUM="2057;">1.8242425183</TD>  
			<TD ALIGN=RIGHT SDVAL="1.6055195613412" SDNUM="2057;">1.6055195613</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="58" SDNUM="2057;">58</TD>  
			<TD ALIGN=RIGHT SDVAL="4589844" SDNUM="2057;">4589844</TD>  
			<TD ALIGN=RIGHT SDVAL="1.66430253402948" SDNUM="2057;">1.664302534</TD>  
			<TD ALIGN=RIGHT SDVAL="1.7399525996962" SDNUM="2057;">1.7399525997</TD>  
			<TD ALIGN=RIGHT SDVAL="1.70212756686284" SDNUM="2057;">1.7021275669</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="60" SDNUM="2057;">60</TD>  
			<TD ALIGN=RIGHT SDVAL="5156250" SDNUM="2057;">5156250</TD>  
			<TD ALIGN=RIGHT SDVAL="1.61616155151515" SDNUM="2057;">1.6161615515</TD>  
			<TD ALIGN=RIGHT SDVAL="1.65656572121212" SDNUM="2057;">1.6565657212</TD>  
			<TD ALIGN=RIGHT SDVAL="1.61616155151515" SDNUM="2057;">1.6161615515</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="62" SDNUM="2057;">62</TD>  
			<TD ALIGN=RIGHT SDVAL="5580357" SDNUM="2057;">5580357</TD>  
			<TD ALIGN=RIGHT SDVAL="1.60533331469653" SDNUM="2057;">1.6053333147</TD>  
			<TD ALIGN=RIGHT SDVAL="1.680000043008" SDNUM="2057;">1.680000043</TD>  
			<TD ALIGN=RIGHT SDVAL="1.55555567502223" SDNUM="2057;">1.555555675</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="64" SDNUM="2057;">64</TD>  
			<TD ALIGN=RIGHT SDVAL="5998884" SDNUM="2057;">5998884</TD>  
			<TD ALIGN=RIGHT SDVAL="1.59751930525744" SDNUM="2057;">1.5975193053</TD>  
			<TD ALIGN=RIGHT SDVAL="1.63224809814626" SDNUM="2057;">1.6322480981</TD>  
			<TD ALIGN=RIGHT SDVAL="1.56279067906631" SDNUM="2057;">1.5627906791</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="66" SDNUM="2057;">66</TD>  
			<TD ALIGN=RIGHT SDVAL="6556920" SDNUM="2057;">6556920</TD>  
			<TD ALIGN=RIGHT SDVAL="1.56382966392758" SDNUM="2057;">1.5638296639</TD>  
			<TD ALIGN=RIGHT SDVAL="1.6382978593608" SDNUM="2057;">1.6382978594</TD>  
			<TD ALIGN=RIGHT SDVAL="1.52659571872159" SDNUM="2057;">1.5265957187</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="68" SDNUM="2057;">68</TD>  
			<TD ALIGN=RIGHT SDVAL="7291667" SDNUM="2057;">7291667</TD>  
			<TD ALIGN=RIGHT SDVAL="1.50669634255102" SDNUM="2057;">1.5066963426</TD>  
			<TD ALIGN=RIGHT SDVAL="1.60714278367347" SDNUM="2057;">1.6071427837</TD>  
			<TD ALIGN=RIGHT SDVAL="1.54017853530612" SDNUM="2057;">1.5401785353</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="70" SDNUM="2057;">70</TD>  
			<TD ALIGN=RIGHT SDVAL="7812500" SDNUM="2057;">7812500</TD>  
			<TD ALIGN=RIGHT SDVAL="1.531250048" SDNUM="2057;">1.531250048</TD>  
			<TD ALIGN=RIGHT SDVAL="1.642857088" SDNUM="2057;">1.642857088</TD>  
			<TD ALIGN=RIGHT SDVAL="1.562499968" SDNUM="2057;">1.562499968</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="72" SDNUM="2057;">72</TD>  
			<TD ALIGN=RIGHT SDVAL="8333333" SDNUM="2057;">8333333</TD>  
			<TD ALIGN=RIGHT SDVAL="1.54017858160714" SDNUM="2057;">1.5401785816</TD>  
			<TD ALIGN=RIGHT SDVAL="1.6125000645" SDNUM="2057;">1.6125000645</TD>  
			<TD ALIGN=RIGHT SDVAL="1.60714290428572" SDNUM="2057;">1.6071429043</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="74" SDNUM="2057;">74</TD>  
			<TD ALIGN=RIGHT SDVAL="9166667" SDNUM="2057;">9166667</TD>  
			<TD ALIGN=RIGHT SDVAL="1.46590903760331" SDNUM="2057;">1.4659090376</TD>  
			<TD ALIGN=RIGHT SDVAL="1.56818176115703" SDNUM="2057;">1.5681817612</TD>  
			<TD ALIGN=RIGHT SDVAL="1.56818176115703" SDNUM="2057;">1.5681817612</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="76" SDNUM="2057;">76</TD>  
			<TD ALIGN=RIGHT SDVAL="9583333" SDNUM="2057;">9583333</TD>  
			<TD ALIGN=RIGHT SDVAL="1.52173914858223" SDNUM="2057;">1.5217391486</TD>  
			<TD ALIGN=RIGHT SDVAL="1.59420297718967" SDNUM="2057;">1.5942029772</TD>  
			<TD ALIGN=RIGHT SDVAL="1.59420297718967" SDNUM="2057;">1.5942029772</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="78" SDNUM="2057;">78</TD>  
			<TD ALIGN=RIGHT SDVAL="10253906" SDNUM="2057;">10253906</TD>  
			<TD ALIGN=RIGHT SDVAL="1.48994714794538" SDNUM="2057;">1.4899471479</TD>  
			<TD ALIGN=RIGHT SDVAL="1.5915343869936" SDNUM="2057;">1.591534387</TD>  
			<TD ALIGN=RIGHT SDVAL="1.67247388458603" SDNUM="2057;">1.6724738846</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="80" SDNUM="2057;">80</TD>  
			<TD ALIGN=RIGHT SDVAL="10986328" SDNUM="2057;">10986328</TD>  
			<TD ALIGN=RIGHT SDVAL="1.49159892185997" SDNUM="2057;">1.4915989219</TD>  
			<TD ALIGN=RIGHT SDVAL="1.56097560531599" SDNUM="2057;">1.5609756053</TD>  
			<TD ALIGN=RIGHT SDVAL="1.61441438850178" SDNUM="2057;">1.6144143885</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="82" SDNUM="2057;">82</TD>  
			<TD ALIGN=RIGHT SDVAL="11718750" SDNUM="2057;">11718750</TD>  
			<TD ALIGN=RIGHT SDVAL="1.495934976" SDNUM="2057;">1.495934976</TD>  
			<TD ALIGN=RIGHT SDVAL="1.59349589333333" SDNUM="2057;">1.5934958933</TD>  
			<TD ALIGN=RIGHT SDVAL="1.64705885866667" SDNUM="2057;">1.6470588587</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="84" SDNUM="2057;">84</TD>  
			<TD ALIGN=RIGHT SDVAL="12276786" SDNUM="2057;">12276786</TD>  
			<TD ALIGN=RIGHT SDVAL="1.5135134716855" SDNUM="2057;">1.5135134717</TD>  
			<TD ALIGN=RIGHT SDVAL="1.60962559744871" SDNUM="2057;">1.6096255974</TD>  
			<TD ALIGN=RIGHT SDVAL="1.67045446585124" SDNUM="2057;">1.6704544659</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="86" SDNUM="2057;">86</TD>  
			<TD ALIGN=RIGHT SDVAL="13437500" SDNUM="2057;">13437500</TD>  
			<TD ALIGN=RIGHT SDVAL="1.47058820465116" SDNUM="2057;">1.4705882047</TD>  
			<TD ALIGN=RIGHT SDVAL="1.59883720930233" SDNUM="2057;">1.5988372093</TD>  
			<TD ALIGN=RIGHT SDVAL="1.67151166511628" SDNUM="2057;">1.6715116651</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="88" SDNUM="2057;">88</TD>  
			<TD ALIGN=RIGHT SDVAL="14062500" SDNUM="2057;">14062500</TD>  
			<TD ALIGN=RIGHT SDVAL="1.52777777777778" SDNUM="2057;">1.5277777778</TD>  
			<TD ALIGN=RIGHT SDVAL="1.63194446222222" SDNUM="2057;">1.6319444622</TD>  
			<TD ALIGN=RIGHT SDVAL="1.70634922666667" SDNUM="2057;">1.7063492267</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="90" SDNUM="2057;">90</TD>  
			<TD ALIGN=RIGHT SDVAL="15277778" SDNUM="2057;">15277778</TD>  
			<TD ALIGN=RIGHT SDVAL="1.5" SDNUM="2057;">1.5</TD>  
			<TD ALIGN=RIGHT SDVAL="1.57061687897285" SDNUM="2057;">1.570616879</TD>  
			<TD ALIGN=RIGHT SDVAL="1.69143353176097" SDNUM="2057;">1.6914335318</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="92" SDNUM="2057;">92</TD>  
			<TD ALIGN=RIGHT SDVAL="16666667" SDNUM="2057;">16666667</TD>  
			<TD ALIGN=RIGHT SDVAL="1.43973213120536" SDNUM="2057;">1.4397321312</TD>  
			<TD ALIGN=RIGHT SDVAL="1.47321423053572" SDNUM="2057;">1.4732142305</TD>  
			<TD ALIGN=RIGHT SDVAL="1.649999967" SDNUM="2057;">1.649999967</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="94" SDNUM="2057;">94</TD>  
			<TD ALIGN=RIGHT SDVAL="17530488" SDNUM="2057;">17530488</TD>  
			<TD ALIGN=RIGHT SDVAL="1.46428570613665" SDNUM="2057;">1.4642857061</TD>  
			<TD ALIGN=RIGHT SDVAL="1.5083612047765" SDNUM="2057;">1.5083612048</TD>  
			<TD ALIGN=RIGHT SDVAL="1.67565215526231" SDNUM="2057;">1.6756521553</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="96" SDNUM="2057;">96</TD>  
			<TD ALIGN=RIGHT SDVAL="18158784" SDNUM="2057;">18158784</TD>  
			<TD ALIGN=RIGHT SDVAL="1.45617173484744" SDNUM="2057;">1.4561717348</TD>  
			<TD ALIGN=RIGHT SDVAL="1.47999998237767" SDNUM="2057;">1.4799999824</TD>  
			<TD ALIGN=RIGHT SDVAL="1.72093021206706" SDNUM="2057;">1.7209302121</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="98" SDNUM="2057;">98</TD>  
			<TD ALIGN=RIGHT SDVAL="19301471" SDNUM="2057;">19301471</TD>  
			<TD ALIGN=RIGHT SDVAL="1.45714282605714" SDNUM="2057;">1.4571428261</TD>  
			<TD ALIGN=RIGHT SDVAL="1.51785711047619" SDNUM="2057;">1.5178571105</TD>  
			<TD ALIGN=RIGHT SDVAL="1.73469384794558" SDNUM="2057;">1.7346938479</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="100" SDNUM="2057;">100</TD>  
			<TD ALIGN=RIGHT SDVAL="20680147" SDNUM="2057;">20680147</TD>  
			<TD ALIGN=RIGHT SDVAL="1.4166666706963" SDNUM="2057;">1.4166666707</TD>  
			<TD ALIGN=RIGHT SDVAL="1.47676769415614" SDNUM="2057;">1.4767676942</TD>  
			<TD ALIGN=RIGHT SDVAL="1.70994152024161" SDNUM="2057;">1.7099415202</TD>  
		</TR>  
	</TBODY>  
</TABLE>

---

## Comment 21

> Username: jzmaddock  
> Created_at: 2021-09-25 08:11:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#issuecomment-927086117  

OK big update to reduce temporary usage, memory allocations look much better now, here for Bernoulli[m] compiled with gcc-11 mingw:  
  
|m|cpp_rational|mpq_rational|number<rational_adaptor<gmp_int>>|mpq_class|  
|-|------------|------------|---------------------------------|---------|  
|2|0|77|123|101|  
|4|0|187|320|252|  
|6|0|345|612|471|  
|8|0|551|988|758|  
|10|0|805|1464|1113|  
|12|0|1107|2044|1536|  
|14|0|1457|2698|2027|  
|16|0|1857|3458|2587|  
|18|0|2336|4320|3216|  
|20|0|2885|5297|3913|  
|22|6|3511|6358|4706|  
|24|22|4203|7601|5600|  
|26|83|4963|8911|6575|  
|28|377|5806|10370|7632|  
|30|780|6738|11947|8769|  
|32|1454|7771|13644|9988|  
|34|2001|9357|15947|11289|  
|36|2789|10598|18023|12704|  
|38|3669|11948|20185|14252|  
|40|4653|13403|22538|15891|  
|42|5923|14976|24990|17620|  
|44|7379|16622|27596|19449|  
|46|8839|18367|30287|21367|  
|48|10296|20227|33295|23431|  
|50|12045|22857|36898|25646|  
|52|13603|25044|40220|27962|  
|54|15276|27331|43755|30389|  
|56|17239|29749|47410|32919|  
|58|19337|32257|51151|35552|  
|60|21409|34958|55308|38417|  
|62|23694|37800|59426|41396|  
|64|27923|39556|62540|44498|  
|66|30240|44706|69096|47711|  
|68|32566|47934|73872|51042|  
|70|35019|51417|78779|54637|  
|72|37460|55047|84163|58363|  
|74|40282|58777|89439|62211|  
|76|42914|62691|95055|66183|  
|78|45752|66694|100709|70296|  
|80|48681|70905|106834|74620|  
|82|51986|77633|115045|79160|  
|84|54855|82364|121949|83842|  
|86|59032|87239|128670|88659|  
|88|63595|92256|135592|93618|  
|90|68352|97486|142665|98820|  
|92|72446|102974|150380|104256|  
|94|76468|108620|158006|109844|  
|96|80361|111109|162765|115594|  
|98|84783|121460|174932|121486|  
|100|89044|127730|183611|127633|  
|102|93561|134241|192393|134050|  
|104|98452|140919|201349|140616|  
|106|103530|147763|210413|147364|  
|108|108326|154804|220168|154276|  
|110|113891|162184|229824|161562|  
|112|119213|169736|239900|169038|  
|114|124770|182417|255032|176693|  
|116|130624|190589|265966|184519|  
|118|137941|198975|276703|192525|  
|120|144829|207759|288645|200952|  
|122|152045|216736|300007|209561|  
|124|158610|225905|311860|218371|  
|126|165383|235283|323744|227360|  
|128|173971|230678|322303|236670|  
|130|181696|248593|342618|246308|  
|132|189310|258615|356021|256148|  
|134|197708|268800|369164|266192|  
|136|205800|279215|382459|276436|  
|138|212940|290112|396424|287167|  
|140|217502|301235|410903|298101|  
|142|223486|312564|425431|309243|  
|144|229579|324133|440945|320605|  
|146|237213|344671|464320|332333|  
|148|248799|357200|479845|344420|  
|150|261345|369947|495567|356745|  
|152|272741|382909|512422|369279|  
|154|283982|396162|528719|382048|  
|156|293626|409993|546804|395353|  
|158|304036|424022|564050|408907|  
|160|313869|427747|571797|422690|  
|162|323626|454723|601473|436715|  
|164|333294|469863|620894|451304|  
|166|343072|485238|639579|466149|  
|168|352236|500922|659464|481221|  
|170|362793|516840|678534|496561|  
|172|372645|533169|698760|512315|  
|174|382908|549962|719939|528510|  
|176|392507|567018|741432|544947|  
|178|404163|597694|775274|561666|  
|180|417539|615615|797960|578647|  
|182|432009|634079|819557|596234|  
|184|444684|652902|842915|614114|  
|186|457104|672042|866413|632248|  
|188|469331|691451|890093|650654|  
|190|481583|711512|913753|669753|  
|192|477225|698188|905638|689111|  
|194|488955|736905|948365|708760|  
|196|499797|757502|973031|728675|  
|198|511163|778591|998650|749102|  
  
Runtime performance looks rather better too, again mingw, and again performance figures are relative to cpp_rational, so smaller is better:  
  
  
<TABLE FRAME=VOID CELLSPACING=0 COLS=5 RULES=NONE BORDER=0>  
	<COLGROUP><COL WIDTH=86><COL WIDTH=86><COL WIDTH=86><COL WIDTH=167><COL WIDTH=86></COLGROUP>  
	<TBODY>  
		<TR>  
			<TD WIDTH=86 HEIGHT=17 ALIGN=LEFT>m</TD>  
			<TD WIDTH=86 ALIGN=LEFT>cpp_rational (ms)</TD>  
			<TD WIDTH=86 ALIGN=LEFT>mpq_rational</TD>  
			<TD WIDTH=167 ALIGN=LEFT>rational_adaptor&lt;gmp_int&gt;</TD>  
			<TD WIDTH=86 ALIGN=LEFT>mpq_class</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="50" SDNUM="2057;">50</TD>  
			<TD ALIGN=RIGHT SDVAL="2351589" SDNUM="2057;">2351589</TD>  
			<TD ALIGN=RIGHT SDVAL="1.27549584557506" SDNUM="2057;">1.2754958456</TD>  
			<TD ALIGN=RIGHT SDVAL="1.86874959867562" SDNUM="2057;">1.8687495987</TD>  
			<TD ALIGN=RIGHT SDVAL="1.49825671067521" SDNUM="2057;">1.4982567107</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="54" SDNUM="2057;">54</TD>  
			<TD ALIGN=RIGHT SDVAL="2949297" SDNUM="2057;">2949297</TD>  
			<TD ALIGN=RIGHT SDVAL="1.41654333219069" SDNUM="2057;">1.4165433322</TD>  
			<TD ALIGN=RIGHT SDVAL="1.6953192574366" SDNUM="2057;">1.6953192574</TD>  
			<TD ALIGN=RIGHT SDVAL="1.39069140883404" SDNUM="2057;">1.3906914088</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="58" SDNUM="2057;">58</TD>  
			<TD ALIGN=RIGHT SDVAL="3685897" SDNUM="2057;">3685897</TD>  
			<TD ALIGN=RIGHT SDVAL="1.51397529556577" SDNUM="2057;">1.5139752956</TD>  
			<TD ALIGN=RIGHT SDVAL="1.58967410103972" SDNUM="2057;">1.589674101</TD>  
			<TD ALIGN=RIGHT SDVAL="1.39891320891495" SDNUM="2057;">1.3989132089</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="62" SDNUM="2057;">62</TD>  
			<TD ALIGN=RIGHT SDVAL="4464286" SDNUM="2057;">4464286</TD>  
			<TD ALIGN=RIGHT SDVAL="1.24999988800001" SDNUM="2057;">1.249999888</TD>  
			<TD ALIGN=RIGHT SDVAL="1.51666649493334" SDNUM="2057;">1.5166664949</TD>  
			<TD ALIGN=RIGHT SDVAL="1.406249958" SDNUM="2057;">1.406249958</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="66" SDNUM="2057;">66</TD>  
			<TD ALIGN=RIGHT SDVAL="5468750" SDNUM="2057;">5468750</TD>  
			<TD ALIGN=RIGHT SDVAL="1.17346944" SDNUM="2057;">1.17346944</TD>  
			<TD ALIGN=RIGHT SDVAL="1.42857142857143" SDNUM="2057;">1.4285714286</TD>  
			<TD ALIGN=RIGHT SDVAL="1.40952374857143" SDNUM="2057;">1.4095237486</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="70" SDNUM="2057;">70</TD>  
			<TD ALIGN=RIGHT SDVAL="6423611" SDNUM="2057;">6423611</TD>  
			<TD ALIGN=RIGHT SDVAL="1.23793439546697" SDNUM="2057;">1.2379343955</TD>  
			<TD ALIGN=RIGHT SDVAL="1.42702710360263" SDNUM="2057;">1.4270271036</TD>  
			<TD ALIGN=RIGHT SDVAL="1.37837845411249" SDNUM="2057;">1.3783784541</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="74" SDNUM="2057;">74</TD>  
			<TD ALIGN=RIGHT SDVAL="7638889" SDNUM="2057;">7638889</TD>  
			<TD ALIGN=RIGHT SDVAL="1.13636367801653" SDNUM="2057;">1.136363678</TD>  
			<TD ALIGN=RIGHT SDVAL="1.406250045" SDNUM="2057;">1.406250045</TD>  
			<TD ALIGN=RIGHT SDVAL="1.406250045" SDNUM="2057;">1.406250045</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="78" SDNUM="2057;">78</TD>  
			<TD ALIGN=RIGHT SDVAL="8333333" SDNUM="2057;">8333333</TD>  
			<TD ALIGN=RIGHT SDVAL="1.175000087" SDNUM="2057;">1.175000087</TD>  
			<TD ALIGN=RIGHT SDVAL="1.47321437892858" SDNUM="2057;">1.4732143789</TD>  
			<TD ALIGN=RIGHT SDVAL="1.43973221758929" SDNUM="2057;">1.4397322176</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="82" SDNUM="2057;">82</TD>  
			<TD ALIGN=RIGHT SDVAL="10000000" SDNUM="2057;">10000000</TD>  
			<TD ALIGN=RIGHT SDVAL="1.1230469" SDNUM="2057;">1.1230469</TD>  
			<TD ALIGN=RIGHT SDVAL="1.3950893" SDNUM="2057;">1.3950893</TD>  
			<TD ALIGN=RIGHT SDVAL="1.4583333" SDNUM="2057;">1.4583333</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="86" SDNUM="2057;">86</TD>  
			<TD ALIGN=RIGHT SDVAL="11474609" SDNUM="2057;">11474609</TD>  
			<TD ALIGN=RIGHT SDVAL="1.14285715530699" SDNUM="2057;">1.1428571553</TD>  
			<TD ALIGN=RIGHT SDVAL="1.36170217216116" SDNUM="2057;">1.3617021722</TD>  
			<TD ALIGN=RIGHT SDVAL="1.46133894409823" SDNUM="2057;">1.4613389441</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="90" SDNUM="2057;">90</TD>  
			<TD ALIGN=RIGHT SDVAL="12555804" SDNUM="2057;">12555804</TD>  
			<TD ALIGN=RIGHT SDVAL="1.1891357972775" SDNUM="2057;">1.1891357973</TD>  
			<TD ALIGN=RIGHT SDVAL="1.7489488526581" SDNUM="2057;">1.7489488527</TD>  
			<TD ALIGN=RIGHT SDVAL="1.54714712016849" SDNUM="2057;">1.5471471202</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="94" SDNUM="2057;">94</TD>  
			<TD ALIGN=RIGHT SDVAL="14687500" SDNUM="2057;">14687500</TD>  
			<TD ALIGN=RIGHT SDVAL="1.13475179574468" SDNUM="2057;">1.1347517957</TD>  
			<TD ALIGN=RIGHT SDVAL="1.52482267234043" SDNUM="2057;">1.5248226723</TD>  
			<TD ALIGN=RIGHT SDVAL="1.5602837106383" SDNUM="2057;">1.5602837106</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="98" SDNUM="2057;">98</TD>  
			<TD ALIGN=RIGHT SDVAL="15625000" SDNUM="2057;">15625000</TD>  
			<TD ALIGN=RIGHT SDVAL="1.162162176" SDNUM="2057;">1.162162176</TD>  
			<TD ALIGN=RIGHT SDVAL="1.433333312" SDNUM="2057;">1.433333312</TD>  
			<TD ALIGN=RIGHT SDVAL="1.576923072" SDNUM="2057;">1.576923072</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="102" SDNUM="2057;">102</TD>  
			<TD ALIGN=RIGHT SDVAL="17530488" SDNUM="2057;">17530488</TD>  
			<TD ALIGN=RIGHT SDVAL="1.15345265916157" SDNUM="2057;">1.1534526592</TD>  
			<TD ALIGN=RIGHT SDVAL="1.39637681506641" SDNUM="2057;">1.3963768151</TD>  
			<TD ALIGN=RIGHT SDVAL="1.60434780822987" SDNUM="2057;">1.6043478082</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="106" SDNUM="2057;">106</TD>  
			<TD ALIGN=RIGHT SDVAL="19425676" SDNUM="2057;">19425676</TD>  
			<TD ALIGN=RIGHT SDVAL="1.15289851431682" SDNUM="2057;">1.1528985143</TD>  
			<TD ALIGN=RIGHT SDVAL="1.3612040064912" SDNUM="2057;">1.3612040065</TD>  
			<TD ALIGN=RIGHT SDVAL="1.64891301594858" SDNUM="2057;">1.6489130159</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="110" SDNUM="2057;">110</TD>  
			<TD ALIGN=RIGHT SDVAL="21484375" SDNUM="2057;">21484375</TD>  
			<TD ALIGN=RIGHT SDVAL="1.16883116218182" SDNUM="2057;">1.1688311622</TD>  
			<TD ALIGN=RIGHT SDVAL="1.36363636363636" SDNUM="2057;">1.3636363636</TD>  
			<TD ALIGN=RIGHT SDVAL="1.64593300945455" SDNUM="2057;">1.6459330095</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="114" SDNUM="2057;">114</TD>  
			<TD ALIGN=RIGHT SDVAL="23958333" SDNUM="2057;">23958333</TD>  
			<TD ALIGN=RIGHT SDVAL="1.1413043637051" SDNUM="2057;">1.1413043637</TD>  
			<TD ALIGN=RIGHT SDVAL="1.33695654034027" SDNUM="2057;">1.3369565403</TD>  
			<TD ALIGN=RIGHT SDVAL="1.6666667084058" SDNUM="2057;">1.6666667084</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="118" SDNUM="2057;">118</TD>  
			<TD ALIGN=RIGHT SDVAL="27644231" SDNUM="2057;">27644231</TD>  
			<TD ALIGN=RIGHT SDVAL="1.10474308364736" SDNUM="2057;">1.1047430836</TD>  
			<TD ALIGN=RIGHT SDVAL="1.27917618688688" SDNUM="2057;">1.2791761869</TD>  
			<TD ALIGN=RIGHT SDVAL="1.54492751127713" SDNUM="2057;">1.5449275113</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="122" SDNUM="2057;">122</TD>  
			<TD ALIGN=RIGHT SDVAL="30505952" SDNUM="2057;">30505952</TD>  
			<TD ALIGN=RIGHT SDVAL="1.09756099399881" SDNUM="2057;">1.097560994</TD>  
			<TD ALIGN=RIGHT SDVAL="1.29555238925178" SDNUM="2057;">1.2955523893</TD>  
			<TD ALIGN=RIGHT SDVAL="1.64634147460797" SDNUM="2057;">1.6463414746</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="126" SDNUM="2057;">126</TD>  
			<TD ALIGN=RIGHT SDVAL="32894737" SDNUM="2057;">32894737</TD>  
			<TD ALIGN=RIGHT SDVAL="1.1249999962" SDNUM="2057;">1.1249999962</TD>  
			<TD ALIGN=RIGHT SDVAL="1.3953125085025" SDNUM="2057;">1.3953125085</TD>  
			<TD ALIGN=RIGHT SDVAL="1.757499991564" SDNUM="2057;">1.7574999916</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="130" SDNUM="2057;">130</TD>  
			<TD ALIGN=RIGHT SDVAL="35361842" SDNUM="2057;">35361842</TD>  
			<TD ALIGN=RIGHT SDVAL="1.12919898233808" SDNUM="2057;">1.1291989823</TD>  
			<TD ALIGN=RIGHT SDVAL="1.41395349258107" SDNUM="2057;">1.4139534926</TD>  
			<TD ALIGN=RIGHT SDVAL="1.68710360167324" SDNUM="2057;">1.6871036017</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="134" SDNUM="2057;">134</TD>  
			<TD ALIGN=RIGHT SDVAL="38194444" SDNUM="2057;">38194444</TD>  
			<TD ALIGN=RIGHT SDVAL="1.13101604516091" SDNUM="2057;">1.1310160452</TD>  
			<TD ALIGN=RIGHT SDVAL="1.35000001570909" SDNUM="2057;">1.3500000157</TD>  
			<TD ALIGN=RIGHT SDVAL="1.67355375038317" SDNUM="2057;">1.6735537504</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="138" SDNUM="2057;">138</TD>  
			<TD ALIGN=RIGHT SDVAL="41360294" SDNUM="2057;">41360294</TD>  
			<TD ALIGN=RIGHT SDVAL="1.13333333655704" SDNUM="2057;">1.1333333366</TD>  
			<TD ALIGN=RIGHT SDVAL="1.36000000386844" SDNUM="2057;">1.3600000039</TD>  
			<TD ALIGN=RIGHT SDVAL="1.68282829420893" SDNUM="2057;">1.6828282942</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="142" SDNUM="2057;">142</TD>  
			<TD ALIGN=RIGHT SDVAL="45833333" SDNUM="2057;">45833333</TD>  
			<TD ALIGN=RIGHT SDVAL="1.12500000818182" SDNUM="2057;">1.1250000082</TD>  
			<TD ALIGN=RIGHT SDVAL="1.36363637355372" SDNUM="2057;">1.3636363736</TD>  
			<TD ALIGN=RIGHT SDVAL="1.62878789548209" SDNUM="2057;">1.6287878955</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="146" SDNUM="2057;">146</TD>  
			<TD ALIGN=RIGHT SDVAL="49107143" SDNUM="2057;">49107143</TD>  
			<TD ALIGN=RIGHT SDVAL="1.12809916471826" SDNUM="2057;">1.1280991647</TD>  
			<TD ALIGN=RIGHT SDVAL="1.35950413568144" SDNUM="2057;">1.3595041357</TD>  
			<TD ALIGN=RIGHT SDVAL="1.69696968524518" SDNUM="2057;">1.6969696852</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="150" SDNUM="2057;">150</TD>  
			<TD ALIGN=RIGHT SDVAL="53125000" SDNUM="2057;">53125000</TD>  
			<TD ALIGN=RIGHT SDVAL="1.14379085176471" SDNUM="2057;">1.1437908518</TD>  
			<TD ALIGN=RIGHT SDVAL="1.36363636705882" SDNUM="2057;">1.3636363671</TD>  
			<TD ALIGN=RIGHT SDVAL="1.73202614588235" SDNUM="2057;">1.7320261459</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="154" SDNUM="2057;">154</TD>  
			<TD ALIGN=RIGHT SDVAL="59659091" SDNUM="2057;">59659091</TD>  
			<TD ALIGN=RIGHT SDVAL="1.09523809204535" SDNUM="2057;">1.095238092</TD>  
			<TD ALIGN=RIGHT SDVAL="1.3386243347221" SDNUM="2057;">1.3386243347</TD>  
			<TD ALIGN=RIGHT SDVAL="1.79591836221574" SDNUM="2057;">1.7959183622</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="158" SDNUM="2057;">158</TD>  
			<TD ALIGN=RIGHT SDVAL="63920455" SDNUM="2057;">63920455</TD>  
			<TD ALIGN=RIGHT SDVAL="1.08888888541235" SDNUM="2057;">1.0888888854</TD>  
			<TD ALIGN=RIGHT SDVAL="1.30370368921811" SDNUM="2057;">1.3037036892</TD>  
			<TD ALIGN=RIGHT SDVAL="1.71111109894321" SDNUM="2057;">1.7111110989</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="162" SDNUM="2057;">162</TD>  
			<TD ALIGN=RIGHT SDVAL="68181818" SDNUM="2057;">68181818</TD>  
			<TD ALIGN=RIGHT SDVAL="1.09490741358642" SDNUM="2057;">1.0949074136</TD>  
			<TD ALIGN=RIGHT SDVAL="1.27678571140476" SDNUM="2057;">1.2767857114</TD>  
			<TD ALIGN=RIGHT SDVAL="1.75694445401852" SDNUM="2057;">1.756944454</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="166" SDNUM="2057;">166</TD>  
			<TD ALIGN=RIGHT SDVAL="72916667" SDNUM="2057;">72916667</TD>  
			<TD ALIGN=RIGHT SDVAL="1.11904761088435" SDNUM="2057;">1.1190476109</TD>  
			<TD ALIGN=RIGHT SDVAL="1.3928571364898" SDNUM="2057;">1.3928571365</TD>  
			<TD ALIGN=RIGHT SDVAL="1.67857141632653" SDNUM="2057;">1.6785714163</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="170" SDNUM="2057;">170</TD>  
			<TD ALIGN=RIGHT SDVAL="78125000" SDNUM="2057;">78125000</TD>  
			<TD ALIGN=RIGHT SDVAL="1.1111111168" SDNUM="2057;">1.1111111168</TD>  
			<TD ALIGN=RIGHT SDVAL="1.2666666624" SDNUM="2057;">1.2666666624</TD>  
			<TD ALIGN=RIGHT SDVAL="1.72" SDNUM="2057;">1.72</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="174" SDNUM="2057;">174</TD>  
			<TD ALIGN=RIGHT SDVAL="85069444" SDNUM="2057;">85069444</TD>  
			<TD ALIGN=RIGHT SDVAL="1.07580175321235" SDNUM="2057;">1.0758017532</TD>  
			<TD ALIGN=RIGHT SDVAL="1.25510204345523" SDNUM="2057;">1.2551020435</TD>  
			<TD ALIGN=RIGHT SDVAL="1.69897960071304" SDNUM="2057;">1.6989796007</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="178" SDNUM="2057;">178</TD>  
			<TD ALIGN=RIGHT SDVAL="90277778" SDNUM="2057;">90277778</TD>  
			<TD ALIGN=RIGHT SDVAL="1.11263736464582" SDNUM="2057;">1.1126373646</TD>  
			<TD ALIGN=RIGHT SDVAL="1.2692307624142" SDNUM="2057;">1.2692307624</TD>  
			<TD ALIGN=RIGHT SDVAL="1.90384614915976" SDNUM="2057;">1.9038461492</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="182" SDNUM="2057;">182</TD>  
			<TD ALIGN=RIGHT SDVAL="98214286" SDNUM="2057;">98214286</TD>  
			<TD ALIGN=RIGHT SDVAL="1.09090908628099" SDNUM="2057;">1.0909090863</TD>  
			<TD ALIGN=RIGHT SDVAL="1.2196969695427" SDNUM="2057;">1.2196969695</TD>  
			<TD ALIGN=RIGHT SDVAL="1.78977272206612" SDNUM="2057;">1.7897727221</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="186" SDNUM="2057;">186</TD>  
			<TD ALIGN=RIGHT SDVAL="104910714" SDNUM="2057;">104910714</TD>  
			<TD ALIGN=RIGHT SDVAL="1.09219858135748" SDNUM="2057;">1.0921985814</TD>  
			<TD ALIGN=RIGHT SDVAL="1.22127659907071" SDNUM="2057;">1.2212765991</TD>  
			<TD ALIGN=RIGHT SDVAL="1.75000000476596" SDNUM="2057;">1.7500000048</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="190" SDNUM="2057;">190</TD>  
			<TD ALIGN=RIGHT SDVAL="111979167" SDNUM="2057;">111979167</TD>  
			<TD ALIGN=RIGHT SDVAL="1.09302324958356" SDNUM="2057;">1.0930232496</TD>  
			<TD ALIGN=RIGHT SDVAL="1.22790697308902" SDNUM="2057;">1.2279069731</TD>  
			<TD ALIGN=RIGHT SDVAL="1.81395348297242" SDNUM="2057;">1.813953483</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="194" SDNUM="2057;">194</TD>  
			<TD ALIGN=RIGHT SDVAL="114583333" SDNUM="2057;">114583333</TD>  
			<TD ALIGN=RIGHT SDVAL="1.20000000349091" SDNUM="2057;">1.2000000035</TD>  
			<TD ALIGN=RIGHT SDVAL="1.28181818554711" SDNUM="2057;">1.2818181855</TD>  
			<TD ALIGN=RIGHT SDVAL="1.81818182056198" SDNUM="2057;">1.8181818206</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="198" SDNUM="2057;">198</TD>  
			<TD ALIGN=RIGHT SDVAL="125000000" SDNUM="2057;">125000000</TD>  
			<TD ALIGN=RIGHT SDVAL="1.125" SDNUM="2057;">1.125</TD>  
			<TD ALIGN=RIGHT SDVAL="1.3125" SDNUM="2057;">1.3125</TD>  
			<TD ALIGN=RIGHT SDVAL="1.75" SDNUM="2057;">1.75</TD>  
		</TR>  
	</TBODY>  
</TABLE>  
</BODY>

---

## Comment 22

> Username: afabri  
> Created_at: 2021-09-29 13:44:57 UTC  
> Updated_at: 2021-09-29 13:51:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#issuecomment-930190493  

Wouldn't it make sense to cherry-pick [your fix of the gcd](https://github.com/boostorg/multiprecision/pull/372) into this branch. It may have an impact on performance, and without it computations might be plain wrong.

---

## Comment 23

> Username: afabri  
> Created_at: 2021-09-29 14:02:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#issuecomment-930207327  

@jzmaddock do you have a (vague) schedule for an integration into the develop branch of boost or a boost release.  It's a question coming from @lrineau, the release manager of the CGAL project.

---

## Comment 24

> Username: jzmaddock  
> Created_at: 2021-09-29 14:19:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#issuecomment-930223456  

>Wouldn't it make sense to cherry-pick your fix of the gcd into this branch. It may have an impact on performance, and without it computations might be plain wrong.  
  
Yep.  
  
> do you have a (vague) schedule for an integration into the develop branch of boost or a boost release. It's a question coming from @lrineau, the release manager of the CGAL project.  
  
It's basically good to go, I just need to work on documentation a bit, and do a quick matrix determinant performance test.  I just got a bit distracted by bug reports elsewhere ;)

---

## Comment 25

> Username: jzmaddock  
> Created_at: 2021-09-29 18:42:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#issuecomment-930446202  

>Wouldn't it make sense to cherry-pick your fix of the gcd into this branch. It may have an impact on performance, and without it computations might be plain wrong.  
  
Done, and 3x3 determinant calculation added as a benchmark - it's a surprisingly tough calculation for rational values isn't it?  
  
Determinant calculation shows mpq significantly ahead of cpp_rational, this calculation mainly just hammers gcd I suspect, but if I have time, I'll investigate some more.  As before, column for cpp_rational shows the actual time, the others are relative to that:  
  
  
<BODY TEXT="#000000">  
<TABLE FRAME=VOID CELLSPACING=0 COLS=4 RULES=NONE BORDER=0>  
	<COLGROUP><COL WIDTH=387><COL WIDTH=144><COL WIDTH=86><COL WIDTH=86></COLGROUP>  
	<TBODY>  
		<TR>  
			<TD WIDTH=387 HEIGHT=18 ALIGN=LEFT>Bits</TD>  
			<TD WIDTH=144 ALIGN=LEFT>cpp_rational (ms)</TD>  
			<TD WIDTH=86 ALIGN=LEFT>mpq_rational</TD>  
			<TD WIDTH=86 ALIGN=LEFT>mpq_class</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="512" SDNUM="2057;">512</TD>  
			<TD ALIGN=RIGHT SDVAL="42.3" SDNUM="2057;">42.3</TD>  
			<TD ALIGN=RIGHT SDVAL="0.309692671394799" SDNUM="2057;">0.3096926714</TD>  
			<TD ALIGN=RIGHT SDVAL="0.302600472813239" SDNUM="2057;">0.3026004728</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="1024" SDNUM="2057;">1024</TD>  
			<TD ALIGN=RIGHT SDVAL="93.8" SDNUM="2057;">93.8</TD>  
			<TD ALIGN=RIGHT SDVAL="0.318763326226013" SDNUM="2057;">0.3187633262</TD>  
			<TD ALIGN=RIGHT SDVAL="0.312366737739872" SDNUM="2057;">0.3123667377</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="2048" SDNUM="2057;">2048</TD>  
			<TD ALIGN=RIGHT SDVAL="229" SDNUM="2057;">229</TD>  
			<TD ALIGN=RIGHT SDVAL="0.310043668122271" SDNUM="2057;">0.3100436681</TD>  
			<TD ALIGN=RIGHT SDVAL="0.310043668122271" SDNUM="2057;">0.3100436681</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="4096" SDNUM="2057;">4096</TD>  
			<TD ALIGN=RIGHT SDVAL="609" SDNUM="2057;">609</TD>  
			<TD ALIGN=RIGHT SDVAL="0.295566502463054" SDNUM="2057;">0.2955665025</TD>  
			<TD ALIGN=RIGHT SDVAL="0.295566502463054" SDNUM="2057;">0.2955665025</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="8192" SDNUM="2057;">8192</TD>  
			<TD ALIGN=RIGHT SDVAL="1797" SDNUM="2057;">1797</TD>  
			<TD ALIGN=RIGHT SDVAL="0.269337785197551" SDNUM="2057;">0.2693377852</TD>  
			<TD ALIGN=RIGHT SDVAL="0.278241513633834" SDNUM="2057;">0.2782415136</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="16384" SDNUM="2057;">16384</TD>  
			<TD ALIGN=RIGHT SDVAL="5844" SDNUM="2057;">5844</TD>  
			<TD ALIGN=RIGHT SDVAL="0.229979466119096" SDNUM="2057;">0.2299794661</TD>  
			<TD ALIGN=RIGHT SDVAL="0.229979466119096" SDNUM="2057;">0.2299794661</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="32768" SDNUM="2057;">32768</TD>  
			<TD ALIGN=RIGHT SDVAL="20641" SDNUM="2057;">20641</TD>  
			<TD ALIGN=RIGHT SDVAL="0.182452400561988" SDNUM="2057;">0.1824524006</TD>  
			<TD ALIGN=RIGHT SDVAL="0.181677244319558" SDNUM="2057;">0.1816772443</TD>  
		</TR>  
	</TBODY>  
</TABLE>  
</BODY>  
  
Bernoulli calculations are largely the same as before:  
  
  
<TABLE FRAME=VOID CELLSPACING=0 COLS=5 RULES=NONE BORDER=0>  
	<COLGROUP><COL WIDTH=144><COL WIDTH=86><COL WIDTH=86><COL WIDTH=167><COL WIDTH=86></COLGROUP>  
	<TBODY>  
		<TR>  
			<TD WIDTH=144 HEIGHT=17 ALIGN=LEFT>m</TD>  
			<TD WIDTH=86 ALIGN=LEFT>cpp_rational</TD>  
			<TD WIDTH=86 ALIGN=LEFT>mpq_rational</TD>  
			<TD WIDTH=167 ALIGN=LEFT>rational_adaptor&lt;gmp_int&gt;</TD>  
			<TD WIDTH=86 ALIGN=LEFT>mpq_class</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="50" SDNUM="2057;">50</TD>  
			<TD ALIGN=RIGHT SDVAL="2197266" SDNUM="2057;">2197266</TD>  
			<TD ALIGN=RIGHT SDVAL="1.31369893312872" SDNUM="2057;">1.3136989331</TD>  
			<TD ALIGN=RIGHT SDVAL="1.81912112598111" SDNUM="2057;">1.819121126</TD>  
			<TD ALIGN=RIGHT SDVAL="1.46031704855033" SDNUM="2057;">1.4603170486</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="54" SDNUM="2057;">54</TD>  
			<TD ALIGN=RIGHT SDVAL="2761044" SDNUM="2057;">2761044</TD>  
			<TD ALIGN=RIGHT SDVAL="1.30594405594406" SDNUM="2057;">1.3059440559</TD>  
			<TD ALIGN=RIGHT SDVAL="1.71724137681254" SDNUM="2057;">1.7172413768</TD>  
			<TD ALIGN=RIGHT SDVAL="1.41477281781819" SDNUM="2057;">1.4147728178</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="58" SDNUM="2057;">58</TD>  
			<TD ALIGN=RIGHT SDVAL="3446691" SDNUM="2057;">3446691</TD>  
			<TD ALIGN=RIGHT SDVAL="1.20160641032225" SDNUM="2057;">1.2016064103</TD>  
			<TD ALIGN=RIGHT SDVAL="1.61904766049524" SDNUM="2057;">1.6190476605</TD>  
			<TD ALIGN=RIGHT SDVAL="1.43816112323385" SDNUM="2057;">1.4381611232</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="62" SDNUM="2057;">62</TD>  
			<TD ALIGN=RIGHT SDVAL="4087936" SDNUM="2057;">4087936</TD>  
			<TD ALIGN=RIGHT SDVAL="1.2231111250274" SDNUM="2057;">1.223111125</TD>  
			<TD ALIGN=RIGHT SDVAL="1.6138271244951" SDNUM="2057;">1.6138271245</TD>  
			<TD ALIGN=RIGHT SDVAL="1.46746035162977" SDNUM="2057;">1.4674603516</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="66" SDNUM="2057;">66</TD>  
			<TD ALIGN=RIGHT SDVAL="4882812" SDNUM="2057;">4882812</TD>  
			<TD ALIGN=RIGHT SDVAL="1.20000012288001" SDNUM="2057;">1.2000001229</TD>  
			<TD ALIGN=RIGHT SDVAL="1.57866676005548" SDNUM="2057;">1.5786667601</TD>  
			<TD ALIGN=RIGHT SDVAL="1.45714293321144" SDNUM="2057;">1.4571429332</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="70" SDNUM="2057;">70</TD>  
			<TD ALIGN=RIGHT SDVAL="5937500" SDNUM="2057;">5937500</TD>  
			<TD ALIGN=RIGHT SDVAL="1.17481195789474" SDNUM="2057;">1.1748119579</TD>  
			<TD ALIGN=RIGHT SDVAL="1.47368421052632" SDNUM="2057;">1.4736842105</TD>  
			<TD ALIGN=RIGHT SDVAL="1.40350871578947" SDNUM="2057;">1.4035087158</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="74" SDNUM="2057;">74</TD>  
			<TD ALIGN=RIGHT SDVAL="6770833" SDNUM="2057;">6770833</TD>  
			<TD ALIGN=RIGHT SDVAL="1.17948722114399" SDNUM="2057;">1.1794872211</TD>  
			<TD ALIGN=RIGHT SDVAL="1.55048086402367" SDNUM="2057;">1.550480864</TD>  
			<TD ALIGN=RIGHT SDVAL="1.44615396657989" SDNUM="2057;">1.4461539666</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="78" SDNUM="2057;">78</TD>  
			<TD ALIGN=RIGHT SDVAL="7812500" SDNUM="2057;">7812500</TD>  
			<TD ALIGN=RIGHT SDVAL="1.173333376" SDNUM="2057;">1.173333376</TD>  
			<TD ALIGN=RIGHT SDVAL="1.535714304" SDNUM="2057;">1.535714304</TD>  
			<TD ALIGN=RIGHT SDVAL="1.464285696" SDNUM="2057;">1.464285696</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="82" SDNUM="2057;">82</TD>  
			<TD ALIGN=RIGHT SDVAL="8958333" SDNUM="2057;">8958333</TD>  
			<TD ALIGN=RIGHT SDVAL="1.17187505755814" SDNUM="2057;">1.1718750576</TD>  
			<TD ALIGN=RIGHT SDVAL="1.49501665097736" SDNUM="2057;">1.495016651</TD>  
			<TD ALIGN=RIGHT SDVAL="1.50000005581396" SDNUM="2057;">1.5000000558</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="86" SDNUM="2057;">86</TD>  
			<TD ALIGN=RIGHT SDVAL="10416667" SDNUM="2057;">10416667</TD>  
			<TD ALIGN=RIGHT SDVAL="1.17857141828571" SDNUM="2057;">1.1785714183</TD>  
			<TD ALIGN=RIGHT SDVAL="1.46666664106667" SDNUM="2057;">1.4666666411</TD>  
			<TD ALIGN=RIGHT SDVAL="1.46666664106667" SDNUM="2057;">1.4666666411</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="90" SDNUM="2057;">90</TD>  
			<TD ALIGN=RIGHT SDVAL="11718750" SDNUM="2057;">11718750</TD>  
			<TD ALIGN=RIGHT SDVAL="1.16666666666667" SDNUM="2057;">1.1666666667</TD>  
			<TD ALIGN=RIGHT SDVAL="1.495934976" SDNUM="2057;">1.495934976</TD>  
			<TD ALIGN=RIGHT SDVAL="1.549549568" SDNUM="2057;">1.549549568</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="94" SDNUM="2057;">94</TD>  
			<TD ALIGN=RIGHT SDVAL="14375000" SDNUM="2057;">14375000</TD>  
			<TD ALIGN=RIGHT SDVAL="1.03864737391304" SDNUM="2057;">1.0386473739</TD>  
			<TD ALIGN=RIGHT SDVAL="1.32197412173913" SDNUM="2057;">1.3219741217</TD>  
			<TD ALIGN=RIGHT SDVAL="1.4066496" SDNUM="2057;">1.4066496</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="98" SDNUM="2057;">98</TD>  
			<TD ALIGN=RIGHT SDVAL="14930556" SDNUM="2057;">14930556</TD>  
			<TD ALIGN=RIGHT SDVAL="1.13953485724175" SDNUM="2057;">1.1395348572</TD>  
			<TD ALIGN=RIGHT SDVAL="1.43895344553813" SDNUM="2057;">1.4389534455</TD>  
			<TD ALIGN=RIGHT SDVAL="1.56976739513251" SDNUM="2057;">1.5697673951</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="102" SDNUM="2057;">102</TD>  
			<TD ALIGN=RIGHT SDVAL="16666667" SDNUM="2057;">16666667</TD>  
			<TD ALIGN=RIGHT SDVAL="1.14020265719595" SDNUM="2057;">1.1402026572</TD>  
			<TD ALIGN=RIGHT SDVAL="1.43749995125" SDNUM="2057;">1.4374999513</TD>  
			<TD ALIGN=RIGHT SDVAL="1.55048072899039" SDNUM="2057;">1.550480729</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="106" SDNUM="2057;">106</TD>  
			<TD ALIGN=RIGHT SDVAL="18581081" SDNUM="2057;">18581081</TD>  
			<TD ALIGN=RIGHT SDVAL="1.13770054605542" SDNUM="2057;">1.1377005461</TD>  
			<TD ALIGN=RIGHT SDVAL="1.42307694584615" SDNUM="2057;">1.4230769458</TD>  
			<TD ALIGN=RIGHT SDVAL="1.54166665545455" SDNUM="2057;">1.5416666555</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="110" SDNUM="2057;">110</TD>  
			<TD ALIGN=RIGHT SDVAL="20507812" SDNUM="2057;">20507812</TD>  
			<TD ALIGN=RIGHT SDVAL="1.14285717072109" SDNUM="2057;">1.1428571707</TD>  
			<TD ALIGN=RIGHT SDVAL="1.42857146340136" SDNUM="2057;">1.4285714634</TD>  
			<TD ALIGN=RIGHT SDVAL="1.59637190939726" SDNUM="2057;">1.5963719094</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="114" SDNUM="2057;">114</TD>  
			<TD ALIGN=RIGHT SDVAL="22949219" SDNUM="2057;">22949219</TD>  
			<TD ALIGN=RIGHT SDVAL="1.16717322711505" SDNUM="2057;">1.1671732271</TD>  
			<TD ALIGN=RIGHT SDVAL="1.39412360830231" SDNUM="2057;">1.3941236083</TD>  
			<TD ALIGN=RIGHT SDVAL="1.61254197800805" SDNUM="2057;">1.612541978</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="118" SDNUM="2057;">118</TD>  
			<TD ALIGN=RIGHT SDVAL="25240385" SDNUM="2057;">25240385</TD>  
			<TD ALIGN=RIGHT SDVAL="1.16071426802721" SDNUM="2057;">1.160714268</TD>  
			<TD ALIGN=RIGHT SDVAL="1.39285712163265" SDNUM="2057;">1.3928571216</TD>  
			<TD ALIGN=RIGHT SDVAL="1.60224085329919" SDNUM="2057;">1.6022408533</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="122" SDNUM="2057;">122</TD>  
			<TD ALIGN=RIGHT SDVAL="27500000" SDNUM="2057;">27500000</TD>  
			<TD ALIGN=RIGHT SDVAL="1.16003789090909" SDNUM="2057;">1.1600378909</TD>  
			<TD ALIGN=RIGHT SDVAL="1.45202021818182" SDNUM="2057;">1.4520202182</TD>  
			<TD ALIGN=RIGHT SDVAL="1.62878789090909" SDNUM="2057;">1.6287878909</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="126" SDNUM="2057;">126</TD>  
			<TD ALIGN=RIGHT SDVAL="31250000" SDNUM="2057;">31250000</TD>  
			<TD ALIGN=RIGHT SDVAL="1.125" SDNUM="2057;">1.125</TD>  
			<TD ALIGN=RIGHT SDVAL="1.6" SDNUM="2057;">1.6</TD>  
			<TD ALIGN=RIGHT SDVAL="1.6" SDNUM="2057;">1.6</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="130" SDNUM="2057;">130</TD>  
			<TD ALIGN=RIGHT SDVAL="33593750" SDNUM="2057;">33593750</TD>  
			<TD ALIGN=RIGHT SDVAL="1.10159118883721" SDNUM="2057;">1.1015911888</TD>  
			<TD ALIGN=RIGHT SDVAL="1.52219874232558" SDNUM="2057;">1.5221987423</TD>  
			<TD ALIGN=RIGHT SDVAL="1.64904861767442" SDNUM="2057;">1.6490486177</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="134" SDNUM="2057;">134</TD>  
			<TD ALIGN=RIGHT SDVAL="37006579" SDNUM="2057;">37006579</TD>  
			<TD ALIGN=RIGHT SDVAL="1.09281044324578" SDNUM="2057;">1.0928104432</TD>  
			<TD ALIGN=RIGHT SDVAL="1.42020201326905" SDNUM="2057;">1.4202020133</TD>  
			<TD ALIGN=RIGHT SDVAL="1.65050503587484" SDNUM="2057;">1.6505050359</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="138" SDNUM="2057;">138</TD>  
			<TD ALIGN=RIGHT SDVAL="40441176" SDNUM="2057;">40441176</TD>  
			<TD ALIGN=RIGHT SDVAL="1.11079546747107" SDNUM="2057;">1.1107954675</TD>  
			<TD ALIGN=RIGHT SDVAL="1.35227274300826" SDNUM="2057;">1.352272743</TD>  
			<TD ALIGN=RIGHT SDVAL="1.68595042834561" SDNUM="2057;">1.6859504283</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="142" SDNUM="2057;">142</TD>  
			<TD ALIGN=RIGHT SDVAL="43198529" SDNUM="2057;">43198529</TD>  
			<TD ALIGN=RIGHT SDVAL="1.10921987644533" SDNUM="2057;">1.1092198764</TD>  
			<TD ALIGN=RIGHT SDVAL="1.31528048096267" SDNUM="2057;">1.315280481</TD>  
			<TD ALIGN=RIGHT SDVAL="1.67698261206996" SDNUM="2057;">1.6769826121</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="146" SDNUM="2057;">146</TD>  
			<TD ALIGN=RIGHT SDVAL="50000000" SDNUM="2057;">50000000</TD>  
			<TD ALIGN=RIGHT SDVAL="1.05113636" SDNUM="2057;">1.05113636</TD>  
			<TD ALIGN=RIGHT SDVAL="1.25" SDNUM="2057;">1.25</TD>  
			<TD ALIGN=RIGHT SDVAL="1.5625" SDNUM="2057;">1.5625</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="150" SDNUM="2057;">150</TD>  
			<TD ALIGN=RIGHT SDVAL="51562500" SDNUM="2057;">51562500</TD>  
			<TD ALIGN=RIGHT SDVAL="1.12121212121212" SDNUM="2057;">1.1212121212</TD>  
			<TD ALIGN=RIGHT SDVAL="1.34680133818182" SDNUM="2057;">1.3468013382</TD>  
			<TD ALIGN=RIGHT SDVAL="1.64502165333333" SDNUM="2057;">1.6450216533</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="154" SDNUM="2057;">154</TD>  
			<TD ALIGN=RIGHT SDVAL="57812500" SDNUM="2057;">57812500</TD>  
			<TD ALIGN=RIGHT SDVAL="1.08108108108108" SDNUM="2057;">1.0810810811</TD>  
			<TD ALIGN=RIGHT SDVAL="1.26126126702703" SDNUM="2057;">1.261261267</TD>  
			<TD ALIGN=RIGHT SDVAL="1.62162162162162" SDNUM="2057;">1.6216216216</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="158" SDNUM="2057;">158</TD>  
			<TD ALIGN=RIGHT SDVAL="66761364" SDNUM="2057;">66761364</TD>  
			<TD ALIGN=RIGHT SDVAL="0.978723397562698" SDNUM="2057;">0.9787233976</TD>  
			<TD ALIGN=RIGHT SDVAL="1.17021275958352" SDNUM="2057;">1.1702127596</TD>  
			<TD ALIGN=RIGHT SDVAL="1.52127658745858" SDNUM="2057;">1.5212765875</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="162" SDNUM="2057;">162</TD>  
			<TD ALIGN=RIGHT SDVAL="69602273" SDNUM="2057;">69602273</TD>  
			<TD ALIGN=RIGHT SDVAL="1.02267573933972" SDNUM="2057;">1.0226757393</TD>  
			<TD ALIGN=RIGHT SDVAL="1.19727890208413" SDNUM="2057;">1.1972789021</TD>  
			<TD ALIGN=RIGHT SDVAL="1.57142856527114" SDNUM="2057;">1.5714285653</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="166" SDNUM="2057;">166</TD>  
			<TD ALIGN=RIGHT SDVAL="69602273" SDNUM="2057;">69602273</TD>  
			<TD ALIGN=RIGHT SDVAL="1.09750566623018" SDNUM="2057;">1.0975056662</TD>  
			<TD ALIGN=RIGHT SDVAL="1.28279882468781" SDNUM="2057;">1.2827988247</TD>  
			<TD ALIGN=RIGHT SDVAL="1.72108843341941" SDNUM="2057;">1.7210884334</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="170" SDNUM="2057;">170</TD>  
			<TD ALIGN=RIGHT SDVAL="74652778" SDNUM="2057;">74652778</TD>  
			<TD ALIGN=RIGHT SDVAL="1.09302324958356" SDNUM="2057;">1.0930232496</TD>  
			<TD ALIGN=RIGHT SDVAL="1.28571428380066" SDNUM="2057;">1.2857142838</TD>  
			<TD ALIGN=RIGHT SDVAL="1.70930232495836" SDNUM="2057;">1.709302325</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="174" SDNUM="2057;">174</TD>  
			<TD ALIGN=RIGHT SDVAL="80357143" SDNUM="2057;">80357143</TD>  
			<TD ALIGN=RIGHT SDVAL="1.10185185404115" SDNUM="2057;">1.101851854</TD>  
			<TD ALIGN=RIGHT SDVAL="1.27777777017284" SDNUM="2057;">1.2777777702</TD>  
			<TD ALIGN=RIGHT SDVAL="1.71111110806914" SDNUM="2057;">1.7111111081</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="178" SDNUM="2057;">178</TD>  
			<TD ALIGN=RIGHT SDVAL="86805556" SDNUM="2057;">86805556</TD>  
			<TD ALIGN=RIGHT SDVAL="1.10571428169874" SDNUM="2057;">1.1057142817</TD>  
			<TD ALIGN=RIGHT SDVAL="1.2599999935488" SDNUM="2057;">1.2599999935</TD>  
			<TD ALIGN=RIGHT SDVAL="1.7099999912448" SDNUM="2057;">1.7099999912</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="182" SDNUM="2057;">182</TD>  
			<TD ALIGN=RIGHT SDVAL="91517857" SDNUM="2057;">91517857</TD>  
			<TD ALIGN=RIGHT SDVAL="1.14634146208209" SDNUM="2057;">1.1463414621</TD>  
			<TD ALIGN=RIGHT SDVAL="1.28048780687686" SDNUM="2057;">1.2804878069</TD>  
			<TD ALIGN=RIGHT SDVAL="1.75000000273171" SDNUM="2057;">1.7500000027</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="186" SDNUM="2057;">186</TD>  
			<TD ALIGN=RIGHT SDVAL="98214286" SDNUM="2057;">98214286</TD>  
			<TD ALIGN=RIGHT SDVAL="1.09090908628099" SDNUM="2057;">1.0909090863</TD>  
			<TD ALIGN=RIGHT SDVAL="1.27272726902479" SDNUM="2057;">1.272727269</TD>  
			<TD ALIGN=RIGHT SDVAL="1.74999999490909" SDNUM="2057;">1.7499999949</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="190" SDNUM="2057;">190</TD>  
			<TD ALIGN=RIGHT SDVAL="104166667" SDNUM="2057;">104166667</TD>  
			<TD ALIGN=RIGHT SDVAL="1.1249999964" SDNUM="2057;">1.1249999964</TD>  
			<TD ALIGN=RIGHT SDVAL="1.229999996064" SDNUM="2057;">1.2299999961</TD>  
			<TD ALIGN=RIGHT SDVAL="1.76249999436" SDNUM="2057;">1.7624999944</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="194" SDNUM="2057;">194</TD>  
			<TD ALIGN=RIGHT SDVAL="109375000" SDNUM="2057;">109375000</TD>  
			<TD ALIGN=RIGHT SDVAL="1.14285714285714" SDNUM="2057;">1.1428571429</TD>  
			<TD ALIGN=RIGHT SDVAL="1.28571428571429" SDNUM="2057;">1.2857142857</TD>  
			<TD ALIGN=RIGHT SDVAL="1.78571428571429" SDNUM="2057;">1.7857142857</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="198" SDNUM="2057;">198</TD>  
			<TD ALIGN=RIGHT SDVAL="114583333" SDNUM="2057;">114583333</TD>  
			<TD ALIGN=RIGHT SDVAL="1.15909091246281" SDNUM="2057;">1.1590909125</TD>  
			<TD ALIGN=RIGHT SDVAL="1.29545454922314" SDNUM="2057;">1.2954545492</TD>  
			<TD ALIGN=RIGHT SDVAL="1.81818182056198" SDNUM="2057;">1.8181818206</TD>  
		</TR>  
	</TBODY>  
</TABLE>  
</BODY>

---

## Comment 26

> Username: jzmaddock  
> Created_at: 2021-09-30 18:45:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#issuecomment-931574509  

Barring CI SNAFU's, this is now ready to go.

---

## Review 27 [Commented]

> Username: lrineau  
> Created_at: 2021-10-01 07:09:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/366#pullrequestreview-768628074  

📁 include/boost/multiprecision/detail/et_ops.hpp

```diff
 835 |+ {
 836 |+    b *= a;
 837 |+    return static_cast<number<B, et_on>&&>(b);
```

> Username: lrineau  
> Created_at: 2021-10-01 07:09:45 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#discussion_r719994872  

Seeing the CI results at https://drone.cpp.al/boostorg/multiprecision/292/179/2, can you comment on that `static_cast`? The type of `b` is `number<B, ET>&&`. Why should it cast nicely into `number<B, et_on>&&`?


---

## Review 28 [Commented]

> Username: lrineau  
> Created_at: 2021-10-01 07:10:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/366#pullrequestreview-768628291  

📁 include/boost/multiprecision/detail/et_ops.hpp

```diff
 810 |+ {
 811 |+    a *= b;
 812 |+    return static_cast<number<B, et_on>&&>(a);
```

> Username: lrineau  
> Created_at: 2021-10-01 07:10:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#discussion_r719995027  

Same here: why `et_on` and not `ET`?

> Username: jzmaddock  
> Created_at: 2021-10-01 08:11:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#discussion_r720039164  

Good catch: too much blind refactoring in one go!  
  
I'm a bit concerned that didn't show up in local testing, also thinking I should be using `std::foward` here.

> Username: lrineau  
> Created_at: 2021-10-01 08:32:59 UTC  
> Updated_at: 2021-10-01 08:33:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#discussion_r720053621  

`std::forward` and the `static_cast` (where right) are completely equivalent. The advantage of using `std::forward` is a better understanding of the intent of the developer.  
  
**Edit:** fix the typo indent/intent

> Username: lrineau  
> Created_at: 2021-10-01 12:57:32 UTC  
> Updated_at: 2021-10-01 12:57:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/366#discussion_r720221467  

Brilliant! Now that I see your commit e4827ced2298503178f1d855979d605626eda9ad... of course it was not `std::forward`, but `std::move`, and now the code is a lot clearer to read! Thanks.


---
