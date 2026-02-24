# #83 PoC: Boost(-ing) MSM with Mp11 🚀  [Closed]

> Username: chandryan  
> Created at: 2025-02-16 17:54:30 UTC  
> Updated at: 2025-10-06 18:16:14 UTC  
> Closed at: 2025-08-03 16:04:39 UTC  
> Url: https://github.com/boostorg/msm/pull/83  

PoC for optimizing Boost MSM with Mp11, more info is available in #82

---

## Comment 1

> Username: chandryan  
> Created_at: 2025-04-15 13:56:06 UTC  
> Url: https://github.com/boostorg/msm/pull/83#issuecomment-2805148289  

The state of this PR's source branch is now outdated. A first mature version of the new backend is available in https://github.com/chandryan/msm-ng/tree/feat/poc-mp11-dispatch-table-per-state.  
  
Please tell me how you prefer to proceed @henry-ch, shall I create another PR with the first version of `backmp11`?

---

## Comment 2

> Username: henry-ch  
> Created_at: 2025-06-26 14:19:42 UTC  
> Url: https://github.com/boostorg/msm/pull/83#issuecomment-3008669740  

We already started testing it for real FSMs. It looks quite good, found one bug so far:  
static const init_cell_value<Cell>* const get_init_cells_impl(mp11::index_sequence<I...>)  
{  
    // replace  
    //static constexpr init_cell_value<Cell> values[] {mp11::mp_at_c<InitCellConstants, I>::value...};  
    //return values;  
    // with:  
    if constexpr (sizeof...(I) == 0) {  
        return nullptr;  
    } else {  
        static constexpr init_cell_value<Cell> values[] {mp11::mp_at_c<InitCellConstants, I>::value...};  
        return values;  
    }  
}

---

## Comment 3

> Username: henry-ch  
> Created_at: 2025-06-26 14:24:12 UTC  
> Url: https://github.com/boostorg/msm/pull/83#issuecomment-3008684522  

I suggest, I review and test your repo with quite a few FSMs we are currently developing, then you can send a new PR with the latest state.

---

## Comment 4

> Username: chandryan  
> Created_at: 2025-06-26 17:43:13 UTC  
> Url: https://github.com/boostorg/msm/pull/83#issuecomment-3009289202  

> I suggest, I review and test your repo with quite a few FSMs we are currently developing, then you can send a new PR with the latest state.  
  
Sounds great! :)  
The bug you mentioned in ``get_init_cells`` should already be fixed in the [develop branch of my fork](https://github.com/chandryan/msm-ng/tree/develop), along with another fix that was required for compiling with clang. IIRC these should be the only 2 bugs, most other changes were related to resolving more pedantic compiler warnings.

---

## Comment 5

> Username: chandryan  
> Created_at: 2025-08-03 16:04:39 UTC  
> Url: https://github.com/boostorg/msm/pull/83#issuecomment-3148516231  

In the meantime I have spent more time into maturing the code and making it ready for integration into Boost 😃   
  
Closing this PR in favor of https://github.com/boostorg/msm/pull/96. I will keep the branch used there up to date with the latest state.

---
