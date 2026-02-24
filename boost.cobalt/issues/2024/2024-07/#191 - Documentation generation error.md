# #191 - Documentation generation error [Open]

> Username: LegalizeAdulthood  
> Created at: 2024-07-17 16:03:33 UTC  
> Updated at: 2024-07-17 16:03:33 UTC  
> Url: https://github.com/boostorg/cobalt/issues/191  

The generated 1.85 docs have an error message embedded in them:  
  
```  
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
  
See https://www.boost.org/doc/libs/1_85_0/libs/cobalt/doc/html/index.html#awaitables
