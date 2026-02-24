# #167 - labeled_graph::remove_vertex does not remove label [Open]

> Username: arneboe  
> Created at: 2019-04-16 09:33:31 UTC  
> Updated at: 2025-07-03 12:55:31 UTC  
> Url: https://github.com/boostorg/graph/issues/167  

When calling `remove_vertex` on a `labeled_graph` the vertex is removed but the label is not.  
A dangling reference to the vertex is left behind. Accessing it causes segfaults.  
  
Minimal example:  
  
  
```  
#include <iostream>  
#include <string>  
  
#include <boost/graph/directed_graph.hpp>  
#include <boost/graph/labeled_graph.hpp>  
  
using namespace boost;  
using namespace std;  
  
int main() {  
  
    using namespace boost::graph_detail;  
    typedef directed_graph<> Digraph;  
    typedef labeled_graph<Digraph, string> Graph;  
      
    Graph g;  
    g.add_vertex("foo");  
      
    auto v = g.vertex("foo");  
    if(v != nullptr)  
        cout << "foo exists\n";  
      
    g.remove_vertex("foo");  
      
    auto v2 = g.vertex("foo");  
    if(v2 != nullptr)  
        std::cout << " BUG! vertex label still exists.\n";  
  
  
    return 0;  
}  
```

---

## Comment 1

> Username: anadon  
> Created at: 2019-04-17 04:27:19 UTC  
> Url: https://github.com/boostorg/graph/issues/167#issuecomment-483932260  

@jzmaddock Hey, this looks like a good one for me to work on.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-04-17 16:17:50 UTC  
> Url: https://github.com/boostorg/graph/issues/167#issuecomment-484159534  

Go for it!

---

## Comment 3

> Username: anadon  
> Created at: 2019-04-17 19:09:18 UTC  
> Updated at: 2019-04-17 19:10:07 UTC  
> Url: https://github.com/boostorg/graph/issues/167#issuecomment-484222385  

@arneboe Can I get your full name and email, since what you submitted is what I'm basing the regression test on?  I need to credit you.

---

## Comment 4

> Username: arneboe  
> Created at: 2019-04-18 07:26:56 UTC  
> Url: https://github.com/boostorg/graph/issues/167#issuecomment-484387731  

I dont care about the credit, but if you have to: Arne Böckmann

---

## Comment 5

> Username: anadon  
> Created at: 2019-04-18 18:48:06 UTC  
> Url: https://github.com/boostorg/graph/issues/167#issuecomment-484637620  

It looks like the intended design, given the state of BGL, is to have `_map` a protected member, not a private member.  So you hack was less of a hack than was expected.

---

## Comment 6

> Username: anadon  
> Created at: 2019-04-18 19:00:36 UTC  
> Url: https://github.com/boostorg/graph/issues/167#issuecomment-484643087  

This may take longer than expected: https://github.com/boostorg/graph/blob/develop/include/boost/graph/adjacency_matrix.hpp#L992

---

## Comment 7

> Username: anadon  
> Created at: 2019-04-18 19:06:09 UTC  
> Url: https://github.com/boostorg/graph/issues/167#issuecomment-484645546  

May be related: https://svn.boost.org/trac10/ticket/7863

---

## Comment 8

> Username: anadon  
> Created at: 2019-04-24 17:53:57 UTC  
> Url: https://github.com/boostorg/graph/issues/167#issuecomment-486359041  

This is going to be delayed somewhat.  There are general cleanups in testing which I need to do first.

---

## Comment 9

> Username: HWiese1980  
> Created at: 2019-04-24 18:57:59 UTC  
> Url: https://github.com/boostorg/graph/issues/167#issuecomment-486382454  

Sure, take your time! Better have it fixed correctly and thoroughly, than to rush it and only come up with some quick'n'dirty patch that fails somewhere else.

---

## Comment 10

> Username: anadon  
> Created at: 2019-07-11 00:04:11 UTC  
> Url: https://github.com/boostorg/graph/issues/167#issuecomment-510274799  

Just an update, had to move twice, once across states, and started a new job.  Getting back to this more now.

---

## Comment 11

> Username: HWiese1980  
> Created at: 2019-07-24 05:06:56 UTC  
> Url: https://github.com/boostorg/graph/issues/167#issuecomment-514481201  

Alright, no pressure. Just don't forget it ;-)

---

## Comment 12

> Username: fjch1997  
> Created at: 2022-07-29 18:00:41 UTC  
> Url: https://github.com/boostorg/graph/issues/167#issuecomment-1199808963  

And... it was forgotten  
This bug really tripped me up when I tried to use labeled_graph

---

## Comment 13

> Username: HWiese1980  
> Created at: 2022-07-30 05:16:26 UTC  
> Url: https://github.com/boostorg/graph/issues/167#issuecomment-1200092196  

As it seems... @anadon how's it going?

---

## Comment 14

> Username: jeremy-murphy  
> Created at: 2022-08-01 00:00:28 UTC  
> Url: https://github.com/boostorg/graph/issues/167#issuecomment-1200532072  

Not sure if they are still active on Boost.Graph, but if someone submits a pull request to fix this issue, I can help them get it polished and merge it.

---

## Comment 15

> Username: EduardoGoulart1  
> Created at: 2023-06-29 09:47:19 UTC  
> Updated at: 2023-06-29 09:47:43 UTC  
> Url: https://github.com/boostorg/graph/issues/167#issuecomment-1612729415  

Hey there, any update on it? @anadon by any chance do you still have your WIP PR that people can pick up from?

---

## Comment 16

> Username: DeuceBox  
> Created at: 2023-06-30 14:59:24 UTC  
> Url: https://github.com/boostorg/graph/issues/167#issuecomment-1614780408  

This may be related:  
  
There was a patch at https://svn.boost.org/trac/boost/ticket/9493 (I did not create it) which is no longer reachable, but I've been using it in production for years  
  
I've preserved it here:  
https://github.com/DeuceBox/graph/commit/de651cb06cd92d59effed88ae0fc79852c43ff34  
  
It appears to fix the bug in the minimal example given in the OP.

---

## Comment 17

> Username: StahlTim  
> Created at: 2023-11-17 15:02:57 UTC  
> Url: https://github.com/boostorg/graph/issues/167#issuecomment-1816585847  

@DeuceBox @jeremy-murphy   
Is there currently an active pull request introducing the fix referenced by @DeuceBox?  
  
Facing the same issue and would appreciate the fix being shipped with the stock boost library.

---

## Comment 18

> Username: DeuceBox  
> Created at: 2023-11-17 17:58:04 UTC  
> Url: https://github.com/boostorg/graph/issues/167#issuecomment-1816858730  

@StahlTim @jeremy-murphy since there wasn't any real discussion beyond the branch/fix I linked a while back, I've put in a PR.

---

## Comment 19

> Username: jeremy-murphy  
> Created at: 2023-11-18 09:03:30 UTC  
> Url: https://github.com/boostorg/graph/issues/167#issuecomment-1817450590  

I'll prioritise merging in the fix asap.

---

## Comment 20

> Username: jeremy-murphy  
> Created at: 2023-11-28 00:51:18 UTC  
> Url: https://github.com/boostorg/graph/issues/167#issuecomment-1828886361  

Could someone please check out the fix I just merged and test it? Thanks to @DeuceBox for making the PR.

---

## Comment 21

> Username: EduardoGoulart1  
> Created at: 2024-07-26 13:29:41 UTC  
> Url: https://github.com/boostorg/graph/issues/167#issuecomment-2252770422  

Hi @jeremy-murphy, has this fix already been merged somewhere? I can't find it in the latest release

---

## Comment 22

> Username: DeuceBox  
> Created at: 2024-07-26 15:15:08 UTC  
> Url: https://github.com/boostorg/graph/issues/167#issuecomment-2252977456  

@EduardoGoulart1 It's merged here https://github.com/boostorg/graph/pull/354 in the "develop" branch. The boostorg/graph "develop" branch is what the boostorg/boost "master" branch references. However, the latest boostorg/boost release branch/tag (boost-1.85.0) references the corresponding release tag in boostorg/graph (https://github.com/boostorg/graph/releases/tag/boost-1.85.0), but it appears based on https://github.com/boostorg/graph/tags that the boostorg/graph repo's release tags haven't pointed to anything new since 2022.  
  
@jeremy-murphy could we get the next boostorg/graph release tag to reference a more recent version of develop? I don't know what's involved in that process.

---

## Comment 23

> Username: jeremy-murphy  
> Created at: 2024-07-27 05:31:01 UTC  
> Updated at: 2024-07-27 05:31:22 UTC  
> Url: https://github.com/boostorg/graph/issues/167#issuecomment-2253782547  

Thanks for pointing it out. That's about how long I've been maintainer for and I'm still learning what needs to be done! I assumed all that release tag business would be automatic from the super project.

---

## Comment 24

> Username: IamHuskar  
> Created at: 2025-07-03 12:55:31 UTC  
> Url: https://github.com/boostorg/graph/issues/167#issuecomment-3032167595  

> Thanks for pointing it out. That's about how long I've been maintainer for and I'm still learning what needs to be done! I assumed all that release tag business would be automatic from the super project.  
  
still have some bugs with graph vertex type boost:vecS  
when remove a vertex in _graph . the index in _graph has been changed after remove . but _map still hold the old value
