# #205 - Interpolation (Catmull-Rom): Add a constructor which doesn't move the input [Closed]

> Username: FlorentTomi  
> Created at: 2019-05-16 13:37:58 UTC  
> Updated at: 2019-05-16 22:04:54 UTC  
> Closed at: 2019-05-16 22:04:53 UTC  
> Url: https://github.com/boostorg/math/issues/205  

The boost::math::catmull_rom constructors take either a `std::vector <Point>&&` or an `initializer_list` (I assume it is for performance).  
  
In my case, I don't mind copying the input vector and reallocate the 3 more points necessary for the algorithm.   
  
Maybe provide a constructor with a `std::vector <Point> const&` (or, better, a templated Container const&) which copy a container into the `std::vector <Point>` member ?

---

## Comment 1

> Username: NAThompson  
> Created at: 2019-05-16 14:51:07 UTC  
> Url: https://github.com/boostorg/math/issues/205#issuecomment-493100458  

@FlorentTomi : I'm ambivalent about providing a constructor with `std::vector<Point> const &`, because all it will do is memcopy and then call the move constructor. Forcing people to make the memcopy themselves makes them recognize that this is an expensive operation that should be only be performed if necessary. You can always get your data back via `std::vector<Point>&& get_points()` if you need it (though this destroys the Catmull-Rom class).  
  
I agree that a templated container which contains a point type, which has a value type which is a floating point type, is better. But I ran into some trouble when I was working on this and decided that templating on the Point type was the most generic structure I could reliably get to compile. I'll revisit this and see if I can get past the original trouble I encountered.

---

## Comment 2

> Username: FlorentTomi  
> Created at: 2019-05-16 15:18:48 UTC  
> Url: https://github.com/boostorg/math/issues/205#issuecomment-493111914  

I understand your point (even though I tend to give more flexibility, at the expense of more documentation to expose the risks).  
  
Will the `get_points` function give me the exact same vector than the input one ? The documentation isn't really clear on the subject. My guess is that it will be different (or the point of moving the input vector won't make much sense), but how ?

---

## Comment 3

> Username: NAThompson  
> Created at: 2019-05-16 17:15:52 UTC  
> Updated at: 2019-05-16 17:16:23 UTC  
> Url: https://github.com/boostorg/math/issues/205#issuecomment-493155468  

It's not the same vector; but it contains the exact same information with a little redundancy. The vector is extended by a couple elements so the curve forms a closed loop with the first and last points being identical.  
  
Actually, I think it makes more sense if the vector is identical, but I guess this is a point of taste.

---

## Comment 4

> Username: NAThompson  
> Created at: 2019-05-16 17:23:22 UTC  
> Url: https://github.com/boostorg/math/issues/205#issuecomment-493158155  

@jzmaddock : How do you feel about a backwards incompatible change to template on a RandomAccessContainer? Worth it or not?

---

## Comment 5

> Username: jzmaddock  
> Created at: 2019-05-16 17:47:44 UTC  
> Url: https://github.com/boostorg/math/issues/205#issuecomment-493166744  

Define it as:  
  
```  
template<class Point, class Container = std::vector<Point> >  
    class catmull_rom;  
```  
  
And it's completely backwards compatible isn't it?

---

## Comment 6

> Username: NAThompson  
> Created at: 2019-05-16 18:42:40 UTC  
> Url: https://github.com/boostorg/math/issues/205#issuecomment-493185938  

Should be; strangely I cannot get it to compile on either clang or gcc-8:  
  
On gcc I get:  
  
```bash  
./include/boost/math/interpolators/catmull_rom.hpp:71:41: error: type/value mismatch at argument 2 in template parameter list for 'template<class Point, class RandomAccessContainer> class boost::math::catmull_rom'  
 catmull_rom<Point, RandomAccessContainer>::catmull_rom(RandomAccessContainer&& points, bool closed, typename Point::value_type alpha) : m_pnts(std::move(points))  
                                         ^  
```  
  
and on clang I get:  
  
```bash  
./include/boost/math/interpolators/catmull_rom.hpp:70:23: fatal error: use of class template 'RandomAccessContainer'  
      requires template arguments; argument deduction not allowed in template parameter  
template<class Point, RandomAccessContainer >  
                      ^~~~~~~~~~~~~~~~~~~~~  
../../boost/concept_check.hpp:762:17: note: template is declared here  
  BOOST_concept(RandomAccessContainer,(C))  
                ^  
```  
  
and following this error message I get   
  
```bash  
In file included from test/catmull_rom_test.cpp:16:  
./include/boost/math/interpolators/catmull_rom.hpp:70:66: fatal error: expected '(' for function-style cast or type  
      construction  
template<class Point, RandomAccessContainer = std::vector<Point> >  
                                              ~~~~~~~~~~~~~~~~~~ ^  
```  
  
Can you get it to work on MSVC?

---

## Comment 7

> Username: jzmaddock  
> Created at: 2019-05-16 19:41:30 UTC  
> Url: https://github.com/boostorg/math/issues/205#issuecomment-493205132  

After fixing lots of search and replace errors in my first attempt(!), this works for me with both msvc and gcc:  
  
```  
diff --git "a/include/boost/math/interpolators/catmull_rom.hpp" "b/include/boost/math/interpolators/catmull_rom.hpp"  
index 05ad666de..0c47d1779 100644  
--- "a/include/boost/math/interpolators/catmull_rom.hpp"  
+++ "b/include/boost/math/interpolators/catmull_rom.hpp"  
@@ -33,14 +33,14 @@ namespace boost{ namespace math{  
         }  
     }  
   
-template <class Point>  
+template <class Point, class RandomAccessContainer = std::vector<Point> >  
 class catmull_rom  
 {  
 public:  
   
-    catmull_rom(std::vector<Point>&& points, bool closed = false, typename Point::value_type alpha = (typename Point::value_type) 1/ (typename Point::value_type) 2);  
+    catmull_rom(RandomAccessContainer&& points, bool closed = false, typename Point::value_type alpha = (typename Point::value_type) 1/ (typename Point::value_type) 2);  
   
-    catmull_rom(std::initializer_list<Point> l, bool closed = false, typename Point::value_type alpha = (typename Point::value_type) 1/ (typename Point::value_type) 2) : catmull_rom(std::vector<Point>(l), closed, alpha) {}  
+    catmull_rom(std::initializer_list<Point> l, bool closed = false, typename Point::value_type alpha = (typename Point::value_type) 1/ (typename Point::value_type) 2) : catmull_rom<Point, RandomAccessContainer>(RandomAccessContainer(l), closed, alpha) {}  
   
     typename Point::value_type max_parameter() const  
     {  
@@ -56,19 +56,19 @@ public:  
   
     Point prime(const typename Point::value_type s) const;  
   
-    std::vector<Point>&& get_points()  
+    RandomAccessContainer&& get_points()  
     {  
         return std::move(m_pnts);  
     }  
   
 private:  
-    std::vector<Point> m_pnts;  
+    RandomAccessContainer m_pnts;  
     std::vector<typename Point::value_type> m_s;  
     typename Point::value_type m_max_s;  
 };  
   
-template<class Point>  
-catmull_rom<Point>::catmull_rom(std::vector<Point>&& points, bool closed, typename Point::value_type alpha) : m_pnts(std::move(points))  
+template <class Point, class RandomAccessContainer>  
+catmull_rom<Point, RandomAccessContainer>::catmull_rom(RandomAccessContainer&& points, bool closed, typename Point::value_type alpha) : m_pnts(std::move(points))  
 {  
     size_t num_pnts = m_pnts.size();  
     //std::cout << "Number of points = " << num_pnts << "\n";  
@@ -122,8 +122,8 @@ catmull_rom<Point>::catmull_rom(std::vector<Point>&& points, bool closed, typena  
 }  
   
   
-template<class Point>  
-Point catmull_rom<Point>::operator()(const typename Point::value_type s) const  
+template <class Point, class RandomAccessContainer>  
+Point catmull_rom<Point, RandomAccessContainer>::operator()(const typename Point::value_type s) const  
 {  
     using std::size;  
     if (s < 0 || s > m_max_s)  
@@ -182,8 +182,8 @@ Point catmull_rom<Point>::operator()(const typename Point::value_type s) const  
     return B1_or_C;  
 }  
   
-template<class Point>  
-Point catmull_rom<Point>::prime(const typename Point::value_type s) const  
+template <class Point, class RandomAccessContainer>  
+Point catmull_rom<Point, RandomAccessContainer>::prime(const typename Point::value_type s) const  
 {  
     using std::size;  
     // https://math.stackexchange.com/questions/843595/how-can-i-calculate-the-derivative-of-a-catmull-rom-spline-with-nonuniform-param  
```

---

## Comment 8

> Username: NAThompson  
> Created at: 2019-05-16 22:04:53 UTC  
> Url: https://github.com/boostorg/math/issues/205#issuecomment-493247888  

Shame on me for suffering so much to get this pretty standard C++ feature running! In any case, this is now fixed in [PR 206](https://github.com/boostorg/math/pull/206)
