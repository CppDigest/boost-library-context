# #247 Feature/stats [Merged]

> Username: joaquintides  
> Created at: 2024-05-06 16:54:01 UTC  
> Updated at: 2024-05-08 15:19:55 UTC  
> Merged at: 2024-05-08 15:19:12 UTC  
> Closed at: 2024-05-08 15:19:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/247  

Open-addressing and concurrent containers can be enabled to keep running statistics of  
  
* Insertion: count and probe length  
* Successul lookup: count, probe length and number of comparisons  
* Unsuccessul lookup: count, probe length and number of comparisons  
  
The provided API is:  
```cpp  
stats get_stats() const;  
void reset_stats() noexcept;  
```

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-05-06 17:02:44 UTC  
> Url: https://github.com/boostorg/unordered/pull/247#issuecomment-2096509443  

An automated preview of the documentation is available at [https://247.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://247.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-05-06 23:17:06 UTC  
> Url: https://github.com/boostorg/unordered/pull/247#issuecomment-2097081881  

Why do the clang-12 jobs fail?

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-05-06 23:22:36 UTC  
> Url: https://github.com/boostorg/unordered/pull/247#issuecomment-2097086386  

Maybe we could have, in the documentation, examples of how the stats look like for a good hash function and for a bad hash function marked with is_avalanching, so that people can get a feel what they can expect.

---

## Comment 4

> Username: joaquintides  
> Created_at: 2024-05-07 09:14:12 UTC  
> Updated_at: 2024-05-07 09:14:28 UTC  
> Url: https://github.com/boostorg/unordered/pull/247#issuecomment-2097826220  

> Why do the clang-12 jobs fail?  
  
No idea, but it's not related to this PR. The following CI run on `develop` used to run on Apr 30:  
  
https://github.com/boostorg/unordered/actions/runs/8890736153/attempts/1  
  
but now fails on the same clang-12 jobs as we observe with the PR:  
  
https://github.com/boostorg/unordered/actions/runs/8890736153  
  
The problem is with `foa_mmap_*` tests, maybe due to some recent changes in Boost.Interprocess? @cmazakas is investigating it.

---

## Comment 5

> Username: joaquintides  
> Created_at: 2024-05-07 11:01:45 UTC  
> Url: https://github.com/boostorg/unordered/pull/247#issuecomment-2098125474  

> Maybe we could have, in the documentation, examples of how the stats look like for a good hash function and for a bad hash function marked with is_avalanching, so that people can get a feel what they can expect.  
  
I don't know if this is too technical and best brought to an external article. Anyway, I've created this example for you to tell me if you feel it's instructive enough:  
  
https://gist.github.com/joaquintides/ce3f5adaeb2d27214f46cb42f9ec8b8b  
  
Output:  
```  
           boost::unordered_flat_map: 27601 ms  
                           insertion: probe length 1.07282  
                   successful lookup: probe length 1.00123, num comparisons 1.01218  
                 unsuccessful lookup: probe length 1.00287, num comparisons 0.021629  
   boost::unordered_flat_map, FNV-1a: 25102 ms  
                           insertion: probe length 1.07286  
                   successful lookup: probe length 1.00124, num comparisons 1.01111  
                 unsuccessful lookup: probe length 1.00287, num comparisons 0.0221967  
 boost::unordered_flat_map, bad_hash: 41711 ms  
                           insertion: probe length 1.02698  
                   successful lookup: probe length 1.00047, num comparisons 4.00251  
                 unsuccessful lookup: probe length 1.00428, num comparisons 5.41056  
```

---

## Comment 6

> Username: pdimov  
> Created_at: 2024-05-07 14:22:09 UTC  
> Url: https://github.com/boostorg/unordered/pull/247#issuecomment-2098528931  

Yes, I think it will be better than having nothing.  
  
That's a "slightly_bad_hash" though, from the look of it. Maybe we can also add `h * 31 + x` as the actually bad hash?  
```  
           boost::unordered_flat_map:   338 ms  
                           insertion: probe length 1.08697  
                   successful lookup: probe length 1.05975, num comparisons 1.02168  
                 unsuccessful lookup: probe length 1.12035, num comparisons 0.0376587  
   boost::unordered_flat_map, FNV-1a:   401 ms  
                           insertion: probe length 1.09567  
                   successful lookup: probe length 1.06202, num comparisons 1.0227  
                 unsuccessful lookup: probe length 1.12195, num comparisons 0.040527  
boost::unordered_flat_map, slightly_bad_hash:   809 ms  
                           insertion: probe length 1.03443  
                   successful lookup: probe length 1.04137, num comparisons 6.22152  
                 unsuccessful lookup: probe length 1.29334, num comparisons 11.0335  
 boost::unordered_flat_map, bad_hash: 16275 ms  
                           insertion: probe length 699.218  
                   successful lookup: probe length 590.183, num comparisons 43.4886  
                 unsuccessful lookup: probe length 1361.65, num comparisons 75.238  
```  
  
I had to decrease N to 50,000, because otherwise the bad hash was taking ages.  
  
`h * 31 + x` is a well-known hash function, e.g. https://stackoverflow.com/questions/299304/why-does-javas-hashcode-in-string-use-31-as-a-multiplier.  
  
The changes to the above source are that I renamed `bad_hash` to `slightly_bad_hash` and added  
  
```  
// bad hash  
  
struct bad_hash  
{  
    using is_avalanching = void;  
  
    std::size_t operator()( std::string const& s ) const  
    {  
       std::size_t h = s.size();  
  
       for( auto ch: s )  
       {  
         h *= 31;  
         h += static_cast<std::size_t>( ch );  
       }  
  
       return h;  
    }  
};  
  
template<class K, class V> using boost_unordered_flat_map_bad_hash =  
    boost::unordered_flat_map<K, V, bad_hash, std::equal_to<K>>;  
```

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2024-05-07 18:22:43 UTC  
> Url: https://github.com/boostorg/unordered/pull/247#issuecomment-2099045390  

An automated preview of the documentation is available at [https://247.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://247.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 8

> Username: joaquintides  
> Created_at: 2024-05-07 18:24:03 UTC  
> Updated_at: 2024-05-07 18:25:41 UTC  
> Url: https://github.com/boostorg/unordered/pull/247#issuecomment-2099047463  

Added a section on hash quality, avalanching and stats:  
  
https://247.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html#hash_quality  
  
Not particularly pleased with the looks of the table I used to describe the members of a `stats` object, suggestions for improvement welcome.  
  
On a side note, docs are getting unwieldy. We should break them down in pages, at least.

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2024-05-08 09:22:45 UTC  
> Url: https://github.com/boostorg/unordered/pull/247#issuecomment-2100145191  

An automated preview of the documentation is available at [https://247.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://247.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2024-05-08 09:52:44 UTC  
> Url: https://github.com/boostorg/unordered/pull/247#issuecomment-2100201698  

An automated preview of the documentation is available at [https://247.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://247.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---
