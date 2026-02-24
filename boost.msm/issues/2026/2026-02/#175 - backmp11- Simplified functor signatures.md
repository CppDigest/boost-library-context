# #175 - backmp11: Simplified functor signatures [Closed]

> Username: chandryan  
> Created at: 2026-02-09 20:38:01 UTC  
> Updated at: 2026-02-23 20:08:36 UTC  
> Closed at: 2026-02-23 20:08:36 UTC  
> Url: https://github.com/boostorg/msm/issues/175  

The Functor API in MSM supports exactly one syntax that requires 4 template parameters for Functor operators:  
  
```cpp  
struct MyAction  
{  
    template <typename Event, typename Fsm, typename Source, typename Target>  
    void operator()(const Event&, Fsm&, Source&, Target&)  
    {  
    }  
};  
```  
  
By applying deeper introspection into the Functor, we can support additional API variants. For example a simpler variant that only takes the Fsm:  
  
```cpp  
struct SimpleAction  
{  
    template <typename Fsm>  
    void operator()(Fsm&)  
    {  
    }  
};  
```  
  
A [PoC looks promising](https://github.com/chandryan/msm/tree/feat/backmp11-functor-apis), though the benchmark shows a slight increase in memory consumption and compile time.

---

## Comment 1

> Username: chandryan  
> Created at: 2026-02-19 18:35:39 UTC  
> Updated at: 2026-02-19 21:20:06 UTC  
> Url: https://github.com/boostorg/msm/issues/175#issuecomment-3929125723  

With SFINAE and tag dispatch the overhead is acceptable.  
  
These 3 signatures seem to be the most useful:  
  
- `Event, Fsm, Source, Target` - Full signature as already existing  
- `Event, Fsm`  - as we often don't need to access source and target state  
- `Fsm` - for accessing the Fsm's member variables, its context, triggering events, ...  
  
Other candidates would be:  
  
- `Event` - maybe useful for guards but not for actions, introducing different signature variants for each is difficult to follow. As the signature is identified by the no. of its arguments, this candidate loses against the `Fsm` variant  
- `<no arguments>` - No access to anything related to the state machine, this signature does not seem very useful  
  
  
Additionally we gain support for lambdas as functors - backwards-compatible, but requires C++20.
