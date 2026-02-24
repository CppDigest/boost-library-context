# #329 - boost v1.7.1 problem on Android 10 (Samsung A71) [Closed]

> Username: laichis  
> Created at: 2020-03-07 10:04:03 UTC  
> Updated at: 2020-12-30 01:13:22 UTC  
> Closed at: 2020-12-30 01:13:21 UTC  
> Url: https://github.com/boostorg/asio/issues/329  

Hi,  
  
Here comes a strange issue.  
Condition:  
boost v1.7.1 + openssl v1.0.2h or 1.1.1  
  
The boost is used in the mobile app of android, usually serve Android 5, 6, 7, 8, 9 and newest Android Q;  The boost integrated mobile APP is working great in each OS until a new model: Samsung A71 (SM-A715F/DS)  
  
The boost has a lot of chance(maybe 60% fail rate) connect & wait for 30sec  handshake timed out and got system 125 message, it is confusing to us because it never happens on other mobile phones.  
Can someone take a look to see how can we do to slove the problem on this mobile phone?  
  
  
The message dump from Android:  
V/myapp:     /  
        + void kp::web::ClientConnection::start_connect()  
D/myapp:     |   Connecting to: 111.111.111.111:443  
V/myapp:     /  
V/myapp: /  
    + void kp::web::ClientConnection::handle_connect(const boost::system::error_code&)  
        + kp::web::ProxySetting::Type kp::web::ProxySetting::get_type() const  
V/myapp:     /  
V/myapp:     + static boost::shared_ptr<kp::web::WebProxy> kp::web::WebProxy::create(kp::web::ProxySetting::Type)  
        |   + kp::web::NullProxy::NullProxy()  
        |   /  
        /  
        + virtual void kp::web::NullProxy::connect(boost::asio::ip::tcp::socket&, const kp::web::ProxySetting&, const kp::web::Uri&)  
V/myapp:     /  
D/myapp:     Canceled 1 number of waiters  
V/myapp: /  
V/myapp: + void kp::web::ClientConnection::handle_connect_timeout(const boost::system::error_code&)  
D/myapp:     Connect timeout aborted  
V/myapp: /  
D/ViewRootImpl@b695bed[MainActivity]: MSG_WINDOW_FOCUS_CHANGED 1 1  
D/InputMethodManager: prepareNavigationBarInfo() DecorView@d8a1884[MainActivity]  
    getNavigationBarColor() -855310  
D/InputMethodManager: prepareNavigationBarInfo() DecorView@d8a1884[MainActivity]  
D/InputMethodManager: getNavigationBarColor() -855310  
V/InputMethodManager: Starting input: tba=com.keypasco.myappsample ic=null mNaviBarColor -855310 mIsGetNaviBarColorSuccess true , NavVisible : true , NavTrans : false  
D/InputMethodManager: startInputInner - Id : 0  
I/InputMethodManager: startInputInner - mService.startInputOrWindowGainedFocus  
D/InputTransport: Input channel destroyed: 'ClientS', fd=82  
I/co.myappsampl: Compiler allocated 4266KB to compile void android.view.ViewRootImpl.performTraversals()  
V/FA: Inactivity, disconnecting from the service  
D/ViewRootImpl@b695bed[MainActivity]: ViewPostIme pointer 0  
D/ViewRootImpl@b695bed[MainActivity]: ViewPostIme pointer 1  
V/myapp: + void kp::web::ClientConnection::handle_handshake_timeout(const boost::system::error_code&)  
D/myapp:     Handshake timed out  
V/myapp:     + void kp::web::ClientConnection::close()  
V/myapp:     /  
V/myapp: /  
V/myapp: + void kp::web::ClientConnection::close()  
V/myapp: /  
    + void kp::web::ClientConnection::handle_handshake(const boost::system::error_code&)  
D/myapp:     Canceled 0 number of waiters  
        mydebug: handle_handshake fail exception!  
E/myapp:     system:125  
V/myapp: /  
D/myapp: mydebug: handle_handshake fail exception!  
I/myapp: myprotocolNetworkException: SSL Connect_timeout(30).  
V/myapp: + kp::client_api::v5::AppClient::~AppClient()  
    /  
    + kp::client_api::ApiClient::~ApiClient()  
V/myapp: /

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:13:20 UTC  
> Url: https://github.com/boostorg/asio/issues/329#issuecomment-752293487  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#750](https://github.com/chriskohlhoff/asio/issues/750).
