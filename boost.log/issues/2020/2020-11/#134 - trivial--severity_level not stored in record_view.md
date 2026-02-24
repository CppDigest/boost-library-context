# #134 - trivial::severity_level not stored in record_view [Closed]

> Username: Zombie-3000  
> Created at: 2020-11-18 14:52:06 UTC  
> Updated at: 2020-11-25 19:32:40 UTC  
> Closed at: 2020-11-18 15:15:41 UTC  
> Url: https://github.com/boostorg/log/issues/134  

Hi,  
  
please take a look at this simple example attached. [example.cpp.txt](https://github.com/boostorg/log/files/5560768/example.cpp.txt)  
  
this outputs:  
  
`2020-Nov-18 15:39:10.730151 0x000036dc 0x00000650 **info**: Some info  
2020-Nov-18 15:39:10.749148 0x000036dc 0x00000650 **warning**: Some warning  
2020-Nov-18 15:39:10.754146 0x000036dc 0x00000650 **error**: Some error  
--------------------------  
2020-Nov-18 15:39:10.730151 0x000036dc 0x00000650 **error**: Some info  
2020-Nov-18 15:39:10.749148 0x000036dc 0x00000650 **error**: Some warning  
2020-Nov-18 15:39:10.754146 0x000036dc 0x00000650 **error**: Some error`  
  
It seems, that the value of trivial::severity_level ist not getting stored in a record_view object.  
  
Tested with VS2019 & boost 1.74 and boost 1.72  
  
I'dont know, if i'm doing something wrong, or if this is a bug.  
  
Thank you

---

## Comment 1

> Username: Lastique  
> Created at: 2020-11-18 15:15:41 UTC  
> Url: https://github.com/boostorg/log/issues/134#issuecomment-729746634  

You can see in the first three lines that the record view does contain a severity level consistent with what you output.  
  
The latter three lines are printed from the vector `ExampleSinkBackend::records`, which holds `record_view` objects. As the name implies, `record_view` is just a lightweight wrapper that offers a way to access attribute values for the purpose of processing. It does not own the values and after the log record is processed, its contents may get invalidated. In particular, the severity level is actually stored in a thread-local storage, which is reused by every log record made by the thread.  
  
If you want to buffer log records, you have to copy the necessary attribute values into your own structures before returning from `consume`.

---

## Comment 2

> Username: Zombie-3000  
> Created at: 2020-11-25 19:32:40 UTC  
> Url: https://github.com/boostorg/log/issues/134#issuecomment-733908872  

Hi Lastique,  
  
i found a good solution:  
  
inherit from boost::log::sinks::sink and pass true to the constructor.  
Like this:  
`  
class ExampleSink : public logging::sinks::sink {  
public:  
	ExampleSink() : sink(true) { }  
  
	bool will_consume(const logging::attribute_value_set &attr) override {  
		return true;  
	}  
  
	void consume(const logging::record_view &rec) override {  
		print_record(rec);  
		this->records.push_back(rec);  
	}  
  
	void flush() override {  
		// ignore  
	}  
  
	void print() {  
		for (const logging::record_view &rec : this->records) {  
			print_record(rec);  
		}  
	}  
  
private:  
	void print_record(const logging::record_view &rec) {  
		auto ts = rec["TimeStamp"].extract<boost::posix_time::ptime>();  
		auto sev = rec["Severity"].extract<boost::log::trivial::severity_level>();  
		auto msg = rec["Message"].extract<std::string>();  
		auto pid = rec["ProcessID"].extract<boost::log::attributes::current_process_id::value_type>();  
		auto tid = rec["ThreadID"].extract<boost::log::attributes::current_thread_id::value_type>();  
  
		std::cout << ts << " " << pid << " " << tid << " " << sev << ": " << msg << "\n";  
	}  
  
	std::vector<logging::record_view> records;  
};  
`  
  
You should mention the influence of the sinks::sink::sink() constructor parameter on the behavior of record_view in the documentation.  
Best regards
