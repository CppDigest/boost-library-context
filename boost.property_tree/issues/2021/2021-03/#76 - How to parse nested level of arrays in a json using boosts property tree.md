# #76 - How to parse nested level of arrays in a json using boosts property tree? [Closed]

> Username: Neeraj0019  
> Created at: 2021-03-12 06:31:56 UTC  
> Updated at: 2021-05-29 17:32:43 UTC  
> Closed at: 2021-05-29 17:32:42 UTC  
> Url: https://github.com/boostorg/property_tree/issues/76  

Hi,   
I want to parse below JSON data and get the "netconf-clients" available. Can someone help me on how to do it.  
  
`('{"o-ran-dhcp:dhcp":   
	{"interfaces": [{"interface": "mPlaneVlan295", "dhcpv4": '  
															'{"client-id": "53148/473966A.101/L1174115125",  
															"dhcp-server-identifier": ''"192.168.100.1",   
															"interface-mtu": 1500,   
															"domain-name": "nokia.com", '  
															'"domain-name-servers": ["192.168.1.1", "192.168.1.2"],  
															"default-gateways": ''["192.168.100.1"],   
															**"netconf-clients": [{"client": "192.168.100.1", ''"optional-port": 4334},   
																				{"client": "xyz.com", "optional-port": 4334}], '**  
															'"ca-ra-servers": [{"servers": "192.168.100.1", "port-number": 99, '  
															'"ca-ra-path": "var/opt/z", "subject-name": "radio", "protocol": "HTTPS"}], '  
															'"segw": [{"gateways": "1.2.3.4"}], "local-ip": "192.168.100.3"},   
															"dhcpv6": ''{}}],   
															"m-plane-dhcp": {"private-enterprise-num": 53148, "vendor-class-data": ' '"o-ran/No125"}}}')`  
  
Br,  
Neeraj

---

## Comment 1

> Username: Neeraj0019  
> Created at: 2021-03-12 08:28:48 UTC  
> Url: https://github.com/boostorg/property_tree/issues/76#issuecomment-797324047  

Hi,  
Below code seems to work.  
  
#include <boost/property_tree/ptree.hpp>  
#include <boost/property_tree/json_parser.hpp>  
#include <iostream>  
using boost::property_tree::ptree;  
  
  
  
int main() {  
  
std::string ss = "{\"o-ran-dhcp:dhcp\": {\"interfaces\": [{\"interface\": \"mPlaneVlan295\", \"dhcpv4\": {\"client-id\": \"174115125\", \"dhcp-server-identifier\": \"192.168.100.1\", \"interface-mtu\": 1500, \"domain-name\": \"xyz.com\", \"domain-name-servers\": [\"192.168.1.1\", \"192.168.1.2\"], \"default-gateways\": [\"192.168.100.1\"], \"netconf-clients\": [{\"client\": \"192.168.100.1\", \"optional-port\": 4334}, {\"client\": \"nokia.com\", \"optional-port\": 4334}], \"ca-ra-servers\": [{\"servers\": \"192.168.100.1\", \"port-number\": 99, \"ca-ra-path\": \"var/opt/z\", \"subject-name\": \"radio\", \"protocol\": \"HTTPS\"}], \"segw\": [{\"gateways\": \"1.2.3.4\"}], \"local-ip\": \"192.168.100.3\"}, \"dhcpv6\": {}}], \"m-plane-dhcp\": {\"private-enterprise-num\": 53148, \"vendor-class-data\": \"o-r115125\"}}}";  
  
ptree pt;  
std::istringstream is(ss);  
read_json(is, pt);  
  
  
boost::property_tree::ptree dhcp = pt.get_child("o-ran-dhcp:dhcp");  
boost::property_tree::ptree ifaces = dhcp.get_child("interfaces");  
  
  
for (auto& e : ifaces.get_child("")) {  
        ptree dhcp = e.second.get_child("dhcpv4");  
        ptree clients = dhcp.get_child("netconf-clients");  
        for (auto& e : clients.get_child(""))  
        {  
                std::cout << "client name: " << e.second.get<std::string>("client") << "\n";  
                std::cout << "optional port name: " << e.second.get<std::string>("optional-port") << "\n";  
        }  
  
}  
  
}  
  
Is this correct way?  
  
Br,  
Neeraj

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-05-29 17:32:42 UTC  
> Url: https://github.com/boostorg/property_tree/issues/76#issuecomment-850869817  

Yes this seems correct
