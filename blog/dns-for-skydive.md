---
title: DNS Data Collection
layout: blog-post
author: Andre Kassis
date: 07/07/2019
---

# Introduction and Motivations

As the Skydive documentation states, one of the primary services Skydive aims to provide is protocol analysis. To that end, the Skydive probes managed by the agents intercept every packet inside its environment and extract protocol-specific data to present to the users for them to gather information concerning activity inside their network, identify security breaches and attacks, and enhance the quality of service.

<p>
  <a href="/assets/images/blog/skydive-protocols.png" data-lightbox="WebUI-1" data-title="Skydive Protocols">
    <center>
      <img src="/assets/images/blog/skydive-protocols.png" width="500"/>
    </center>
  </a>
</p>

That said, Skydive is still undergoing development, and the number of supported protocols is still meager. One of the first protocols that Skydive is currently able to handle is DNS.

&quot;The  **Domain Name System**  ( **DNS** ) is a hierarchal and decentralized naming system for computers, services, or other resources connected to the Internet or a private network. It associates various information with domain names assigned to each of the participating entities. Most prominently, it translates more readily memorized domain names to the numerical IP addresses needed for locating and identifying computer services and devices with the underlying network protocols.&quot; ([https://en.wikipedia.org/wiki/Domain\_Name\_System](https://en.wikipedia.org/wiki/Domain_Name_System))

# DNS Traffic Collection

As Skydive collects traffic-related information based on the notion of a flow, which is typically a connection between to endpoints, each packet that is captured by one of the agents is matched to its corresponding flow. Hence, the DNS collected packets by Skydive will appear as additional data within the structure describing a specific connection in the UI.

Each flow has a field named DNS, which is set to &quot;nil&quot; by default unless the flow indeed holds DNS data, i.e., one of the parties is a DNS server (those are easily identified as they serve requests on port 53). For such flows, the value of the DNS field will not be &quot;nil,&quot; but rather an array holding all the packets that were sent over the connection. Each of the packets may have any of the above fields:

- ID – A unique ID for the specific DNS query
- QR – Query/Response, 0/1
- Opcode – DNS packet type. Can be any of QUERY, IQUERY, STATUS, Notify, Update
- AA – Specifies whether the responding name server is an authority for the domain name in question
- TC – Indicates that only the first 512 bytes of the reply was returned
- RD – Recursion desired
- RA – Recursion available
- Z – Reserved
- AD - Indicates in a response that all data included in the answer and authority sections of the response have been authenticated by the server according to the policies of that server
- Rcode – Response code
- QDCount – Total number of questions in the query
- ANCount – Total number of answers received
- NSCount – Total number of items in the Authorities section
- ARCount – Total number of items in the Additionals section
- questions - A list of zero or more _Query_ structures.
- answers -A list of zero or more Answer _Resource Record_ structures.
- authorities - A list of zero or more Authority _Resource Record_ structures.
- additionals - A list of zero or more Additional _Resource Record_ structures.

The definition for each of the above fields is taken from [http://www.networksorcery.com/enp/protocol/dns.htm#QR](http://www.networksorcery.com/enp/protocol/dns.htm#QR). Please refer to this website for more information.

To recap, Skydive is capable of fully intercepting all DNS communication in its environment. However, it is worth mentioning that not all the flow probes that Skydive provides are capable of tracking DNS activity. To capture DNS traffic, the capture type should be PCAP.

# How to Use

To learn how to deploy Skydive and start a capture, please refer to skydive.network. You can find the specific instructions on starting new captures at [http://skydive.network/tutorials/first-steps-6.html](http://skydive.network/tutorials/first-steps-6.html). To start a new capture, follow the instructions available at this above link – the relevant command line would be:

skydive client capture create --gremlin &quot;G.V().Has(&#39;Name&#39;, &#39;eth0&#39;)&quot;

However, in order to make Skydive collect DNS data, the command line needs to be extended as follows:

skydive client capture create --gremlin &quot;G.V().Has(&#39;Name&#39;, &#39;eth0&#39;)&quot; –-extra-layer DNS

Once the capture has been activated, Skydive will start to collect DNS traffic for the target interface, and this data will be accessible from the UI.

<p>
  <a href="/assets/images/blog/dns-expanded.png" data-lightbox="WebUI-1" data-title="DNS Field Expanded">
    <center>
      <img src="/assets/images/blog/dns-expanded.png" width="500"/>
    </center>
  </a>
</p>

However, if the user wishes to take it one step further and log this DNS activity, Skydive provides this service as well. To that end, the user will need to run Skydive&#39;s storage process by navigating to its directory:

cd skydive/contrib/pipelines/ **secadvisor** /

Afterward, you should follow the instructions in the README.md file.

# Acknowledgments

We thank Andre Kassis for his valuable contribution.
