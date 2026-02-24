# #42 - Documentation does not describe treap priority_of_value changes [Closed]

> Username: jm-mikkelsen  
> Created at: 2019-04-03 07:08:28 UTC  
> Updated at: 2019-06-08 23:02:19 UTC  
> Closed at: 2019-06-08 23:01:56 UTC  
> Url: https://github.com/boostorg/intrusive/issues/42  

The documentation page treap_set_multiset.html does not describe the addition of the `priority_of_value` option on `treap` and has the default for `priority` as `priority< priority_compare<key_type> >` instead of `priority< priority_compare<priority_type> >`

---

## Comment 1

> Username: igaztanaga  
> Created at: 2019-06-08 23:02:18 UTC  
> Url: https://github.com/boostorg/intrusive/issues/42#issuecomment-500171743  

Many thanks for the report!
