# #148 - examples crash when run [Open]

> Username: jzmaddock  
> Created at: 2018-12-16 19:09:01 UTC  
> Updated at: 2019-01-26 08:40:54 UTC  
> Url: https://github.com/boostorg/graph/issues/148  

The following two examples crash at runtime:  
  
astar_maze.cpp  
kevin-bacon2.cpp  
  
Sorry but I don't see what the issue is off hand.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-12-18 17:58:18 UTC  
> Url: https://github.com/boostorg/graph/issues/148#issuecomment-448312050  

Need to add last-mod-time.cpp to this list as well (msvc-12.0).

---

## Comment 2

> Username: deinst  
> Created at: 2019-01-24 14:10:20 UTC  
> Url: https://github.com/boostorg/graph/issues/148#issuecomment-457209506  

astar_maze works for me (osx)  
last-mod-time has problems with the stat call on osx (non fatal, but incorrect results).  This would be easiest to fix by using the c++17 filesystem header, is that permissible?

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-01-24 19:30:45 UTC  
> Url: https://github.com/boostorg/graph/issues/148#issuecomment-457326208  

>astar_maze works for me (osx)  
  
Works for me on Ubuntu - it's just msvc where it's crashing.  
  
Here's what I've found:  
* MSVC has an "uninitialised variable guard" in debug mode and this is causing the crash, because an uninitialised value is being passed to new[].  
* The problem is the value of `map::m_barrier_grid.m_g.m_num_vertices`.  
* Prior to the call of `m.solve()` in `main()` everything is fine, but during the function call initialization, msvc debug code overwrites the stack space with it's "special" value as a guard against subsequent uninitialized reads, this leads to the value of `this->m_barrier_grid.m_g.m_num`_vertices to change to the "uninitialized" special value and the subsequent crash.  
* Nothing else inside `*this` seems to be touched.  
* My assumption is that something during initialization of the maze was created on the stack so that `this->m_barrier_grid.m_g.m_num_vertices` goes out of scope.  But I don't see how.  
  
So at this point I'm stumped.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2019-01-24 19:37:12 UTC  
> Url: https://github.com/boostorg/graph/issues/148#issuecomment-457328321  

>last-mod-time has problems with the stat call on osx (non fatal, but incorrect results). This would be easiest to fix by using the c++17 filesystem header, is that permissible?  
  
Ooops looks like I added that one to the wrong bug report: the example uses several unix-isms which will never compile on windows.  So yes, either <filesystem> or <boost/filesystem.hpp> would be good choices IMO.

---

## Comment 5

> Username: deinst  
> Created at: 2019-01-25 14:58:06 UTC  
> Url: https://github.com/boostorg/graph/issues/148#issuecomment-457599130  

> astar_maze works for me (osx)  
  
Even valgrind is not complaining, so I cannot test anything.  I think llvm has similar tools to msvc, and I'll try to figure out what they are.  
  
In the mean time, one thing that smells funny to me is the constructors of the member of `maze`.  The initialization of `m_barrier_grid` calls  `create_barrier_grid` which uses `m_barriers` which is initialized after `barrier_grid`, and so is uinititialized during the call to `create_barrier_grid`.  
  
If this is the problem, then swapping lines 123 and 125 (the definitions of `m_barrier_grid` and `m_barrier`) so that `m_barrier` is constructed first should fix things.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2019-01-25 18:40:20 UTC  
> Url: https://github.com/boostorg/graph/issues/148#issuecomment-457676640  

OK, there is a bug somewhere, but it's masked by some very clever named-return-value optimisations with gcc and clang.  Here's what I have so far:  
  
* During:  
```  
maze random_maze(std::size_t x, std::size_t y) {  
  maze m(x, y);  
```  
The member variable `m.m_barrier_grid.m_g` is of type `const boost::grid_graph<2,unsigned int,unsigned int>&` and "points to" the first member of `m` which is `m.m_grid`.  
* When the function returns, then the reference `m.m_barrier_grid.m_g` is left still pointing to the variable that was created inside the subroutine, and not new the copy that was returned.  
* When NVRO is in effect, then the address of the variable inside `random_maze` _is the same as the one inside main_ and the issue is masked.  GCC and Clang it appears do this all the time (and very cleverly so), while msvc only does it as an optimisation in release mode.  
* This all tracks back to `filtered_graph_base` which stores actually stores the reference - IMO this type should be non-copyable to prevent this type of bug (it's already non-assignable of course).  I might just change it locally and see what if anything breaks.  
* I wonder if there are other issues like this lurking?

---

## Comment 7

> Username: jzmaddock  
> Created at: 2019-01-25 19:51:03 UTC  
> Url: https://github.com/boostorg/graph/issues/148#issuecomment-457703812  

Making `filtered_graph_base` non-copyable breaks a lot of code - there are a lot of factory functions that create filtered_graph's.  filter_graph is actually documented to store a reference and mentions that care should be taken with it's use, but that still didn't stop the authors from falling into this trap.  
  
In the mean time I have an easy fix for astar_maze.cpp which I'll push shortly...

---

## Comment 8

> Username: jzmaddock  
> Created at: 2019-01-26 08:40:54 UTC  
> Url: https://github.com/boostorg/graph/issues/148#issuecomment-457813932  

Pushed fixes for astar_maze.cpp: https://github.com/boostorg/graph/commit/32bc0e1dff9554e179f54cb287af9a5541359a25
