# #1351 Extensions/add geojson writer [Merged]

> Username: barendgehrels  
> Created at: 2024-12-06 18:50:03 UTC  
> Updated at: 2025-01-10 17:32:55 UTC  
> Merged at: 2025-01-10 17:32:51 UTC  
> Closed at: 2025-01-10 17:32:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/1351  

This is how I usually test now. It is way more convenient than SVG.  
  
For example (`case_multi_simplex`), in QGis  
  
![image](https://github.com/user-attachments/assets/508b90c0-144e-4106-a986-77c561caa16f)  
  
## update  
  
The last commits also allow showing operation types and travel-to information, often quite useful.  
  
<img width="890" alt="Screenshot 2024-12-15 at 11 28 55" src="https://github.com/user-attachments/assets/fd6de1df-f322-4efe-834d-6e8bbcf0c2a7" />  
  
<img width="800" alt="Screenshot 2024-12-15 at 11 21 52" src="https://github.com/user-attachments/assets/c37c6eca-73aa-459b-bd6a-930fc8914999" />

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-12-06 18:52:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1351#pullrequestreview-2485639195  

📁 include/boost/geometry/extensions/gis/io/geojson/geojson_writer.hpp

```diff
  84 |+         feature_count++;
  85 |+ 
  86 |+         m_out << cbopen() << std::endl << ttag() << colon() << quote() << "Feature" << quote();
```

> Username: barendgehrels  
> Created_at: 2024-12-06 18:52:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1351#discussion_r1873844569  

It's in extensions, because in the end it is (probably) better to use `boost.json` for this functionality.  
But when I started it (this was somewhere in 2023), I was not yet aware of it.  
Since them I'm using it in various tests, mostly in `overlay.cpp` - but it was never committed.

> Username: tinko92  
> Created_at: 2024-12-14 15:53:18 UTC  
> Updated_at: 2024-12-15 03:13:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/1351#discussion_r1885104061  

I think this header looks good and maybe preferable over an additional dependency since it is relatively short and GeoJSON does seem to need only a small subset of JSON?  
  
ttag(), gtag() and ctag() never seem to appear without a colon(), seems verbose to not include the ':' in the definition.  
  
Edit: Also, CppCoreGuidelines recommends '\n' over std::endl (guideline SL.io.50) due to the performance cost of redundant flushes with std::endl.

> Username: barendgehrels  
> Created_at: 2024-12-15 11:29:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1351#discussion_r1885670510  

✅ great, all handled (`ttag` etc are gone)  
  
Yes maybe it shouldn't really depend on JSON. But we cannot read it yet. We'll see.


---

## Comment 2

> Username: barendgehrels  
> Created_at: 2024-12-06 18:53:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1351#issuecomment-2523954849  

@tinko92 for some reason I cannot add you to the reviewers

---

## Review 3 [Commented]

> Username: tinko92  
> Created_at: 2024-12-14 16:36:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1351#pullrequestreview-2503910694  

📁 include/boost/geometry/extensions/gis/io/geojson/geojson_writer.hpp

```diff
  68 |+     static char quote() { return '"'; }
  69 |+     static char cbopen() { return '{'; }
  70 |+     static char cbclose() { return '}'; }
```

> Username: tinko92  
> Created_at: 2024-12-14 16:02:26 UTC  
> Updated_at: 2024-12-14 16:36:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1351#discussion_r1885106707  

Is there a motivation for these functions on lines  62, 63, 67-70 if they do not maintain/validate any state, rather than writing the chars/strings explicitly into the output stream where used?

> Username: barendgehrels  
> Created_at: 2024-12-15 10:10:02 UTC  
> Updated_at: 2024-12-15 11:55:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1351#discussion_r1885630146  

There is a weak motivation, my habits coming from another code base where I'm working on, where it is the preferred way not to use constants or literals directly in the code.  
  
But indeed - no need to adhere to that here. I'll update it, thanks.

> Username: barendgehrels  
> Created_at: 2024-12-15 11:29:36 UTC  
> Updated_at: 2024-12-15 11:38:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1351#discussion_r1885670642  

✅ all gone, and optimized a bit using `R("` etc.

---

📁 include/boost/geometry/extensions/gis/io/geojson/geojson_writer.hpp

```diff
 135 |+     {
 136 |+         start_property();
 137 |+         m_out << quote() << name << quote() << colon() << value;
```

> Username: tinko92  
> Created_at: 2024-12-14 16:34:29 UTC  
> Updated_at: 2024-12-14 16:36:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1351#discussion_r1885115097  

Maybe using something like  
```cpp  
if BOOST_GEOMETRY_CONSTEXPR (std::is_same<T, std::string>::value || std::is_same<T, const char*>::value)  
```  
would be more clear than two overloads.

> Username: barendgehrels  
> Created_at: 2024-12-15 11:30:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/1351#discussion_r1885670911  

✅ great idea. the `const char*` part didn't catch all, but with copilot it's optimized using  
```  
std::is_same<T, std::string>::value  
              || (std::is_array<T>::value  
                  && std::is_same<typename std::remove_extent<T>::type, char>::value)  
```

> Username: barendgehrels  
> Created_at: 2024-12-15 11:42:22 UTC  
> Updated_at: 2024-12-15 11:42:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1351#discussion_r1885674559  

Or, even better and simpler:  
```  
constexpr bool needs_quotes   
            = std::is_same<T, std::string>::value  
              || std::is_same<typename std::remove_extent<T>::type, char>::value;  
```  
and now a `char` property also works, great. I'll update again.


---

## Comment 4

> Username: tinko92  
> Created_at: 2024-12-20 10:26:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/1351#issuecomment-2556712247  

Looks good to me!

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2024-12-30 07:25:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/1351#issuecomment-2565116007  

Last push adds multi-linestring and multi-point, for a (not yet committed) test of buffer on multi linestring.  
  
<img width="932" alt="image" src="https://github.com/user-attachments/assets/444d5843-7a9b-4882-bb9f-e56e318e97c4" />

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2024-12-30 07:26:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1351#issuecomment-2565116726  

<img width="922" alt="image" src="https://github.com/user-attachments/assets/978f575d-9e52-4030-ba6f-a1e3b12291c2" />

---

## Review 7 [Approved]

> Username: vissarion  
> Created_at: 2025-01-10 10:57:26 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1351#pullrequestreview-2542150896  

LGTM thanks!

---
