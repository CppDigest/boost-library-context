# #142 - boost-1.81 and to_string issue [Closed]

> Username: Pro-pra  
> Created at: 2023-02-04 03:29:35 UTC  
> Updated at: 2023-02-06 02:46:30 UTC  
> Closed at: 2023-02-06 02:46:30 UTC  
> Url: https://github.com/boostorg/core/issues/142  

Hi all, i compile ceph with boost-1.81 and see this error:  
```  
/builddir/build/BUILD/ceph-17.2.5/src/rgw/rgw_asio_client.cc:58:39: error: 'const class boost::core::basic_string_view<char>' has no member named 'to_string'  
   58 |       env.set("CONTENT_LENGTH", value.to_string());  
```  
  
some headers may be missing?

---

## Comment 1

> Username: pdimov  
> Created at: 2023-02-04 04:47:08 UTC  
> Url: https://github.com/boostorg/core/issues/142#issuecomment-1416660358  

@vinniefalco @klemens-morgenstern What's the right thing to do here? `core::string_view` doesn't have `to_string`, because `std::string_view` doesn't have it. (`value` above comes from Beast: https://github.com/ceph/ceph/blob/94699750e2153341ae55dd492525e2ed5d483974/src/rgw/rgw_asio_client.cc#L42).

---

## Comment 2

> Username: pdimov  
> Created at: 2023-02-04 04:53:14 UTC  
> Url: https://github.com/boostorg/core/issues/142#issuecomment-1416661263  

@Pro-pra you might want to open an issue in https://github.com/ceph/ceph as well. Since it looks like `env.set` takes `std::string`:  
  
https://github.com/ceph/ceph/blob/94699750e2153341ae55dd492525e2ed5d483974/src/rgw/rgw_common.h#L443  
  
the fix would be as simple as removing `.to_string()`, but if `ceph` supports Boost versions older than 1.81, this might need to be `#ifdef`-ed on `BOOST_VERSION`.

---

## Comment 3

> Username: Pro-pra  
> Created at: 2023-02-04 09:50:53 UTC  
> Updated at: 2023-02-04 10:00:59 UTC  
> Url: https://github.com/boostorg/core/issues/142#issuecomment-1416710895  

thank you!  
  
I see in ceph https://github.com/ceph/ceph/pull/18866/files may be need unroll this patch?

---

## Comment 4

> Username: pdimov  
> Created at: 2023-02-04 10:50:08 UTC  
> Url: https://github.com/boostorg/core/issues/142#issuecomment-1416720678  

A part of it, yes.
