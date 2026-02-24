# #91 Allow use of grid_graph without "using namespace boost" [Open]

> Username: murraycu  
> Created at: 2017-05-11 10:19:24 UTC  
> Updated at: 2018-11-08 10:21:15 UTC  
> Url: https://github.com/boostorg/graph/pull/91  

This is really about whether people should be able to use the BGL API without putting "using namespace boost" in their code. I think they should, and they can with most of the API, but not all - for instance, boost::grid_graph.  
  
Currently, removing "using namespace boost", and using the "boost::" prefix instead, causes the following compiler errors, because these functions are actually member methods of the grid_graph class, not functions in the boost namespace.  
  
Normally, when we call, for instance, just num_vertices(graph), instead of boost::num_vertices(graph), the compiler finds the member method via argument dependent lookup.  
  
```  
grid_graph_example.cpp:34:25: error: no member named 'num_vertices' in namespace 'boost'  
       v_index < boost::num_vertices(graph); ++v_index) {  
                 ~~~~~~~^  
grid_graph_example.cpp:38:53: error: no member named 'vertex' in namespace 'boost'  
      boost::get(boost::vertex_index, graph, boost::vertex(v_index, graph)) << std::endl;  
                                             ~~~~~~~^  
grid_graph_example.cpp:48:51: error: no member named 'edge_at' in namespace 'boost'  
      boost::get(boost::edge_index, graph, boost::edge_at(e_index, graph)) << std::endl;  
                                           ~~~~~~~^  
grid_graph_example.cpp:65:30: error: no member named 'vertex' in namespace 'boost'  
  auto first_vertex = boost::vertex(0, graph);  
```  
  
As you can see by these example commits, fixing this involves some tedious code outside of the class. Maybe you see a better way to do it. If this is the only way then I still think it's worth it.  
  
This would also be fixed by Bjarne Stroustrup's Unified Call proposal:  
https://isocpp.org/blog/2016/02/a-bit-of-background-for-the-unified-call-proposal  
but that doesn't seem likely to happen any time soon.

---

## Comment 1

> Username: anadon  
> Created_at: 2018-08-31 17:58:07 UTC  
> Url: https://github.com/boostorg/graph/pull/91#issuecomment-417743909  

I'm helping out with the PR backlog. Looks like you have a feature addition, no test, and no new examples will be needed.  Users can just use 'use' semantics.  I'd like to have a test, but also see appropriate changes in the examples and documentation.  The change you are proposing is actually larger.  This is to let you know and help me prioritize PR's.

---

## Comment 2

> Username: anadon  
> Created_at: 2018-10-15 19:59:07 UTC  
> Url: https://github.com/boostorg/graph/pull/91#issuecomment-429992038  

@murraycu Can you rebase your PR on devel?

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2018-10-20 02:15:12 UTC  
> Url: https://github.com/boostorg/graph/pull/91#issuecomment-431540826  

I don't quite get what this PR is trying to achieve, since `friend` functions are found via ADL in the same way that functions in the enclosing namespace of a class are.  
So yes, `get(foo)` and `num_vertices(foo)` should work without `using namespace boost;` when `foo` is a graph class from inside `namespace boost {}`.  
The use of `using namespace boost;` in the examples is presumably just a convenience to bring the graph and graph trait classes into scope. I don't think it is egregious usage but it may be more reassuring that the ADL works as expected if we replace it with the specific `using boost::grid_graph;`, etc or just qualify `boost::grid_graph`, etc.

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2018-10-26 09:01:38 UTC  
> Url: https://github.com/boostorg/graph/pull/91#issuecomment-433339462  

For example, with `grid_graph_test.cpp`, if you replace `using namespace boost;` with  
```C++  
using boost::minstd_rand;  
using boost::grid_graph;  
using boost::graph_traits;  
using boost::lexical_cast;  
```  
then the ADL works to find `vertices`, etc.  
  
@jzmaddock I'm pretty sure, unless I've missed something, that this PR is based on a misunderstanding and thus not actually achieving what it sets out to.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2018-10-26 17:40:03 UTC  
> Url: https://github.com/boostorg/graph/pull/91#issuecomment-433487370  

Can the OP post a use case that doesn't work at present?

---

## Comment 6

> Username: jeremy-murphy  
> Created_at: 2018-10-27 04:15:01 UTC  
> Url: https://github.com/boostorg/graph/pull/91#issuecomment-433589090  

The OP seems to be MIA.

---

## Comment 7

> Username: murraycu  
> Created_at: 2018-11-01 08:37:51 UTC  
> Url: https://github.com/boostorg/graph/pull/91#issuecomment-434971394  

[snip]  
> using boost::minstd_rand;  
[snip]  
  
I'd like the API to be usable without any "using", so without either "using boost" or "using boost::somethingelse".

---

## Comment 8

> Username: anadon  
> Created_at: 2018-11-01 14:10:13 UTC  
> Url: https://github.com/boostorg/graph/pull/91#issuecomment-435052073  

@murraycu That would mean putting boost into the global namespace.  Not even the standard library does that and it is far for common to use than boost.  That isn't a reason for such a change.

---

## Comment 9

> Username: jeremy-murphy  
> Created_at: 2018-11-05 00:27:52 UTC  
> Url: https://github.com/boostorg/graph/pull/91#issuecomment-435723393  

> @murraycu That would mean putting boost into the global namespace. Not even the standard library does that and it is far for common to use than boost. That isn't a reason for such a change.  
  
I don't think that was the intended meaning since such a change would be radical and disproportionate.  
I think what was meant is that, given an object of a class from Boost.Graph (which is in the `boost` namespace), one should be able to interact with it without having to make explicit `using` declarations of either namespaces or functions.  
  
That _should_ work via ADL. And the change made in this PR does not appear to have any bearing on ADL, since the [look-up rules](https://en.cppreference.com/w/cpp/language/adl) for `friend` functions are the same as for functions declared in the same namespace. But you cannot qualify a friend function with the enclosing namespace, that's an error! They can be found via ADL but not namespace qualification.  
I tested for myself by removing `using namespace boost;` and ADL finds `num_vertices` just fine, which is the example given in the reason for opening the PR.  
  
As for `boost::minstd_rand`, it's not part of Boost.Graph, it doesn't take a graph parameter, so you can't expect it to be found via ADL. Some people might want to use `std::minstd_rand` instead.  
  
Murray, does that make sense? I might be wrong and I can't actually experiment with the code today.

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2018-11-05 18:43:29 UTC  
> Url: https://github.com/boostorg/graph/pull/91#issuecomment-435987513  

Actually looking at this, I believe the PR is valid - though whether it matters is another thing.  
  
The OP is correct that these non-members cannot be called via `boost::get(whatever)` for example.  Also the changes indicate that some of these accessor functions don't actually need to be friends in order to be implemented!  
  
But.... the documentation is clear that an unqualified `get(whatever)` is the supported syntax.  And this does work via ADL even without a `using namespace boost;`, for example in the example cited (grid_graph_example.cpp) I changed:  
  
```  
using namespace boost;  
```  
  
to  
  
```  
using boost::grid_graph;  
using boost::graph_traits;  
```  
  
and then everything built just fine.  
  
So... IMO this is low priority, but harmless enough.  I would want to remove the explicit boost:: prefixes added to the examples/tests though as they're not required - and we should test that they're not required as that's the documented syntax.

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2018-11-05 19:00:26 UTC  
> Url: https://github.com/boostorg/graph/pull/91#issuecomment-435993963  

A couple of other points:  
  
* Some of the internals of these classes were made public in in this PR: IMO that's not required - the functions can still be friends, just not defined class-inline.  IMO that should be fixed before accepting this.  
* The only conceivable valid use case I can think of, is where an unqualified call leads to ambiguities with some other function of the same name.  Currently it's not possible to make the calls qualified to resolve such an issue.  
* Someone should check whether there are other functions with the same issue - if we do this then we should at least be consistent.

---

## Comment 12

> Username: jeremy-murphy  
> Created_at: 2018-11-08 10:18:53 UTC  
> Updated_at: 2018-11-08 10:21:15 UTC  
> Url: https://github.com/boostorg/graph/pull/91#issuecomment-436944605  

OK, I can agree that removing friend access from functions that don't require it is an improvement, such as the two overloads of `get` at the start that just index a map with a key, and maybe two more `get` functions later.  
  
But what is the point of un-inlining the definition of friend functions that are going to remain friends?

---
