# #356 - astar_search does unnecessary initialization of vertices [Open]

> Username: jeremy-murphy  
> Created at: 2023-12-03 22:24:31 UTC  
> Updated at: 2024-12-10 23:19:22 UTC  
> Url: https://github.com/boostorg/graph/issues/356  

From [Keith Bennet](https://github.com/boostorg/graph/wiki/Feedback-submissions-from-the-Boost-User-mailing-list#keith-bennett):  
> astar_search() takes too much time to initialize all vertices (in a 2048x2048x40 matrix) even though we only need to initialize vertices when they are added to the open set. We ensure our data's initialization state is identical to a default-constructed object, then guarantee that the vertex initialization is done when the vertex is first added to the open set, and of course A* ensures that vertices aren't used until they're added to the open set (eg, when they're first discovered). To make resizing containers quicker, we ensure that our default-constructed objects are trivially-constructible so that resizing the underlying containers to match the matrix size does not require invoking expensive constructors which effectively reduces the initialization requirements to just resizing the containers correctly and setting the non-zero start-point state and end-point state.

---

## Comment 1

> Username: andrea-cassioli-maersk  
> Created at: 2024-12-10 08:06:26 UTC  
> Url: https://github.com/boostorg/graph/issues/356#issuecomment-2530732746  

Hey, I use Astar quite a lot and I wonder if this is still a thing.

---

## Comment 2

> Username: jeremy-murphy  
> Created at: 2024-12-10 23:19:21 UTC  
> Url: https://github.com/boostorg/graph/issues/356#issuecomment-2533199405  

Needs someone to give it some love.
