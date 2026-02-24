# #45 - Is LoggerType intended as the official way to observe connection lifecycle events (CONNACK success/failure, disconnect, transport errors)? [Open]

> Username: Greendogo  
> Created at: 2025-12-15 18:31:21 UTC  
> Updated at: 2025-12-16 10:28:35 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/45  

We’re integrating boost::mqtt5::mqtt_client and need a reliable way to react to connection lifecycle transitions (e.g., set internal “connected/offline” state, emit telemetry, detect misconfiguration).  
  
From the docs:  
- mqtt_client doesn’t expose connect/async_connect; async_run starts the connection attempt(s).  
- The auto-reconnect design can hide persistent misconfiguration, and the docs suggest using the logging mechanism to detect those cases.  
  
We also see mqtt_client has a LoggerType template parameter, and the LoggerType concept allows implementing any subset of callbacks such as:  
at_resolve, at_tcp_connect, at_tls_handshake, at_ws_handshake, at_transport_error, at_connack, at_disconnect, at_ws_handshake.  
  
Questions:  
1) Is supplying a custom LoggerType the intended/official extension point to observe these lifecycle events (beyond printing debug output)?  
2) If yes, is it considered acceptable to use LoggerType callbacks for application state (e.g., raising events/flags), or is LoggerType meant strictly for diagnostics?  
3) If no, is there another recommended pattern/API for “connected / reconnecting / disconnected” signals (or would you consider adding one)?  
  
Implementation-wise, our plan is to provide a LoggerType that forwards at_* calls to std::function callbacks (so we can hook at_connack/at_disconnect/etc.). Does that align with the library’s intended paradigm?

---

## Comment 1

> Username: siladic  
> Created at: 2025-12-16 10:28:35 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/45#issuecomment-3659839883  

The short answer to the question, “Does that align with the library’s intended paradigm?”, is yes.  
  
As you have already seen, we have deliberately chosen to hide connect/disconnect logic from library users. The vast majority of library usage is on edge devices, often deployed in the field. These devices require network connectivity at the moment they need to publish (and possibly receive) data. Since wireless connectivity cannot be assumed to be stable, we have built a retry mechanism—aligned with the MQTT protocol—directly into the library. Implementing retries (reconnect and re-publish) in compliance with the MQTT specification is non-trivial, and we intentionally shield library users from having to solve the same problem repeatedly.  
  
From a design perspective, users may observe what is happening internally when needed, and that is precisely the role of the `LoggerType`. Observation implies that no actions should be taken within observers that would alter the library’s behavior; for this reason, `LoggerType` is the appropriate and intentional name.
