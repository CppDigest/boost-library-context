# #124 - The `m_eproperty` are not same in subgraph. [Open]

> Username: kenkangxgwe  
> Created at: 2018-10-18 06:45:29 UTC  
> Updated at: 2018-10-18 08:50:37 UTC  
> Url: https://github.com/boostorg/graph/issues/124  

Imagine we have a root graph g, and two sons of it g1 and g2.  
If we use `edges(g1)` to get two iterators and cycle from one to the other to find an edge with index `i`,  
we name its corresponding descriptor as `ed_1 = *e1_i`.  
We use the same method in `g`, find an edge with index `i`, and convert it to g1's local edge descriptor, we name it `ed_2 = g1.global_to_local(*e_i)`.  
And I found that their `m_eproperty` are not the same. Shouldn't these two be the same descriptor?

---

## Comment 1

> Username: anadon  
> Created at: 2018-10-18 07:25:51 UTC  
> Updated at: 2018-10-18 07:26:06 UTC  
> Url: https://github.com/boostorg/graph/issues/124#issuecomment-430903472  

> get two iterators and cycle from one to the other to find an edge with index i  
  
Please clarify  
  
> convert it to g1's local edge descriptor  
  
One reason why I want to replace BGL.  
  
So are you saying that the edge between two vertices in a subgraph is not the same edge as between those two vertices in the full graph?

---

## Comment 2

> Username: kenkangxgwe  
> Created at: 2018-10-18 07:30:09 UTC  
> Updated at: 2018-10-18 07:32:27 UTC  
> Url: https://github.com/boostorg/graph/issues/124#issuecomment-430904652  

> get two iterators and cycle from one to the other to find an edge with index i  
``` c++  
    typename subgraph_t::edge_iterator e1_i, e1_i_end;  
    typename subgraph_t::edge_descriptor ed1;  
    for(tie(e1_i, e1_i_end) = edges(g1); e1_i != e1_i_end; ++e1_i)  
    {  
        if(get(boost::edge_index, g1, /* index */ 1) {  
            ed1 = *e1_i;  
        }  
    }  
```  
  
Try this if you get a segmentation fault:  
``` c++  
template<typename subgraph_t>  
void global_local_convertion_test(subgraph_t &g)  
{  
    subgraph_t &r_g = g.root();  
    typename subgraph_t::edge_iterator er_i, er_i_end;  
    for(tie(er_i, er_i_end) = edges(r_g); er_i != er_i_end; ++er_i)  
    {  
        if(get(boost::edge_index, r_g, *er_i) == 1)  
        {  
            g[g.global_to_local(*er_i)];  
        }  
    }  
    typename subgraph_t::const_children_iterator gi, gi_end;  
    boost::tie(gi, gi_end) = g.children();  
    for(; gi != gi_end; ++gi)  
    {  
        global_local_convertion_test(*gi);  
    }  
}  
```

---

## Comment 3

> Username: kenkangxgwe  
> Created at: 2018-10-18 07:35:22 UTC  
> Updated at: 2018-10-18 07:35:29 UTC  
> Url: https://github.com/boostorg/graph/issues/124#issuecomment-430906067  

> So are you saying that the edge between two vertices in a subgraph is not the same edge as between those two vertices in the full graph?  
  
I tried to say the edge between two vertices in a subgraph is not the same edge as the edge between the same two vertices full graph **after I convert it from global to local**

---

## Comment 4

> Username: anadon  
> Created at: 2018-10-18 07:38:19 UTC  
> Url: https://github.com/boostorg/graph/issues/124#issuecomment-430906883  

That global to local might be to have alternate sets of edges in subgraphs.  I'll check it out.    
  
Please provide the output from valgrind running your test case in a gist.

---

## Comment 5

> Username: anadon  
> Created at: 2018-10-18 08:37:51 UTC  
> Url: https://github.com/boostorg/graph/issues/124#issuecomment-430924492  

```  
    edge_descriptor global_to_local(edge_descriptor e_global) const  
    { return is_root() ? e_global : (*m_local_edge.find(get(get(edge_index, root().m_graph), e_global))).second; }  
```  
  
It looks like subgraphs can have alternate sets of edges.  I disagree with this design choice and it is opaque, but it is already set.  
  
That moves focus to whether a subgraph's included edges at creation should be left as default, copied from the next level up, or it is obviously incorrect.  I'll hit this later.  I've been up since 3AM local time, past 4:30 now and I'm dead tired.

---

## Comment 6

> Username: kenkangxgwe  
> Created at: 2018-10-18 08:50:37 UTC  
> Url: https://github.com/boostorg/graph/issues/124#issuecomment-430928536  

There you go the log:  
https://gist.github.com/kenkangxgwe/af4ccb3cec3ae446dc5d69df2184caba  
  
BTW, it seems that we are in the same Time Zone. Good night, bro.
