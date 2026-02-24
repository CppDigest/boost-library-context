# #151 - kth_channel_view fails when used with virtual views (Trac 9020) [Open]

> Username: mloskot  
> Created at: 2018-10-08 09:58:22 UTC  
> Updated at: 2019-11-07 10:00:54 UTC  
> Url: https://github.com/boostorg/gil/issues/151  

Moved from https://svn.boost.org/trac10/ticket/9020 description:  
  
> Given an image_view with a `virtual_2d_locator` that returns a `planar_pixel_reference`, `kth_channel_view` produces a `kth_channel_deref_fn` with an incorrect `result_type` which may result an invalid pointer dereference.  
>  
> For example, consider a `virtual_2d_locator` instantiated with a dereference functor that returns a `planar_pixel_reference<bits8&, rgb_t>`  
>  
> An image_view instantiated with such a locator will have a reference type of `planar_pixel_reference<bits8&,rgb_t>`. Now suppose this image_view is passed to `kth_channel_view()`. This results in the instantiation of a `kth_channel_deref_fn` with a result_type of `pixel<bits8&, gray_layout_t>&` however, the deref functor will initialize this result_type with a `pixel<bits8,gray_layout_t>&`.  
>  
> `detail::kth_channel_deref_fn` needs to ensure the channel type of its `result_type` is not a reference type.  
  
Patch:  
  
```  
--- image_view_factory.hpp.orig	2013-08-19 12:20:06.000000000 -0500  
+++ image_view_factory.hpp	2013-08-19 13:11:49.000000000 -0500  
@@ -479,7 +479,8 @@  
         BOOST_STATIC_CONSTANT(bool, is_mutable=pixel_is_reference<SrcP>::value && pixel_reference_is_mutable<SrcP>::value);  
     private:  
         typedef typename remove_reference<SrcP>::type src_pixel_t;  
-        typedef typename kth_element_type<src_pixel_t, K>::type channel_t;  
+        typedef typename kth_element_type<src_pixel_t, K>::type channel_ref_t;  
+        typedef typename remove_reference<channel_ref_t>::type channel_t;  
         typedef typename src_pixel_t::const_reference const_ref_t;  
         typedef typename pixel_reference_type<channel_t,gray_layout_t,false,is_mutable>::type ref_t;  
     public:  
```  
  
Example program illustrating the issue:  
  
```  
#include <iostream>  
#include <boost/gil/gil_all.hpp>  
using namespace boost;  
using namespace gil;  
  
//  
// Do-nothing dereference adaptor  
//  
template <typename View>  
struct deref_fn {  
    BOOST_STATIC_CONSTANT(bool, is_mutable=false);  
    typedef deref_fn<typename View::const_t> const_t;  
    typedef typename View::value_type value_type;  
    typedef typename View::reference reference;  
    typedef typename View::point_t argument_type;  
    typedef reference result_type;  
  
    deref_fn() {;}  
    deref_fn(const View& v) : mView(v) { ; }  
    result_type operator()(const argument_type& p) const {  
        return mView(p);  
    }  
  
    View mView;  
};  
  
typedef rgb8_planar_image_t image_t;  
typedef image_t::view_t view_t;  
typedef deref_fn<view_t> fn_t;   
typedef virtual_2d_locator<fn_t, false> loc_t;  
typedef image_view<loc_t> virt_view_t;  
typedef kth_channel_view_type<0, virt_view_t>::type chan0_view_t;  
  
int main(int argc, char **argv) {  
    image_t img(4,4,rgb8_pixel_t(1,2,3),0);  
    view_t v(view(img));  
  
    fn_t fn(v);  
    loc_t loc(view_t::point_t(0,0), view_t::point_t(1,1), fn);  
    virt_view_t virtView(v.dimensions(), loc);  
  
    //  
    // Here detail::kth_channel_deref_fn<K,SrcP> is instantiated with  
    // SrcP = 'planar_pixel_reference<bits8&, rgb_t>'. The    
    // kth_channel_deref_fn then declares its result_type (and thereby   
    // the chan0_view_t:reference) to be a  
    // 'pixel<bits8&, gray_layout_t>&'  
    //  
    chan0_view_t chan0View(kth_channel_view<0,virt_view_t>(virtView));  
  
    assert((is_same<chan0_view_t::reference,   
                    pixel<bits8&, gray_layout_t>& >::value));  
  
    //  
    // This will typically cause a bus error because the result_type  
    // of the deref adaptor is a 'pixel<bits8&, gray_layout_t>&'  
    // which gets initialized in the deref function by a   
    // 'pixel<bits8, gray_layout_t>&'  
    //  
    assert(chan0View(0,0) == gray8_pixel_t(1));  
    return 0;  
}  
```

---

## Comment 1

> Username: mloskot  
> Created at: 2018-10-08 10:00:00 UTC  
> Url: https://github.com/boostorg/gil/issues/151#issuecomment-427779527  

OP `Shaun Marko <smarko@…>` wrote in https://svn.boost.org/trac10/ticket/9020#comment:1  
  
> Please retract this bug report. The proposed fix makes the `kth_channel_view` read-only for virtual views with reference types of `planar_pixel_reference`.

---

## Comment 2

> Username: mloskot  
> Created at: 2018-10-08 10:03:38 UTC  
> Updated at: 2018-10-17 19:38:11 UTC  
> Url: https://github.com/boostorg/gil/issues/151#issuecomment-427780493  

Unless someone feels like taking a shot to improve the proposed patch, I'd suggest we drop and close it as per the OP-s comment. /cc @stefanseefeld & @chhenning
