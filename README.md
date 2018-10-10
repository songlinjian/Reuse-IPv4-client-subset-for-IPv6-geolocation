#  Dual-stack GeoIP: Taking IPv4 GeoIP into account for IPv6 GeoIP 

## Abstract

During the IPv6 adoption, IPv6 GeoIP is identified as a challenge and speed bump for Content and application operator. Some studies and operational experiences show that state-of-art the accuracy of IPv6 GeoIP is far less reliable than IPv4 GeoIP for operational use. This memo proposes an approach and use cases to provide better IPv6 GeoIP based on an intuitive idea: if IPv6 GeoIP is not as good as IPv4 GeoIP, why not use dual-stack host's IPv4 GeoIP for IPv6 GeoIP purpose? 

REMOVE BEFORE PUBLICATION: The source of the document with test script is currently placed at GitHub [IPv6-GeoIP-GitHub].  Comments and pull request are welcome.

## Introduction

GeoIP (IP-based Geolocation) is nowadays widely used in Geolocation-based applications, Content distribution and traffic scheduling, and Geolocation-based security policy. The key issue on GeoIP is the accuracy which vary between IPv4 and IPv6. Since IPv4 has been used for many years, IPv4 GeoIP is accurate enough for Geolocation-based application. In contrast IPv6 is newly deployed and new IPv6 address blocks are being assigned from RIRs to ISPs and organizations. IPv6 location information may not be well documented. So IPv6 GeoIP is identified as a notable challenge and speed bump for Content and application operator. Some studies and operational experiences [1,2] show that state-of-art the accuracy of IPv6 GeoIP is relatively poor in comparison to their IPv4 counterparts.

One typical example of GeoIP application in DNS field is that many authoritative nameservers today return different and tailored responses based on the perceived topological location of the user. If IPv6 GeoIP is not precise enough, the performance of topology-sensitive authoritative nameserver will be poorer in IPv6 than IPv4. Users' experience will be impacted , and it may end up with hesitation or hopelessness for the authoritative DNS operator to update its DNS to Dual Stack. It is a problem.

It is a intuition that if IPv6 GeoIP is not as good as IPv4 GeoIP, why not use dual-stack host's IPv4 GeoIP for IPv6 GeoIP purpose? The rationale is simple that given dual-stack is widely adopted as a main transition strategy, the typical case is one network interface of a host is assigned with both IPv6 and IPv4 addresses. The client's IP subnet and Geolocaiton information has inherent one-to-one mapping between IPv6 and IPv4. Since IPv4 GeoIP database is relatively stable and more accurate for the time being, it is wise to map IPv6 prefix and IPv4 subnet, and take IPv6 prefix as a certain key to search the IPv4 GeoIP database to deliver a better GeoIP result.

However, to the best knowledge of the authors, no evidence shows the available open GeoIP systems take advantage of IPv6/IPv4 GeoIP mapping and try something like translating IPv6 geolocation lookup to IPv4. (Maxmind only do this with IPv6 addresses that contain an embedded IPv4 address.) The Regional Whois registry, online lookup services(IP2Location.com) and offline database (MaxMind GeoIP2) most probably build separate systems between IPv4 and IPv6 without any reference or mapping. 

This memo intends to introduce a practice taking advantage of IPv4/IPv6 GeoIP mapping towards a dual-stack GeoIP and achieve better IPv6 GeoIP by leveraging IPv4 GeoIP information. For informational purpose, this memo describes some existing and deployed operational practice as well as some technologies to be implemented. Three use cases are introduced on taking IPv4 subnet information into account for topology-sensitive authoritative nameserver. The author also suggest it may be a useful aid for IPv6 GeoIP service provider taking IPv4 GeoIP into account for more general case. 

## Methodology and Use cases 

Given that separate IPv4 GeoIP and IPv6 GeoIP are available, to enhance IPv6 GeoIP by leveraging IPv4 GeoIP, the basic methodology is to make both IPv6 prefix and IPv4 subnet visible for Geolocation-based applications. There are three use cases for topology-sensitive authoritative nameserver.

### Use Case 1: AAAA Query via IPv4 transport

The typical case that client and recursive server are close in topological sense that recursive server's source address is sufficient for authoritative nameservers to reply a appropriate tailored response. If cache miss for a AAAA query, the resolver may send that AAAA query to the authoritative server via IPv4. In that case, the authoritative server can take use of the source address of the query and response a appropriate AAAA record according to the output of IPv4 GeoIP lookup. 

For example, A dual-stack client and resolver located in ISP A asking AAAA record via IPv4 for a site. The site authoritative server with the knowledge of the IPv6 and IPv4 GeoIP information are capable to recognize where the query come from and know exactly where the IPv6 traffic should go towards the site instance located in ISP A.

Note that it's a real case which is largely implemented and deployed. One example is introduce in appendix A. 

### Use Case 2: IPv4 Subnet in IPv6 DNS Query  

Case 1 has some limitations. Only part of AAAA DNS response is aided by IPv4 GeoIP information, because not all AAAA query are sent via IPv4 transport. It also require topological adjacency between stub-resolver and recursive server which limits the scope. 

One approach to break the limitation is to enable the client (stub resolver or forwarding server) or recursive server to carry IPv4 Subnet in the query towards authoritative server. ECS [RFC7871] option is a useful tool for that purpose. To fit this case, there should be an extension to ECS logic in which the ECS in AAAA query  contain not only IPv6 prefix but also IPv4 subnet as well. 

Thanks to ECS, case 2 expands the scope of case 1 by add an extension. However it mainly covers the scenario where the public DNS supports the ECS extension to carry IPv4 subnet in IPv6 DNS query. Both stub resolver and ISP resolver do not have enough motivation deploy ECS and the extension, not to mention the privacy concerns introduce by ECS.

### Use Case 3: IPv4 Subnet in DOH query

Different from Use Case 1 and 2, Use case 3 focus on the popular mobile Internet where the mobile application providers have more control over the DNS resolution in both client and server. It is possible for handset to directly send AAAA query via IPv6 and http transport to DNS server which operated by mobile application provider DNS. IPv4 subnet of the handset can be carried by that query. DOH (draft-ietf-doh-dns-over-https) protocol is a available tool to achieve that goal.

Note that this use case is also a real one which is under development and test. More information can be found in appendix A

## limits consideration

Multihoming is a issue for this practice, because IPv4 and IPv6 address of a site may be assigned by different upstream ISP. Although the physical location of a client are the same but the locations of network topology are different which real matters for many Geolocation-based application.

## Security Considerations

TBD

## Appendix A IPv6 GeoIP and DNS over HTTP practice in Tencent



## Reference

1. [Geolocation with IPv6](https://community.infoblox.com/t5/IPv6-CoE-Blog/Geolocation-with-IPv6)
2. [Comparing the Accuracy of IPv4 and IPv6 Geolocation Databases](https://pdfs.semanticscholar.org/0705/1014673302f97a762e74b795b70efdd74a1c.pdf)