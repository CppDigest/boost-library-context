# #1101 [buffer] add spheroid to buffer strategies as an optional constructor parameter [Merged]

> Username: barendgehrels  
> Created at: 2022-12-14 12:03:38 UTC  
> Updated at: 2022-12-27 11:58:02 UTC  
> Merged at: 2022-12-27 11:57:57 UTC  
> Closed at: 2022-12-27 11:57:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1101  



---

## Comment 1

> Username: barendgehrels  
> Created_at: 2022-12-14 15:00:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1101#issuecomment-1351570559  

This is (I think) the last implementation part of supporting geographic in buffers.  
There is still an (at least one) issue left: #1057

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2022-12-14 15:01:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1101#issuecomment-1351571605  

All checks fail because of  
```  
W: GPG error: http://dk.archive.ubuntu.com/ubuntu xenial InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 40976EAF437D05B5 NO_PUBKEY 3B4FE6ACC0B21F32  
```  
Is that at my side (might be) or is there something changed in the CI? @awulkiew @mloskot ?

---

## Comment 3

> Username: vissarion  
> Created_at: 2022-12-14 16:16:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1101#issuecomment-1351713651  

> All checks fail because of  
>   
> ```  
> W: GPG error: http://dk.archive.ubuntu.com/ubuntu xenial InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 40976EAF437D05B5 NO_PUBKEY 3B4FE6ACC0B21F32  
> ```  
>   
> Is that at my side (might be) or is there something changed in the CI? @awulkiew @mloskot ?  
  
I think this is general (for `master` too). See https://github.com/boostorg/geometry/pull/1102

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2022-12-14 16:17:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/1101#issuecomment-1351715279  

> > All checks fail because of  
> > ```  
> > W: GPG error: http://dk.archive.ubuntu.com/ubuntu xenial InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 40976EAF437D05B5 NO_PUBKEY 3B4FE6ACC0B21F32  
> > ```  
> >   
> >   
> >       
> >         
> >       
> >   
> >         
> >       
> >   
> >       
> >     
> > Is that at my side (might be) or is there something changed in the CI? @awulkiew @mloskot ?  
>   
> I think this is general (for `master` too). See #1102  
  
Thanks!

---

## Review 5 [Approved]

> Username: vissarion  
> Created_at: 2022-12-15 13:44:00 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1101#pullrequestreview-1219236215  

Thanks!

📁 test/algorithms/buffer/buffer_geo_spheroid.cpp

```diff
  19 |+ template <typename Formula, bool Clockwise, typename Point, typename Spheroid>
  20 |+ void test_linestring(std::string const& label, Spheroid const& spheroid,
  21 |+     double expected_area_round, double expected_area_miter)
```

> Username: vissarion  
> Created_at: 2022-12-15 13:42:27 UTC  
> Updated_at: 2022-12-15 13:44:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1101#discussion_r1049654147  

should this be aligned under `(`?

> Username: barendgehrels  
> Created_at: 2022-12-27 09:30:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/1101#discussion_r1057556631  

:heavy_check_mark:


---
