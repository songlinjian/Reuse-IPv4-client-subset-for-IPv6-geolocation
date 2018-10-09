#  Dual-stack GeoIP: Provide better IPv6 GeoIP by leveraging IPv4 GeoIP

## Abstract

During the IPv6 adoption, IPv6 GeoIP is identified as a challenge and speed bump for Content and application operator. Some studies and operational experiences show that state-of-art the accuracy of IPv6 GeoIP is far less reliable than IPv4 GeoIP for operational use. This memo proposes an approach and use cases to provide better IPv6 GeoIP based on an intuitive idea: if IPv6 GeoIP is not as good as IPv4 GeoIP, why not use dual-stack host's IPv4 GeoIP for IPv6 GeoIP purpose? 

## Introduction

GeoIP (IP-based Geolocation) is nowadays widely used in Geolocation-based applications, Content distribution and traffic scheduling, and Geolocation-based security policy. The key issue on GeoIP is the accuracy which vary between IPv4 and IPv6. Since IPv4 has been used for many years, IPv4 GeoIP is accurate enough for Geolocation-based application. In contrast IPv6 is newly deployed and new IPv6 address blocks are being assigned from RIRs to ISPs and organizations. IPv6 location information may not be well documented. So IPv6 GeoIP is identified as a notable challenge and speed bump for Content and application operator. Some studies and operational experiences show that state-of-art the accuracy of IPv6 GeoIP is far less reliable than IPv4 GeoIP for operational use.

It is intuitive to think that if IPv6 GeoIP is not as good as IPv4 GeoIP, why not use dual-stack host's IPv4 GeoIP for IPv6 GeoIP purpose? Given dual-stack is widely adopted as a main transition strategy, the typical case is one network interface of a host is assigned with both IPv6 and IPv4 addresses. The client's IP subnet and Geolocaiton information has inherent one-to-one mapping between IPv6 and IPv4. Since IPv4 GeoIP database is relatively stable and more accurate for the time being, it is wise to map IPv6 prefix and IPv4 subnet, and take IPv6 prefix as a certain key to search the IPv4 GeoIP database to deliver a better GeoIP result.

However, to the best knowledge of the authors, no evidence shows the available open GeoIP systems take advantage of IPv6/IPv4 GeoIP mapping and try something like translating IPv6 geolocation lookup to IPv4. (Maxmind only do this with IPv6 addresses that contain an embedded IPv4 address.) The Regional Whois registry, online lookup services(IP2Location.com) and offline database (MaxMind GeoIP2) most likely build separate systems between IPv4 and IPv6. 

This memo intends to take advantage of IPv4/IPv6 GeoIP mapping towards a dual-stack GeoIP and achieve better IPv6 GeoIP by leveraging IPv4 GeoIP. For informational purpose this memo introduces three possible use cases on taking IPv4 subnet information into the consideration for IPv6 GeoIP. 

## Methodology and Use cases 

To provide better IPv6 GeoIP by leveraging IPv4 GeoIP, the basic methodology  is to make IPv4 subnet visible for Geolocation-based applications. There are three use cases and approaches to make this happen.

### AAAA Query via IPv4 transport

It is possible that client ask AAAA query to a resolver which in this case is located in the same ISP of that client. If cache miss, resolver may send that AAAA query to the authoritative server via IPv4. In that case, the authoritative server can take use of the source address of the query and response a appropriate AAAA record according to the IPv4 GeoIP. 

For example, A dual-stack client located in ISP A and asking AAAA record via IPv4 for a site. The site authoritative server with the knowledge of the dual-stack GeoIP information are capable to recognize where the query come from and know exactly where the IPv6 traffic should go towards the site instance located in ISP A.

### Client's Query Carrying IPv4 Subnet (using ECS)

### Resolver Query Carrying IPv4 Subnet (using ECS)


## limits 

## Security Considerations





