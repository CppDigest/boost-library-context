# #363 - Single-path RCSP does not return always the shortest path [Closed]

> Username: andrea-cassioli-maersk  
> Created at: 2024-01-30 08:19:31 UTC  
> Updated at: 2024-04-08 05:11:58 UTC  
> Closed at: 2024-04-08 05:11:58 UTC  
> Url: https://github.com/boostorg/graph/issues/363  

Hi,  
working on the RCSP algorithm, I have come to realise that when using the single shortest path interface for RCSP, it is not alway what is returned.  
  
Now before submitting a fix, I would like to understand whether this is a bug or my misunderstanding.  
  
Let me try to explain:  
  
* The RCSP does not explicitly deal with the distance that define which path is the shortest, but rather asks that when extending a label l1 to the next l2, then l1<=l2  
* The RCSP uses the `<operator` to process labels from the priority queue so that the "smallest" one is picked up at each iteration.   
* If l1<=l2 implies that the distance increases from l1 to l2, then   
    * at each iteration RCSP processes the label with the smallest distance so far  
    * when a label at the target is processed, any other label has at least the same distance, thus we have found one of the possible shortest path.  
  
All sounds good. However, when the algorithm terminates there is an additional step to reconstruct the solution. It iterates over the labels stored in a list at target vertex, and use the first one as the single best solution. But those labels are not sorted using the `<` but rather they are in order of extension. This mean that the post-processing can return another path than the one that was found by the algorithm itself!  
  
I have create a four node example that I think shows what happens, and it is attached.  
[rcsp-bug-submission.zip](https://github.com/boostorg/graph/files/14094723/rcsp-bug-submission.zip)  
  
To me it is, as the RCSP must return the shortest path (hence the SP in the name) if labels are first sorted by the distance first. Clearly, if one asks for all non-dominated solutions, then this is not an issue anymore.   
  
Is this a bug?

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2024-01-30 23:46:42 UTC  
> Url: https://github.com/boostorg/graph/issues/363#issuecomment-1918102704  

I don't know if this helps, but I just found this comment in the test file for this algorithm:  
```  
// DominanceFunction model  
class dominance_no_res_cont  
{  
public:  
    inline bool operator()(const spp_no_rc_res_cont& res_cont_1,  
        const spp_no_rc_res_cont& res_cont_2) const  
    {  
        // must be "<=" here!!!  
        // must NOT be "<"!!!  
        return res_cont_1.cost <= res_cont_2.cost;  
        // this is not a contradiction to the documentation  
        // the documentation says:  
        // "A label $l_1$ dominates a label $l_2$ if and only if both are  
        // resident at the same vertex, and if, for each resource, the resource  
        // consumption of $l_1$ is less than or equal to the resource  
        // consumption of $l_2$, and if there is at least one resource where  
        // $l_1$ has a lower resource consumption than $l_2$." one can think of  
        // a new label with a resource consumption equal to that of an old label  
        // as being dominated by that old label, because the new one will have a  
        // higher number and is created at a later point in time, so one can  
        // implicitly use the number or the creation time as a resource for  
        // tie-breaking  
    }  
};  
```  
Apologies, but I don't have time to delve into it.  
  
If you still think it's a bug, I suggest you open a PR to add your test to the existing file, because that's generally easier to review than an attached zip file.

---

## Comment 2

> Username: andrea-cassioli-maersk  
> Created at: 2024-01-31 07:49:49 UTC  
> Updated at: 2024-02-06 09:52:49 UTC  
> Url: https://github.com/boostorg/graph/issues/363#issuecomment-1918564254  

@jeremy-murphy indeed I use `<=` as mentioned in the comments. In my understanding dominance is not even strictly required (indeed if you just return always false, i.e. no label dominates any the problem still persists.)  
  
  
I have a fix that seems to work, hopefully will have a PR to share soon.

---

## Comment 3

> Username: andrea-cassioli-maersk  
> Created at: 2024-03-06 15:04:28 UTC  
> Url: https://github.com/boostorg/graph/issues/363#issuecomment-1981071766  

Hey, I am a bit lost on how the project is set up.  
  
* I have clone this repo, not the main boost  
* cmake cannot find Boost::algorithm   
  
I do have boost installed. How do you usually work on this code base?

---

## Comment 4

> Username: jeremy-murphy  
> Created at: 2024-03-10 23:54:05 UTC  
> Updated at: 2024-03-11 23:06:58 UTC  
> Url: https://github.com/boostorg/graph/issues/363#issuecomment-1987417737  

Ah, yeah, sadly, Boost is still a monolithic pain in the arse to work with. First step is that you have to clone the main repo with the `--recursive` option so that you get _everything_.   
  
Edit: Fixed spelling of "recursive", whoops.

---

## Comment 5

> Username: jeremy-murphy  
> Created at: 2024-03-10 23:58:09 UTC  
> Url: https://github.com/boostorg/graph/issues/363#issuecomment-1987418964  

Fortunately, Boost.Graph does not depend on any precompiled libraries, so you can skip the `b2` stuff completely for now.  
  
Open the `CMakeLists.txt` in the root of the Boost project, navigate to the Boost.Graph project and edit the CMakeLists.txt to add whichever test is useful to you.  
  
One day I'll do the whole (Graph) project so that no-one has to go through these steps again.

---

## Comment 6

> Username: andrea-cassioli-maersk  
> Created at: 2024-03-22 11:02:48 UTC  
> Url: https://github.com/boostorg/graph/issues/363#issuecomment-2014847103  

Here we go https://github.com/boostorg/graph/pull/368

---

## Comment 7

> Username: jeremy-murphy  
> Created at: 2024-04-08 05:11:58 UTC  
> Url: https://github.com/boostorg/graph/issues/363#issuecomment-2041876467  

Usually merging the review automatically closes the issue, not sure why it didn't in this case.
