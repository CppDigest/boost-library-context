# #654 - Proposal: Make read/write WKT more symmetric by adding writing to std::string [Closed]

> Username: pauljurczak  
> Created at: 2019-12-24 22:00:19 UTC  
> Updated at: 2021-03-03 13:06:08 UTC  
> Closed at: 2021-03-03 13:06:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/654  

AFAIK, the only way to write WKT provided by boost.geometry is a stream manipulator. In many cases it is more convenient to produce a `std::string` representation directly, as it is provided for reading by `read_wkt`. I would like to propose `write_wkt` function operating on a string and geometry.

---

## Comment 1

> Username: mloskot  
> Created at: 2019-12-24 22:13:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/654#issuecomment-568806676  

Can you present a sample of desired syntax of usage?

---

## Comment 2

> Username: pauljurczak  
> Created at: 2019-12-25 01:11:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/654#issuecomment-568816631  

Here is the context I'm working with right now (`Polyline` is a geometry):  
```  
struct Scan {  
	int      id;  
	float    rangeMax;  
	Polyline points;  
        ...  
};  
```  
In order to write a JSON file with `points` stored as WKT string, I have:  
```  
void Scan::writeJSON(string fileName) {  
  Json          json;  
  ofstream      file(fileName + ".json");  
  ostringstream stream;  
  
  stream << bg::wkt(points);  
  
  json["id"]       = id;  
  json["rangeMax"] = rangeMax;  
  json["points"]   = stream.str();  
  
  file << json;  
}  
```  
I would much preferred:  
```  
void Scan::writeJSON(string fileName) {  
  Json          json;  
  ofstream      file(fileName + ".json");  
    
  json["id"]       = id;  
  json["rangeMax"] = rangeMax;  
  json["points"]   = bg::write_wkt(points);  
  
  file << json;  
}  
```  
Additionally, for reading, I would prefer:  
```  
  json["points"] = bg::read_wkt(points);  
```  
over the current option:  
```  
  bg::read_wkt(json["points"], points);  
```

---

## Comment 3

> Username: awulkiew  
> Created at: 2019-12-28 16:35:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/654#issuecomment-569431014  

Even though a user could implement this short function if needed indeed it would be convenient if the library provided it out of the box. I'm not sure about the naming though. `read_wkt` seems to be a good name for a function taking `string` as output argument:  
  
    bg::write_wkt(points, json["points"]);  
  
I'd expect the function you proposed to have a name like `as_wkt` or `to_wkt`. The standard name for this function is `ST_AsText` and PostGIS also has `ST_AsEWKT`. But I personally prefer `to_wkt` name because it corresponds to `from_wkt` (see below).  
  
---  
  
Regarding the second proposal (`read_wkt`). The argument and return type above are interchanged, correct? The function would have to take `string` and return a geometry. Furthermore it would have to know the type of a geometry. This is why typically geometries are taken as function arguments in Boost.Geometry. So the type would have to be passed explicitly. The call would rather look like this:  
  
    points = bg::read_wkt<Polyline>(json["points"]);  
  
In such cases typically a name with `return_` prefix is used in Boost.Geometry, e.g. `return_envelope` but in this case it would have to be `return_from_wkt` or something like that, unless you have a different idea. So I guess we could relax this rule and in the case of IOs allow functions with `from_` prefix to have the same semantics. So it'd be:  
  
    points = bg::from_wkt<Polyline>(json["points"]);

---

## Comment 4

> Username: pauljurczak  
> Created at: 2019-12-28 23:59:31 UTC  
> Updated at: 2019-12-29 00:08:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/654#issuecomment-569460878  

Name `as_wkt` or `to_wkt` is even better. I was only trying to state my case and leave the name choices to you, as my experience with boost.geometry is very minimal.   
  
The second part could be implemented as `as_string` or `to_string`. `to_string` looks nicer since there is already a standard `std::to_string()` function.   
  
To sum up, `to_wkt` and `to_string` could be used as geometry <-> WKT string interface.

---

## Comment 5

> Username: awulkiew  
> Created at: 2019-12-29 00:23:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/654#issuecomment-569462449  

The problem with `to_string` is that there can be many text formats so this name would be ambiguous. Currently we support WKT, DSV and SVG.

---

## Comment 6

> Username: pauljurczak  
> Created at: 2019-12-29 01:19:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/654#issuecomment-569464937  

You are right. I read your responses a bit too casually, sorry about that. `from_wkt` and `to_wkt` pair sounds great. It could be extended to `from_`/`to_` pairs for other representations (DSV, SVG), if needed.

---

## Comment 7

> Username: mloskot  
> Created at: 2019-12-31 20:07:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/654#issuecomment-569983587  

So, the consensus is to provide `to_<format>` and `from_<format>` utility functions for the I/O routines. Correct?  
  
@pauljurczak Are you going to propose implementation and submit PR?

---

## Comment 8

> Username: pauljurczak  
> Created at: 2019-12-31 22:39:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/654#issuecomment-570000697  

I definitely support `to_<format>` and `from_<format>` utility functions for the I/O routines. Unfortunately, I'm not in a position to provide implementation. I'm just a beginner casual user swamped with other projects. BTW, I don't have enough template metaprogramming experience to contribute to boost.geometry.

---

## Comment 9

> Username: awulkiew  
> Created at: 2020-01-02 01:31:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/654#issuecomment-570100591  

@barendgehrels what do you think?  
  
Btw, I made a quick performance test (MSVS2017 x64 /O2, appending 3 strings of length 10) and it seems that appending to `std::string` with `+=` is several times faster than formatting with `std::ostringstream`. At least with my test case.

---

## Comment 10

> Username: barendgehrels  
> Created at: 2020-01-03 15:04:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/654#issuecomment-570597215  

Hi @awulkiew  @pauljurczak I'm OK with to_wkt and from_wkt . Thanks.  
It is often convenient and (I did not expect that) especially if it can be much faster, we should have it

---

## Comment 11

> Username: awulkiew  
> Created at: 2020-01-03 18:11:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/654#issuecomment-570652354  

@barendgehrels I was thinking about it more and there are some decisions we'd have to make if we wanted to provide it.  
  
With streams the user can set the desired precision with `std::setprecision`. This is not possible with `std::string`. The obvious solution is to use max precision for native types but for user-defined types it is not possible. So we'd need another trait for that or maybe depend on `boost::lexical_cast` default handling.  
  
Btw, `boost::lexical_cast` docs states:  
  
> The lexical_cast function template offers a convenient and consistent form for supporting common conversions to and from arbitrary types when they are represented as text. The simplification it offers is in expression-level convenience for such conversions. For more involved conversions, such as where precision or formatting need tighter control than is offered by the default behavior of lexical_cast, the conventional std::stringstream approach is recommended. Where the conversions are numeric to numeric, boost::numeric_cast may offer more reasonable behavior than lexical_cast.  
  
Regarding the performence. This was only a simple test of attaching strings to a string. It is possible that in case of `to_wkt` the majority of the time will be consumed by conversion from coordinates to `std::string` which has to be done one way or another. It could be done in several ways:  
- `std::ostringstream`,  
- `boost::lexical_cast`,  
- function like `itoa`/`ftoa` (this would probably be the fastest),  
- etc.  
  
If we simply relied on `stringstream` internally only for coordinates it is possible that there would be no difference in performance.  
  
And again we have to take user-defined types into account. So this proposal is not a straightforward thing and I'm starting to lean towards "not doing it" especially that a user can write a simple function for convenience if needed. But I'm open to suggestion, maybe one of you has an idea how to elegantly deal with the above problems?

---

## Comment 12

> Username: pauljurczak  
> Created at: 2020-01-04 11:22:47 UTC  
> Updated at: 2020-01-04 11:46:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/654#issuecomment-570777309  

@awulkiew How about not providing any means for setting precision when using `to_<format>` and `from_<format>`? I was thinking about convenience for new users of boost.geometry, who may not know that precision can be set for text representations. I had no idea that it's available and no need for it.

---

## Comment 13

> Username: mloskot  
> Created at: 2020-01-04 13:51:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/654#issuecomment-570787239  

Since the idea is about textual serialisation of coordinates, I expect sooner than later someone will ask why it outputs N decimal places and not M. It's quite frequent question on GDAL/OGR mailing list w.r.t. GeoJSON. Hence, OGR driver offers reasonable defaults, overridable:  
https://gdal.org/drivers/vector/geojson.html  
  
> COORDINATE_PRECISION = int_number: (OGR >= 1.9.0) Maximum number of figures after decimal separator to write in coordinates. Default to 15 for GeoJSON 2008, and 7 for RFC 7946.

---

## Comment 14

> Username: pauljurczak  
> Created at: 2020-01-05 01:39:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/654#issuecomment-570835846  

There can be a free standing function provided to set the precision then. The simplest way is: `set_wkt_string_precision()`, but there may be something fitting better with boost.geometry design.

---

## Comment 15

> Username: barendgehrels  
> Created at: 2020-01-05 16:29:50 UTC  
> Updated at: 2020-01-05 16:30:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/654#issuecomment-570926793  

@awulkiew  good point! Yes, precision is important and any default will not be good for everyone.  
@pauljurczak a free standing function would require a global "setting" variable which we don't have in our library  
@mloskot yes we might add an overridable default for precision, in the function itself and precision is probably the most important usecase, but fixed/scientific might be another thing uses might ask.  
  
So I agree with @awulkiew  - maybe we can just provide an example function in the documentation about how to convert a geometry to a WKT (or other format) string, it's trivial, and keep the library itself as it is now.

---

## Comment 16

> Username: awulkiew  
> Created at: 2020-01-05 19:35:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/654#issuecomment-570940504  

Ok, before closing it I'll mention another solution, i.e. to always use the max precision of a numeric type preserving all of the data. This is the only reasonable default for me because as @mloskot have said, it's a serialization problem. In other words we could set precision to `std::numeric_limits<T>::digits10` or `std::numeric_limits<T>::digits10 + 1` if `std::numeric_limits<T>::is_specialized` is `true`. For other types the library could simply fail to compile forcing the user to either specialize `std::numeric_limits` or use `stringstream` with `bg::wkt` explicitly. Or we could add another trait in `math/` or `io/` if `to_string` with user-defined types was really needed. We could postpone the implementation of the trait until a user requests it.

---

## Comment 17

> Username: pauljurczak  
> Created at: 2020-01-05 22:46:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/654#issuecomment-570955873  

@barendgehrels I agree that precision parameter with a default value to `to_<format>` and `from_<format>`  sounds better. I disagree that a useful function, trivial or not, is left for user to define. Creating and discarding a `stringstream` each time I want to get a `string` representation seems hackish and inefficient.

---

## Comment 18

> Username: sudo-panda  
> Created at: 2020-02-27 19:12:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/654#issuecomment-592128084  

I would like to work on this issue.

---

## Comment 19

> Username: mloskot  
> Created at: 2020-02-27 19:19:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/654#issuecomment-592131676  

@sudo-panda I don't think you have to request permission. You pick an item, work on it and you submit PR when you are ready to show anything.  
  
IMHO, you don't have to wait until your contribution is polished perfectly and you should consider submitting PR if you wish to receive early feedback, even if it is half-finished. There will likely be multiple review iterations anyway.

---

## Comment 20

> Username: sudo-panda  
> Created at: 2020-02-28 09:46:54 UTC  
> Updated at: 2020-02-28 09:49:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/654#issuecomment-592434541  

The simplest way to implement `to_wkt` is to use the `write_wkt` itself and convert the string_stream to string. Similarly for `from_wkt` we can use the `read_wkt` function and return the string.  
  
The other way would be to extensively change every function to return string instead of void and taking in a string_stream. Which one do you think is better?

---

## Comment 21

> Username: pauljurczak  
> Created at: 2020-02-28 10:27:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/654#issuecomment-592450693  

@sudo-panda I think the first option is a good start. It is somewhat inefficient (heap allocations and copying), but maybe good enough for most cases. It would definitely work for me.

---

## Comment 22

> Username: awulkiew  
> Created at: 2020-02-28 10:47:13 UTC  
> Updated at: 2020-02-28 10:50:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/654#issuecomment-592458473  

`to_wkt` should call `dispatch::devarianted_wkt` just like `wkt_manipulator` does. However `dispatch::devarianted_wkt` should not only be able to take any `OutputStream` but also `std::string` or rather `basic_string<CharT, Traits, Allocator>`. It would be enough to simply pass any of these two things and only when something is added to the output instead of directly using string formatting (`os << abc`) call some static method of a struct dispatched by an Output kind (either a stream or a string). So in other words add another layer of abstraction between the wkt algorithm and the kind of Output that is passed into it. E.g.:  
  
```  
template <typename Output>  
struct output_formatter  
{  
    template <typename T>  
    void add(Output & out, T const& v)  
    {  
        out << v;  
    }  
};  
  
struct output_formatter<std::string>  
{  
    template <typename T>  
    void add(std::string & out, T const& val)  
    {  
        std::stringstream ss;  
        ss << val;  
        out += ss.str();  
    }  
  
    void add(std::string & out, char c)  
    {  
        out += c;  
    }  
  
    void add(std::string & out, const char * s)  
    {  
        out += s;  
    }  
  
    void add(std::string & out, std::string const& str)  
    {  
        out += str;  
    }  
};  
```  
  
Later replace:  
 - std::string with std::basic_string<CharT, Traits, Allocator>  
 - char with CharT  
 - const char * with const CharT *  
 - std::stringstream with std::basic_stringstream<CharT, Traits>  
  
and then internally e.g. in `detail::wkt::stream_coordinate` instead of  
  
```  
os << (I > 0 ? " " : "") << get<I>(p);  
```  
  
call:  
  
```  
if (I > 0)  
{  
    output_formatter<Output>::add(out, ' ');  
}  
output_formatter<Output>::add(out, get<I>(p));  
```

---

## Comment 23

> Username: mloskot  
> Created at: 2020-02-28 12:28:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/654#issuecomment-592491947  

@awulkiew Good stuff, thanks!  
  
@sudo-panda   
> Which one do you think is better?  
  
My rule of thumb is to always start with option that is the least intrusive to the existing code.  
IOW, adding new features without or with minimal changes to existing code is always the safest.  
If you follow @awulkiew's excellent suggestion to "add another layer of abstraction", you will also follow that rule.  
  
If you have a basic implementation, something that compiles and runs basic tests, you can submit PR so the review can start, then you will receive confirmation if the overall direction is good or not.
