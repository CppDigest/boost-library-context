# #72 - Support B+ tree [Closed]

> Username: adolfogc  
> Created at: 2022-01-01 01:46:24 UTC  
> Updated at: 2022-07-16 12:02:26 UTC  
> Closed at: 2022-07-16 12:02:26 UTC  
> Url: https://github.com/boostorg/intrusive/issues/72  

The implementation from the tlx library, provided under the Boost license, could be adapted to align with Boost.Intrusive.  
  
See: https://github.com/tlx/tlx/blob/1b2059e19f3605c854f2a7450fa102e3e40dc10e/tlx/container/btree.hpp#L104

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-07-16 12:02:26 UTC  
> Url: https://github.com/boostorg/intrusive/issues/72#issuecomment-1186166430  

Thanks for the suggestion, but it should be targeted to another Boost library, as a B+tree can't be buit as an intrusive container (in B+ trees keys are stored in internal nodes and values in leaves), so I don't think Boost.Intrusive is the correct place for such a container.
