# #919 - Zone ID is erased in host_address round-trip [Closed]

> Username: Ericson2314  
> Created at: 2025-08-25 20:44:40 UTC  
> Updated at: 2025-08-29 17:01:51 UTC  
> Closed at: 2025-08-29 17:01:51 UTC  
> Url: https://github.com/boostorg/url/issues/919  

I see that #711 was closed by #771. I agree with the proposed resolution *not* to put the Zone ID in the IPv6 address type -- it is indeed not part of of an IPv6 address. However, I think that just putting it in ~~`url_view`~~ `url_view_base` and not `url`, `authority_view`, etc. means that the work is not yet finished --- some operations support Zone IDs and others do not, and it can be tricky to figure out either case.  
  
See for example the hack that my https://github.com/NixOS/nix/pull/13819 will do to get around this.  
  
In particular, the issue is that `url_view::host_address` and `url::set_host_address` do not round-trip as expected in the presence of Zone IDs.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2025-08-25 21:11:48 UTC  
> Updated at: 2025-08-25 21:14:24 UTC  
> Url: https://github.com/boostorg/url/issues/919#issuecomment-3221759341  

> However, I think that just putting it in url_view  
  
It's actually in url_view_base. Other URL classes inherit it. You're correct about authority_view though.  
  
> some operations support Zone IDs and others do not  
  
Do you mean some containers?  
  
> In particular, the issue is that url_view::host_address and url::set_host_address do not round-trip as expected in the presence of Zone IDs.  
  
Mmm... That's a different issue from what you described before. In that case, we can just come up with a minimal case that doesn't work and add a test for this new issue.

---

## Comment 2

> Username: Ericson2314  
> Created at: 2025-08-25 22:13:29 UTC  
> Url: https://github.com/boostorg/url/issues/919#issuecomment-3221902582  

I haven't look into how to build/run the tests yet, but I think something like this should catch it  
  
```diff  
diff --git a/test/unit/url_view.cpp b/test/unit/url_view.cpp  
index e76c551..7b97a64 100644  
--- a/test/unit/url_view.cpp  
+++ b/test/unit/url_view.cpp  
@@ -647,6 +647,14 @@ public:  
             check("ftp://a.com/files/", "");  
             BOOST_TEST_NOT(parse_uri("http://[fe80::1%25]/").has_value());  
         }  
+        // issue 919  
+        {  
+            core::string_view with_zone_id = "http://[fe80::1%25eth0]/";  
+            url u0(with_zone_id);  
+            u0.set_host_address(u0.host_address());  
+            url u1(with_zone_id);  
+            BOOST_TEST_EQ(u0, u1);  
+        {  
     }  
  
     void  
```
