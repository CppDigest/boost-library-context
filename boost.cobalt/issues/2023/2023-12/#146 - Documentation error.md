# #146 - Documentation error [Closed]

> Username: inn0minatus  
> Created at: 2023-12-23 16:27:42 UTC  
> Updated at: 2024-05-31 00:41:05 UTC  
> Closed at: 2024-05-30 15:35:51 UTC  
> Url: https://github.com/boostorg/cobalt/issues/146  

It seems there's some kind of error in the documentation: https://www.boost.org/doc/libs/1_84_0/libs/cobalt/doc/html/index.html#awaitables  
  
After an Info block there is a browser error displayed:  
``````  
Failed to generate image: mmdc failed:  
Error: Failed to launch the browser process!  
/root/.cache/puppeteer/chrome/linux-1108766/chrome-linux/chrome: error while loading shared libraries: libatk-1.0.so.0: cannot open shared object file: No such file or directory  
  
  
TROUBLESHOOTING: https://pptr.dev/troubleshooting  
  
    at Interface.onClose (file:///root/.nvm/versions/node/v18.18.1/lib/node_modules/@mermaid-js/mermaid-cli/node_modules/@puppeteer/browsers/lib/esm/launch.js:253:24)  
    at Interface.emit (node:events:529:35)  
    at Interface.close (node:internal/readline/interface:534:10)  
    at Socket.onend (node:internal/readline/interface:260:10)  
    at Socket.emit (node:events:529:35)  
    at endReadableNT (node:internal/streams/readable:1368:12)  
    at process.processTicksAndRejections (node:internal/process/task_queues:82:21)  
  
  
flowchart TD  
    aw{await_ready?}  
    aw ---->|true| ar[await_resume]  
    aw -->|false| as[await_suspend]  
    as -->|Resume| ar  
  
```  
  
Tried in Firefox and Chrome.

---

## Comment 1

> Username: sdarwin  
> Created at: 2024-03-20 19:27:26 UTC  
> Url: https://github.com/boostorg/cobalt/issues/146#issuecomment-2010455116  

@klemens-morgenstern ,  it seems that the documentation builds of cobalt are using puppeteer, which in turn requires Google Chrome, and numerous other system packages to be installed.     In the past I have found that a way to ensure the necessary Ubuntu packages are available is to install Firefox also, not because Firefox itself is needed, but because that will install everything else.  
  
Usually, all Boost documentation is generated together by release-tools https://github.com/boostorg/release-tools    
  
Since no other Boost libraries are using the Chrome toolchain, packages are missing.   Cobalt docs are getting puppeteer errors.  
  
Adding all the required packages in release-tools adds many other packages, including browsers and much more, and increases the maintenance and debugging of release-tools.  
  
So, it's worth asking, is this necessary? It would be better to avoid.    
  
For example, let's say Cobalt includes a few flowcharts.   You could render the flowcharts out-of-band, creating images, and then check-in the images to git.  Embed those images in the documentation.   But, not requiring the images be re-rendered/re-generated on every run of CI that's building all of Boost (multiple times per day).

---

## Comment 2

> Username: sdarwin  
> Created at: 2024-03-25 14:31:34 UTC  
> Url: https://github.com/boostorg/cobalt/issues/146#issuecomment-2018140266  

Sent a copy of the images via email.

---

## Comment 3

> Username: sdarwin  
> Created at: 2024-05-31 00:41:03 UTC  
> Url: https://github.com/boostorg/cobalt/issues/146#issuecomment-2141051335  

Not absolutely sure, but an idea might be to switch  
  
`image::{docdir}/images/lazy_eager2.png[] `  
  
to   
  
`image::../images/lazy_eager2.png[]`
