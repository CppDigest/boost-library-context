# #383 - Why the implementation of Lengauer-Tarjan uses std::deque for a bucket? [Closed]

> Username: samolisov  
> Created at: 2024-09-04 08:19:00 UTC  
> Updated at: 2025-03-04 13:19:32 UTC  
> Closed at: 2025-03-04 13:19:32 UTC  
> Url: https://github.com/boostorg/graph/issues/383  

I was going through the implementation of the Lengauer-Tarjan algorithm in BGL's domibator_tree.hpp and have found that there is a vector of std::deque to store buckets: vertices with the given semidominator. https://github.com/boostorg/graph/blob/5557ccf92147731eb02fcd8254b451a29b111348/include/boost/graph/dominator_tree.hpp#L198  
  
I read the original paper as well as some explanations carefully and found nowhere that the order of procesding `buckets[parent[w]]` makes any sense. Moreover, I see the deque is used there just as a container, not as a backend for the `std::queue` adapter: the code pushes vertices at the back of the deque, walks through the deque with a corresponding iterators and then clears it. In my understanding, std::vector<std::vector<>> might be used there.  
  
Could you tell me why the deque is used to store a bucket in the code? My idea is to eliminate an overhead for the vector reallocation during the repeatedly performed `push_back()` and `clear()` operations, so this is a technical thing only and has no impact on the correctness of the algorithm.  
  
Thank you very much.

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2024-09-09 04:48:42 UTC  
> Url: https://github.com/boostorg/graph/issues/383#issuecomment-2337107524  

Yeah, that bucket type should be `vector`, not `deque`. In the early 2000s, there was a bit of a misguided preference for `deque`. Can't explain it otherwise.  
  
Other performance improvements to consider:  
  
- Changing the type of `buckets_` to `boost::unordered_flat_map<Vertex, std::vector<Vertex>>`, and instead of clearing the bucket, remove it from the hash map.   
- `std::vector< Vertex > semi_, ancestor_, samedom_, best_;` these are all the same size (number of vertices in g) and allocated separately, however they are accessed in the same pattern, e.g.:  
```  
            put(semiMap_, n, s);  
  
            // 2. Calculation of n's dominator is deferred until  
            // the path from s to n has been linked into the forest  
            get(bucketMap_, s).push_back(n);  
            get(ancestorMap_, n) = p;  
            get(bestMap_, n) = n;  
  
```  
so changing from a "struct of vectors" to a "vector of structs" would probably improve cache efficiency. Not sure about `samedom_`, it doesn't seem to fit the pattern.  
  
I recommend using Google's benchmark library to get your results. Please include a graph of the comparison in any pull request. Also, please don't do all the performance changes together in one PR -- better to do it one at a time so as not to hurt my brain. Thanks.  :)

---

## Comment 2

> Username: samolisov  
> Created at: 2024-09-11 07:29:20 UTC  
> Url: https://github.com/boostorg/graph/issues/383#issuecomment-2342871819  

@jeremy-murphy Thank you very much for the answer and a lot of information and suggestion. I believe developing benchmarks with the google benchmark project ismay itself be a good first issue for one who would like to join boost development. They may be started from a set of "real" CFGs from the boost sources or some other applications. LLVM or even Bolt may help a lot to collect the CFGs. Then the benchmarks may be added to the `test` directory as google abseil does it for its benchmarks and run as a part of the test suite to allow anyone reproduce the results. After this we may change the implementation of the algorithm to pick the correct containers. This is my vision of this task.

---

## Comment 3

> Username: jeremy-murphy  
> Created at: 2024-09-11 08:19:15 UTC  
> Url: https://github.com/boostorg/graph/issues/383#issuecomment-2342972419  

That sounds essentially right. I wouldn't trouble yourself to get 'real' CFGs from LLVM for example, it should be sufficient to generate synthetic ones? If we add Google benchmark tests to the library, they will be optional, as we don't want to make that library a required dependency.   
Start small, commit early, make a draft pull request as soon as you have something you want to discuss!

---

## Comment 4

> Username: samolisov  
> Created at: 2024-12-19 11:17:32 UTC  
> Updated at: 2024-12-20 05:09:45 UTC  
> Url: https://github.com/boostorg/graph/issues/383#issuecomment-2553454756  

@jeremy-murphy Hello, sorry for a late response. I would like to implement this point:  
  
>> Changing the type of buckets_ to boost::unordered_flat_map<Vertex, std::vector<Vertex>>, and instead of clearing the bucket, remove it from the hash map.  
  
but have a question:  
  
What about initializing the `unordered_flat_map` with a number of vertices in the graph? Currently we have [the following](https://github.com/boostorg/graph/blob/develop/include/boost/graph/dominator_tree.hpp#L90) code to initialize the `std::vector` of `std::vectors` (`std::deque`s actually, but the replacement is just transparent):  
  
```c++  
        , buckets_(num_vertices(g))  
        , bucketMap_(make_iterator_property_map(buckets_.begin(), indexMap))  
```  
  
for `boost::unordered_flat_map` we have the following, [bucket count constructor](https://www.boost.org/doc/libs/1_83_0/libs/unordered/doc/html/unordered.html#unordered_flat_map_bucket_count_constructor):  
  
```c++  
explicit unordered_flat_map(size_type n,  
                            const hasher& hf = hasher(),  
                            const key_equal& eql = key_equal(),  
                            const allocator_type& a = allocator_type());  
```  
  
Here, `n` is not the number of elements but this is the number of *buckets*. I see no much difference and think we can have as many buckets as vertices in the graph but what about memory wasting/consumption? Also, as I see, there is no guarantee that all the key-value pairs will be initialized at the constructor. I believe we may use `reserve()` to allocate the corresponding amount of memory and then initialize every key (vertex descriptor) with the corresponding empty vector as the value.  
  
The second question is about using the **flat** map as a backbone for an `iterator_property_map`. The [**Iterator Invalidation**](https://www.boost.org/doc/libs/1_83_0/libs/unordered/doc/html/unordered.html#regular_iterator_invalidation) section in the documentation of `Boost.Unordered` says nothing about iterator invalidation on `erase()`, the only method we will use for the container **after** it and the corresponding `iterator_property_map` will be built and filled. The invalidation is occurs only during calls to the `rehash()`, `reserve()`, and, potentially, `insert()` members. I believe this is safe to make the `iterator_property_map` instance from an `unordered_flat_map` one.

---

## Comment 5

> Username: samolisov  
> Created at: 2024-12-20 05:02:43 UTC  
> Updated at: 2024-12-20 05:09:27 UTC  
> Url: https://github.com/boostorg/graph/issues/383#issuecomment-2556292863  

@jeremy-murphy   
  
About this suggestion:  
  
> Changing the type of buckets_ to boost::unordered_flat_map<Vertex, std::vector<Vertex>>, and instead of clearing the bucket, remove it from the hash map.  
  
I've replaced `std::vector< std::vector <Vertex> > buckets_` with `boost::unordered_flat_map< Vertex, std::vector< Vertex > >` and `iterator_property_map<...> bucketMap_`  with ` boost::associative_property_map< ... > bucketMap_`, respectively (this gets rid my concern about iterator stability in the `unordered_flat_map` class). Unfortunately, the times became worser:  
  
**baseline**  
```  
----------------------------------------------------------------------------------  
Benchmark                                        Time             CPU   Iterations  
----------------------------------------------------------------------------------  
Tarjan's paper (vertex list)                   945 ns          945 ns       738046  
Tarjan's paper  (vertex vector)                854 ns          854 ns       812712  
Appel. fig. 19.8 (vertex list)                 963 ns          963 ns       726102  
Appel. fig. 19.8  (vertex vector)              870 ns          870 ns       811134  
Muchnick. fig. 8.18 (vertex list)              566 ns          566 ns      1235880  
Muchnick. fig. 8.18  (vertex vector)           541 ns          541 ns      1290703  
Cytron's paper, fig. 9 (vertex list)          1144 ns         1144 ns       606261  
Cytron's paper, fig. 9  (vertex vector)       1053 ns         1053 ns       667039  
From a code, 186 BBs (vertex list)           12812 ns        12812 ns        55302  
From a code, 186 BBs (vertex vector)         11230 ns        11229 ns        61754  
```  
  
With the `unordered_flat_map` and erasing entries for vertices instead of clearing the corresponding vectors (values in the map):  
  
```  
----------------------------------------------------------------------------------  
Benchmark                                        Time             CPU   Iterations  
----------------------------------------------------------------------------------  
Tarjan's paper (vertex list)                  1303 ns         1303 ns       537294  
Tarjan's paper  (vertex vector)               1211 ns         1211 ns       574342  
Appel. fig. 19.8 (vertex list)                1315 ns         1315 ns       531879  
Appel. fig. 19.8  (vertex vector)             1219 ns         1219 ns       575134  
Muchnick. fig. 8.18 (vertex list)              762 ns          762 ns       917463  
Muchnick. fig. 8.18  (vertex vector)           760 ns          760 ns       919554  
Cytron's paper, fig. 9 (vertex list)          1548 ns         1548 ns       452332  
Cytron's paper, fig. 9  (vertex vector)       1446 ns         1446 ns       484960  
From a code, 186 BBs (vertex list)           15915 ns        15914 ns        44147  
From a code, 186 BBs (vertex vector)         14380 ns        14379 ns        48732  
```  
  
When I initialize an entry for every vertex with an empty vector in the `buckets_` map in the constructor of the `dominator_visitor` class, the times are worse again:  
  
```  
----------------------------------------------------------------------------------  
Benchmark                                        Time             CPU   Iterations  
----------------------------------------------------------------------------------  
Tarjan's paper (vertex list)                  1450 ns         1450 ns       483249  
Tarjan's paper  (vertex vector)               1319 ns         1319 ns       529584  
Appel. fig. 19.8 (vertex list)                1445 ns         1445 ns       482939  
Appel. fig. 19.8  (vertex vector)             1324 ns         1324 ns       531363  
Muchnick. fig. 8.18 (vertex list)              846 ns          846 ns       806287  
Muchnick. fig. 8.18  (vertex vector)           802 ns          802 ns       876838  
Cytron's paper, fig. 9 (vertex list)          1683 ns         1683 ns       417305  
Cytron's paper, fig. 9  (vertex vector)       1561 ns         1561 ns       447610  
From a code, 186 BBs (vertex list)           17704 ns        17702 ns        39568  
From a code, 186 BBs (vertex vector)         15942 ns        15941 ns        43184  
```  
  
Clearing the vectors for corresponding vertices instead of erasing the entries [at the end](https://github.com/boostorg/graph/blob/develop/include/boost/graph/dominator_tree.hpp#L170) of the `operator()` works a little bit slower but this is observed for large cases (the last two) only:  
  
(with the initialization of the `buckets_` map in the visitor's constructor):  
  
```  
----------------------------------------------------------------------------------  
Benchmark                                        Time             CPU   Iterations  
----------------------------------------------------------------------------------  
Tarjan's paper (vertex list)                  1388 ns         1388 ns       493841  
Tarjan's paper  (vertex vector)               1275 ns         1275 ns       546833  
Appel. fig. 19.8 (vertex list)                1457 ns         1457 ns       484569  
Appel. fig. 19.8  (vertex vector)             1316 ns         1316 ns       528623  
Muchnick. fig. 8.18 (vertex list)              825 ns          825 ns       847926  
Muchnick. fig. 8.18  (vertex vector)           802 ns          802 ns       872203  
Cytron's paper, fig. 9 (vertex list)          1653 ns         1653 ns       421746  
Cytron's paper, fig. 9  (vertex vector)       1543 ns         1543 ns       452688  
From a code, 186 BBs (vertex list)           19344 ns        19343 ns        36638  
From a code, 186 BBs (vertex vector)         17211 ns        17210 ns        40707  
```  
  
(without the initialization):  
  
```  
----------------------------------------------------------------------------------  
Benchmark                                        Time             CPU   Iterations  
----------------------------------------------------------------------------------  
Tarjan's paper (vertex list)                  1259 ns         1259 ns       553241  
Tarjan's paper  (vertex vector)               1184 ns         1184 ns       594146  
Appel. fig. 19.8 (vertex list)                1300 ns         1300 ns       537443  
Appel. fig. 19.8  (vertex vector)             1214 ns         1214 ns       574376  
Muchnick. fig. 8.18 (vertex list)              741 ns          741 ns       936624  
Muchnick. fig. 8.18  (vertex vector)           700 ns          700 ns       999325  
Cytron's paper, fig. 9 (vertex list)          1539 ns         1539 ns       459508  
Cytron's paper, fig. 9  (vertex vector)       1425 ns         1424 ns       490696  
From a code, 186 BBs (vertex list)           17325 ns        17324 ns        41183  
From a code, 186 BBs (vertex vector)         15990 ns        15989 ns        43576  
```  
(these numbers should be compared with the second chart in my comment, what we can see, the numbers are a little bit **worser** for the last two (large) cases: 17325 vs 15915 - 8% and 15990 vs 14380 - 10% but are a little bit **better** for small cases: 700 vs 760 - 8% for Muchnick or 1184 vs 1211 - 2% for Tarjan's paper.)  
  
I believe the numbers demonstrate that a `vector of vectors` is a true working horse for the implementation of the dominator tree algorithm and the changing needs an additional research at least. Anyway we should be very careful at this point.

---

## Comment 6

> Username: jeremy-murphy  
> Created at: 2024-12-21 07:17:49 UTC  
> Url: https://github.com/boostorg/graph/issues/383#issuecomment-2558032190  

Thanks for trying all these different ideas. Well, we fixed the essential issue of using `deque`, so feel free to close this now or after we finish the vector of structures PR.
