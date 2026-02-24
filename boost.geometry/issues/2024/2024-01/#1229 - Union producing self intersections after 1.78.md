# #1229 - Union producing self intersections after 1.78 [Closed]

> Username: jpan127  
> Created at: 2024-01-16 19:52:15 UTC  
> Updated at: 2024-03-05 07:55:13 UTC  
> Closed at: 2024-03-03 22:45:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/1229  

`Minimal Reproducible Example`: https://godbolt.org/z/Tdb51rcY8  
  
Hi, I have been `union_`ing a set of triangular polygons. Our repo recently updated from `1.71` to `1.82` which broke one of our union functions. The above example takes 2 triangles, that share an edge, and unions them. The result contains duplicate points and fails `boost::geometry::is_valid`.  
  
All versions from `1.79 onwards` fails this example.  
  
I noticed that the release notes (https://beta.boost.org/doc/libs/1_79_0/libs/geometry/doc/html/geometry/release_notes.html) show issue (https://github.com/boostorg/geometry/issues/838) which mentions `BOOST_GEOMETRY_NO_ROBUSTNESS`. Defining that for `1.71` also leads to the same failure if that gives any hints.

---

## Comment 1

> Username: barendgehrels  
> Created at: 2024-03-03 09:50:17 UTC  
> Updated at: 2024-03-03 09:50:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/1229#issuecomment-1975105439  

Thanks for the report. I cannot reproduce it anymore. It is fixed by the fix in 68a225daf by @vissarion

---

## Comment 2

> Username: barendgehrels  
> Created at: 2024-03-03 22:45:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/1229#issuecomment-1975384467  

I'll close the issue because it is fixed and a testcase has been added.

---

## Comment 3

> Username: cjamin  
> Created at: 2024-03-05 07:55:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/1229#issuecomment-1978154988  

Will this fix be released with Boost 1.85?
