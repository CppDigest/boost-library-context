# #31 - Release version of shared library broken (at least for Asio code) [Closed]

> Username: erakadjiev  
> Created at: 2015-02-26 12:14:41 UTC  
> Updated at: 2015-12-15 09:04:24 UTC  
> Closed at: 2015-12-15 09:04:24 UTC  
> Url: https://github.com/boostorg/fiber/issues/31  

Hi Oliver,  
  
This is the issue that was traced down in #29. I encountered it today again with another application. Because #29 was originally not about this problem, and you closed it meanwhile anyway, I'm opening a new one.  
  
**Problem:** When dynamically linking against the `RELEASE` version of the Boost Fiber shared library, compilation and linking are successful, but when the binary is run, it fails.   
The problem occurs with the "official" Boost Fiber Asio examples, and also with two different applications of mine.  
  
When dynamically linking against the `debug` shared library, or statically linking against either the `debug` or `release` static library, everything works.    
  
**Note:** I have tested this only with code using Asio. I don't know if the issue affects non-Asio Fibers code.  
  
**Environment:** Ubuntu 14.04 64-bit, GCC 4.9.2, latest Modular Boost, latest Boost Fiber (`develop`) branch  
  
To reproduce it with the official examples:  
  
<ol>  
<li><code>cd /modboost-folder/libs/fiber/examples</code></li>  
<li>Change Jamfile.v2:  
<pre>  
-      &lt;link&gt;static  
+      &lt;link&gt;shared  
+      &lt;linkflags&gt;-lboost_context  
+      &lt;linkflags&gt;-lboost_thread  
</pre>  
</li>  
<li><code>debug</code> build:  
<ul>  
<li><pre>b2 toolset=gcc cxxflags="-std=c++14" address-model=64 architecture=x86 <b>debug</b>  
cd /modboost-folder/bin.v2/libs/fiber/examples/gcc-4.9.2/<b>debug</b>/address-model-64/architecture-x86/threading-multi/asio</pre></li>  
<li>In one terminal <code>./echo_server 1234</code>, in another <code>./echo_client 127.0.0.1 1234</code></li>  
<li><b>Works</b></li>  
</ul>  
</li>  
<li><code>release</code> build  
<ul>  
<li><pre>b2 toolset=gcc cxxflags="-std=c++14" address-model=64 architecture=x86 <b>release</b>  
cd /modboost-folder/bin.v2/libs/fiber/examples/gcc-4.9.2/<b>release</b>/address-model-64/architecture-x86/threading-multi/asio</pre></li>  
<li>In one terminal <code>./echo_server 1234</code>, in another <code>./echo_client 127.0.0.1 1234</code></li>  
<li><b>Doesn't work</b></li>  
</ul>  
</li>  
</ol>

---

## Comment 1

> Username: olk  
> Created at: 2015-12-15 09:04:24 UTC  
> Url: https://github.com/boostorg/fiber/issues/31#issuecomment-164692456  

do you have checked your library-search-paths?
