# #397 Fix the O(n^3) performance issue in `is_connected` utility function [Open]

> Username: Wavetrace  
> Created at: 2024-11-12 13:49:58 UTC  
> Updated at: 2025-12-27 10:23:06 UTC  
> Url: https://github.com/boostorg/graph/pull/397  

The `is_connected()` function is implemented in terms of `is_rechable()`, effectively checking if each vertex is reachable from each other vertex in a nested loop. This quickly becomes terribly slow as the number of vertices and edges grow, becoming roughly unusable with a square connected graph of size 20x20.  
  
This request fixes the issue by  
- using a single DFS walk for undirected graphs  
- employing the Tarjan's SCC algorithm for directed graphs  
  
This reduces the algorithm complexity from cubic to linear and makes it possible to use it on large graphs. See also the commit message in https://github.com/boostorg/graph/commit/80ae041e652130cfdcbeee72c2fd91fbf97e14b7  
  
This change is fully backward compatible with the previous implementation.   
  
It also adds a new `is_connected.cpp` test. Feel free to run this test on the previous implementation. This test passes without the new algorithm, but picks the size of 20x20 square to be considerably slow. This test also accepts an integer as argv[1],  the size of the square, so it is possible to observe the slow-down growing as the size increases (the default is 20).  
  
Please see an excerpt of my measurement below (I can upload this program as well). The previous implementation quickly becomes impracticably slow.  
  
I note, that `is_connected` is not documented and is not widely used. Still I consider it to be important to fully adhere to the interface specified in comment and preserve the compatibility, as it could have been used in non-public projects. In short one important decision here is to avoid any breaking changes.  
  
I foresee a few concerns with this request, which I'll be happy to discuss and address if possible:  
* the `graph_utility.hpp` header file seems to be designed as low-dependency. Including < vector > and <strong_components.hpp> there seems to violate that choice. But I think the practical aspect of this is reasonably minimal; while fixing the issue fully preserving the backward compatibility is important in practice.  
* the colormap is not used for directed graphs. `strong_components` do not accept the colormap and pass it to DFS. I don't see this as a big concern. This can be addressed in 2 ways: updating SCC interface to pass the colormap to DFS, or (probably better) provide the version of `is_connected` with a single argument and no colormap.  
* the version for directed graphs seems to now require `VertexIndexGraph` concept. (I think) This can be fixed by using `map` instead of `vector` for the component map (`comp_map`) in case the graph is not VertexIndexGraph. I'm not sure, how important is this, but this seems to be a deviation from the full backward compatibility.  
  
I consider a bit of follow-up work on this, which can also be done in a separate pull request:  
* provide single-argument `is_connected(const Graph& g)`. This will require including the colormap header into the graph_utility   
* document `is_connected` (and other utility functions like `is_reachable`)  
* add examples of the above  
  
fixes #398  
  
Measurement:  
```  
=== Testing size 1  
= Testing undirected, connected  
time: undirected, connected - prev implementation - elapsed     3.299e-06s  
time: undirected, connected - new  implementation - elapsed     1.179e-06s  
= Testing undirected, not connected  
time: undirected, not connected - prev implementation - elapsed         8.62e-07s  
time: undirected, not connected - new  implementation - elapsed         5.39e-07s  
= Testing directed, connected  
time: directed, connected - prev implementation - elapsed       8.22e-07s  
time: directed, connected - new  implementation - elapsed       2.636e-06s  
= Testing directed, not connected  
time: directed, not connected - prev implementation - elapsed   1.64e-06s  
time: directed, not connected - new  implementation - elapsed   1.555e-06s  
  
...  
=== Testing size 10  
= Testing undirected, connected  
time: undirected, connected - prev implementation - elapsed     0.0409115s  
time: undirected, connected - new  implementation - elapsed     5.703e-06s  
= Testing undirected, not connected  
time: undirected, not connected - prev implementation - elapsed         0.000392494s  
time: undirected, not connected - new  implementation - elapsed         4.8e-06s  
= Testing directed, connected  
time: directed, connected - prev implementation - elapsed       0.0240339s  
time: directed, connected - new  implementation - elapsed       7.925e-06s  
= Testing directed, not connected  
time: directed, not connected - prev implementation - elapsed   0.000261854s  
time: directed, not connected - new  implementation - elapsed   6.209e-06s  
  
...  
=== Testing size 24  
= Testing undirected, connected  
time: undirected, connected - prev implementation - elapsed     16.458s  
time: undirected, connected - new  implementation - elapsed     6.1249e-05s  
= Testing undirected, not connected  
time: undirected, not connected - prev implementation - elapsed         0.0312753s  
time: undirected, not connected - new  implementation - elapsed         6.9342e-05s  
= Testing directed, connected  
time: directed, connected - prev implementation - elapsed       7.45684s  
time: directed, connected - new  implementation - elapsed       4.9894e-05s  
= Testing directed, not connected  
time: directed, not connected - prev implementation - elapsed   0.01372s  
time: directed, not connected - new  implementation - elapsed   0.000215756s  
```

---

## Comment 1

> Username: Wavetrace  
> Created_at: 2024-11-12 13:59:46 UTC  
> Url: https://github.com/boostorg/graph/pull/397#issuecomment-2470605365  

I've also opened https://github.com/boostorg/graph/issues/398 to track this.

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2024-11-14 03:14:42 UTC  
> Url: https://github.com/boostorg/graph/pull/397#issuecomment-2475326095  

I only have one question: how does someone with almost no activity history in GitHub know so much about Boost.Graph? :)

---

## Comment 3

> Username: Wavetrace  
> Created_at: 2024-11-14 10:41:14 UTC  
> Url: https://github.com/boostorg/graph/pull/397#issuecomment-2476002951  

> I only have one question: how does someone with almost no activity history in GitHub know so much about Boost.Graph? :)  
  
A great question! Well, I read the book as soon at it was published ;) And Alexander Stepanov's recommendation meant a lot to me, I know him personally and appreciate his inventions a lot. I also ended up rolling up my own graph library before, because Boost was not welcome in my organization.  
  
As of GitHub, I hope to publish a few things soon, including the small project, where I encountered this error.  
  
One reason I actually hit it is that I go straight to the code first and resort to the documentation when the code doesn't work as I'd expect.  
  
Thanks for looking into this!

---

## Comment 4

> Username: Wavetrace  
> Created_at: 2024-11-14 11:20:58 UTC  
> Url: https://github.com/boostorg/graph/pull/397#issuecomment-2476091023  

For the record, this function was added in https://github.com/boostorg/graph/commit/86ef70716434ea1dabe2b6a25bb489e19793ebfe on `Feb 6, 2001` and not modified since then except for style.

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2024-11-15 01:51:40 UTC  
> Url: https://github.com/boostorg/graph/pull/397#issuecomment-2477790173  

> > I only have one question: how does someone with almost no activity history in GitHub know so much about Boost.Graph? :)  
>   
> A great question! Well, I read the book as soon at it was published ;) And Alexander Stepanov's recommendation meant a lot to me, I know him personally and appreciate his inventions a lot. I also ended up rolling up my own graph library before, because Boost was not welcome in my organization.  
  
Interesting. Did you also work with him? I'm also a great admirer of his work and managed to visit him once for a chat; unfortunately, Palo Alto is a long way from Australia.  
  
Anyway, my question was fairly flippant, so let me address an actual concern: maybe this function shouldn't exist at all?  
Although the meaning of "connected" is unambiguous for undirected graphs, it's not so for directed graphs, and although we would preserve the original meaning of strongly connected, it still leaves every new user to discover what is meant by "connected" in the directed case. Also, what happens when someone wants "connected" to mean weakly, or unilaterally, connected in the directed case? It sits uncomfortably with me. It perhaps has to be a parameter to `is_connected` so that the user can specify it at each call. What do you think of this situation? Let's resolve this design question before we move on to matters of code.  
  
As you pointed out, the function is undocumented and not used anywhere in the code base that I can see, so we are free to reinvent it.

---

## Comment 6

> Username: Wavetrace  
> Created_at: 2024-11-21 14:51:33 UTC  
> Url: https://github.com/boostorg/graph/pull/397#issuecomment-2491440475  

> Interesting. Did you also work with him? I'm also a great admirer of his work and managed to visit him once for a chat; unfortunately, Palo Alto is a long way from Australia.  
  
No, we also only met for a few days.  
  
> It perhaps has to be a parameter to is_connected so that the user can specify it at each call. What do you think of this situation? Let's resolve this design question before we move on to matters of code.  
  
Yes, I like this idea. Let me draft an implementation do discuss in greater details. I'd still consider it important to keep the interface backward-compatible. What do you think about it?    
  
Also is it possible to mark a function as deprecated in Boost? I only see the deprecated headers or comments for function deprecation.

---

## Comment 7

> Username: Wavetrace  
> Created_at: 2024-11-21 15:04:33 UTC  
> Url: https://github.com/boostorg/graph/pull/397#issuecomment-2491476529  

And one more question: I see `meta/libraries.json` specifies `"cxxstd": "14"`. Does this mean we are free to use all the C++14 features? I see most code is C++98, but maybe it was just not updated yet?

---

## Comment 8

> Username: Wavetrace  
> Created_at: 2024-12-11 23:06:35 UTC  
> Url: https://github.com/boostorg/graph/pull/397#issuecomment-2537384129  

Hi  
  
I updated this FR with your suggestions, please have a look. One can now call the algorithm like this:  
  
```  
bool b = is_connected(g, is_connected_kind::strong);  
bool b = is_connected(g, is_connected_kind::unilateral);  
bool b = is_connected(g, is_connected_kind::weak);  
```  
  
which all returns the same for an undirected graph (this makes sense conceptually).  
  
Does this correspond to your direction of thought? If yes, I'll add some documentation.  
  
I think there are 3 questions at hand, I made some reasonable trade offs here, but would like to discuss this more. Probably it is good enough, but sure there is some place for improvement.  
  
1. Do we want to keep the interface 100% backward compatible? This is technically possible, though I understood from your previous comment, that you don't see this as important. I opted into breaking the interface, but producing a compile-time error with a reasonable message if one tries to use the old one.  
2. Algorithms now require that graph has a vertex index (there's a separate discussion of why it's not simply `VertexIndexGraphConcept`). It is also possible to accept the named params to get the vertex index, though this might be a bit of an overkill for now. Another alternative is to create associative output property maps instead of iterated ones, but this might be slow. Is there an easy way to pick an appropriate property map type depending on if vertex_index is provided by the graph? I see something relevant in `named_function_params.hpp`, though not exactly this.  
3. It might make sense to drop the default of "strongly connected" for directed graphs and require the mode / kind to be always specified explicitly.  
  
While working on this I spotted a few other things; I'll probably send separate PRs for them to keep this one focused on is_connected.  
  
I look forward to your comments and suggestions on the subject. Many thanks for looking into this!

---

## Comment 9

> Username: jeremy-murphy  
> Created_at: 2024-12-11 23:08:33 UTC  
> Url: https://github.com/boostorg/graph/pull/397#issuecomment-2537386576  

Apologies for the delay in replying, I'm in the midst of relocating to a different country.  
  
> > It perhaps has to be a parameter to is_connected so that the user can specify it at each call. What do you think of this situation? Let's resolve this design question before we move on to matters of code.  
>   
> Yes, I like this idea. Let me draft an implementation do discuss in greater details. I'd still consider it important to keep the interface backward-compatible. What do you think about it?  
  
I know backward-compatibility is often sought after or required, but I think it's given too much priority without consideration of the value provided. In this case, as I mentioned:  
  
1. the function is undocumented  
2. the interface is ambiguous  
  
so in my opinion, backward-compatibility is not a priority.  
  
I think it's better to draft an interface first before moving on to code, but I'm too late with that advice.  
How I imagine it working is, given `is_connected<X>(Y)`, where `X` is a type and `Y` is a concept:  
  
    X              Y              Result  
                   undirected     OK  
                   directed       invalid  
    weakly         directed       OK  
    unilaterally   directed       OK  
    strongly       directed       OK  
  
The relationship between the kinds of directed connectivity is that strongly ⇒ weakly and strongly ⇒ unilaterally, but nothing else (i.e. unilaterally does not ⇒ weakly nor vice versa). So I wouldn't use the struct/enum design you currently have, I would do  
  
```  
struct weakly {};  
struct unilaterally {};  
struct strongly : weakly, unilaterally {};  
```  
I believe that's how the graph concepts are done too, have a look around in `graph_traits.hpp` maybe.   
  
> Also is it possible to mark a function as deprecated in Boost? I only see the deprecated headers or comments for function deprecation.  
  
https://en.cppreference.com/w/cpp/language/attributes/deprecated

---

## Comment 10

> Username: jeremy-murphy  
> Created_at: 2024-12-11 23:12:21 UTC  
> Url: https://github.com/boostorg/graph/pull/397#issuecomment-2537390939  

Ah, I see we both wrote long comments at the same time; I didn't see yours until after I finished mine.

---

## Comment 11

> Username: jeremy-murphy  
> Created_at: 2024-12-11 23:16:23 UTC  
> Url: https://github.com/boostorg/graph/pull/397#issuecomment-2537396130  

Hmmm, my idea of using the template parameter is not essential, btw. Using the second parameter for a tag like you have done is probably preferable and I think follows existing conventions. One benefit of the tag dispatch is that `is_connected(g)` can work for an undirected graph.

---

## Comment 12

> Username: Wavetrace  
> Created_at: 2024-12-12 01:48:03 UTC  
> Url: https://github.com/boostorg/graph/pull/397#issuecomment-2537568941  

> I'm in the midst of relocating to a different country  
  
I wish you very smooth relocation, definitely not an easy thing to do ;)  
  
> How I imagine it working is, given is_connected<X>(Y)  
  
Yes, I ended up implementing this behaviour even before reading your comment! I agree, that explicit is better than implicit even when it requires a bit more typing. Please see the recent version.  
  
Thanks for commenting on the backward compatibility. I think a static assert with a reasonable message would be good enough for now. Also thanks for confirming the kind selection tags strategy, it's probably good enough for now.  
  
Overall I think this code is now in a reasonably good shape to be useful already.   
  
I will look into selecting the proper output property maps and picking associative ones when the vertex_index is not present in the graph. I think this would be more usable than providing an external index and still reasonably fast. This can also follow as a different PR.

---

## Comment 13

> Username: Wavetrace  
> Created_at: 2024-12-12 03:10:48 UTC  
> Url: https://github.com/boostorg/graph/pull/397#issuecomment-2537711271  

As of the other issues I mentioned, please see #403 and #404 (this is the reason some code is commented out in the test).

---

## Comment 14

> Username: jeremy-murphy  
> Created_at: 2025-02-25 10:04:29 UTC  
> Url: https://github.com/boostorg/graph/pull/397#issuecomment-2681419568  

Sorry, I've forgotten where we were up to on this one, so I will have to reacquaint myself with what is going on.

---

## Comment 15

> Username: Wavetrace  
> Created_at: 2025-02-27 15:42:12 UTC  
> Updated_at: 2025-02-27 15:45:20 UTC  
> Url: https://github.com/boostorg/graph/pull/397#issuecomment-2688347164  

Hi Jeremy,  
  
I think (after some consideration), that I'd need to update this solution  
to relax the requirement for the graph to be VertexIndexGraph by allowing  
the client to pass the vertex index via an additional argument to the  
algorithm.  
  
Let me upload this solution soon, please get back to this PR after that  
(let's say in a week).

---

## Comment 16

> Username: jeremy-murphy  
> Created_at: 2025-02-28 06:00:21 UTC  
> Url: https://github.com/boostorg/graph/pull/397#issuecomment-2689796750  

Sure, no rush!

---
