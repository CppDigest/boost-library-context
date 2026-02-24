# #143 - backmp11: Provide UML-compliant event deferring [Closed]

> Username: chandryan  
> Created at: 2025-11-04 19:22:07 UTC  
> Updated at: 2025-12-19 14:15:34 UTC  
> Closed at: 2025-12-19 14:15:34 UTC  
> Url: https://github.com/boostorg/msm/issues/143  

### Handling of deferred events according to UML  
  
According to [UML, p. 309](https://www.omg.org/spec/UML/2.5.1/PDF), events get and remain deferred until the following conditions are met:  
  
> - a state configuration is reached where these Event types are no longer deferred or,  
> - if a deferred Event type is used explicitly in a Trigger of a Transition whose source is the deferring State (i.e., a  
kind of override option).  
  
This "state configuration" refers to all currently active states, with no differentiation whether the active states come from different orthogonal regions. This means that for orthogonal regions an event is *either* deferred *or* consumed, but not both.  
The test case [Deferred 004-A in the PSSM document, p. 207](https://www.omg.org/spec/PSSM/1.0/PDF) demonstrates this behavior.  
  
*Note:* [Wikipedia claims a different behavior](https://en.wikipedia.org/wiki/UML_state_machine)  
  
> In the case of orthogonal regions where one orthogonal region defers an event and another consumes the event, the consumer takes precedence and the event is consumed and not deferred.  
  
However, this statement contradicts the PSSM test case. Wikipedia seems to have taken this information from a non-compliant implementation in the [spring-statemachine](https://docs.spring.io/spring-statemachine/docs/1.1.x/reference/html/sm-deferevents.html#sm-deferevents).  
  
  
### Current implementation  
  
All back-ends in MSM currently implement handling of deferred events in orthogonal regions in the same, non-compliant way. Roughly like this:  
  
- Deferred events are kept in a dedicated deferred events queue  
- The event is dispatched to all regions independently, making it possible to defer *and* consume the event at the same time  
- When deferred events are processed, all of them are popped from the queue and MSM tries to process them  
  - If an event is deferred again, it gets pushed back to the queue  
  - If an event is processed in at least one region, a sorting algorithm tries to first sort the events to retain their original order in the queue and then processes the deferred events again from the beginning of the queue  
  
**Potential pitfalls:**  
  
There is an [open issue](https://github.com/boostorg/msm/issues/72) describing a situation, in which the sorting algorithm leads to an infinite recursion. To my current knowledge it's impossible to provide a bugfix that could fully address this issue, because it's not possible to distinguish between newly queued and old, re-queued events for the sorting algorithm. But the re-queued events would need to be skipped in order to prevent the infinite recursion.  
  
Also consuming as well as deferring the event at the same time leads to other potential pitfalls:  
When the orthogonal region that defers the event switches to a state that does not defer it anymore, the region that did not defer the event will consume the same event twice.  
  
Other side effects would come into place, if there was a situation where both regions defer the event; its occurrences would increase with every attempt to process the deferred events queue.  
  
  
### Target implementation  
  
#### backmp11  
  
For backmp11 we can change the deferred event handling logic to be UML-compliant by evaluating the deferral prior to dispatching the event to all orthogonal regions. This requires changes in the event dispatch mechanism, moving the event deferral out of the dispatch table. This implementation should be manageable quite well with the help of the universal visitor API.  
  
This should resolve the currently existing issues and probably even make the sorting algorithm obsolete, as events which remain deferred can stay in the queue.  
  
I'm not sure how to handle the part "if a deferred Event type is used explicitly in a Trigger of a Transition whose source is the deferring State (i.e., a  
kind of override option).". This could be handled with some additional method that just reports whether the event *could* be processed, but worst case this leads to the need to instantiate a second dispatch table for the same - resulting in a significant impact in compilation time. We can probably live with a limitation stating that such an "override option" is not supported.  
  
#### back & back11  
  
These back-ends do not have the universal visitor API, thus changing the implementation requires substantial efforts. And, while this change would be UML-compliant, it does lead to a non-negligible behavioral change, which can easily lead to breaking changes for users.  
  
Thus I believe it's better to leave back & back11 as-is.

---

## Comment 1

> Username: henry-ch  
> Created at: 2025-11-29 17:45:13 UTC  
> Url: https://github.com/boostorg/msm/issues/143#issuecomment-3591830257  

We can also live with the current implementation, which when used correctly is an useful feature.

---

## Comment 2

> Username: chandryan  
> Created at: 2025-12-19 14:15:34 UTC  
> Url: https://github.com/boostorg/msm/issues/143#issuecomment-3675236834  

Agree for back and back11 👍  For the backmp11 back-end deferral with the `deferred_events` property now behaves as described in UML, for conditional deferral we have #155 to follow up.  
Closing this issue.
