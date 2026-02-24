# #317 - Improve dijkstra heap performance test [Closed]

> Username: hdu-sdlzx  
> Created at: 2022-12-13 07:53:32 UTC  
> Updated at: 2022-12-19 22:52:26 UTC  
> Closed at: 2022-12-19 22:52:26 UTC  
> Url: https://github.com/boostorg/graph/issues/317  

In dijkstra_heap_performance.cpp we have:  
  
    std::cout << boost::timer::format(relaxed_heap_time) << " seconds.\n"  
              << "Speedup = " << (binary_heap_time.user / relaxed_heap_time.user)  
              << ".\n";  
  
On my x86 computer, the user time is 0.000 as the random graph is rather small.  
  
[  298s] ====== BEGIN OUTPUT ======  
[  298s] Generating graph...10000 vertices, 100000 edges.  
[  298s] Running Dijkstra's with binary heap... 0.003279s wall, 0.000000s user + 0.000000s system = 0.000000s CPU (n/a%)  
[  298s]  seconds.  
[  298s] Running Dijkstra's with d-ary heap (d=4)...  
[  298s] EXIT STATUS: 136  
[  298s] ====== END OUTPUT ======  
  
So maybe we should generate a larger graph to test, or just delete the speedup output?  
BTW, will anyone check the speedup manually?

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2022-12-14 03:47:02 UTC  
> Url: https://github.com/boostorg/graph/issues/317#issuecomment-1350353274  

Even though the year-on-year increase in single-threaded CPU speed dropped off since this benchmark was written in 2004, 18 years is certainly long enough to render most benchmark data set sizes invalid. We could simply increase the size to match today's CPU speed, but that will just become invalid again in the future. What it should do is keep doubling the size until the measured speed is non-zero. That way it will also work efficiently when someone does something unusual and runs it on a simulated or ancient CPU, etc.  
  
Is this benchmark run automatically as part of the tests? I hope not, I think it should be a convenience utility that someone can run if they are interested in it.

---

## Comment 2

> Username: hdu-sdlzx  
> Created at: 2022-12-16 02:40:49 UTC  
> Url: https://github.com/boostorg/graph/issues/317#issuecomment-1354103324  

> Is this benchmark run automatically as part of the tests? I hope not, I think it should be a convenience utility that someone can run if they are interested in it.  
  
It is listed in test/Jamfile.v2 just like other testcases and I've created a PR to comment out this test.  
Do we have other performance tests?

---

## Comment 3

> Username: jeremy-murphy  
> Created at: 2022-12-17 09:45:54 UTC  
> Url: https://github.com/boostorg/graph/issues/317#issuecomment-1356147842  

Thanks. I'm not sure, I would have to look.
