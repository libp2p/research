# Routing at Scale (1M, 10M, 100M, 1B.. nodes)

## Short Description
> In one sentence or paragraph.

Routing in content-oriented or content-centric networks means that any content published in this network needs to a routable address. While traditionally the number of routable addresses needed (i.e., IP addresses) was bounded by the number of physical machines/end-notes, switching to addressable content means that the number of routable addresses is automatically exploding to the number of different content items (i.e., any file) that is published in the Internet.

Therefore, content-addressable networks face the challenge of routing scalability, as the amount of addressable elements in the network rises by several orders of magnitude compared to the host-addressable Internet of today.

## Long Description

Routing in content-oriented and content-centric networks has traditionally been a painful open issue. Following the conventions of the Internet Protocol to store the pre-computed routing/forwarding state for all prefixes at the network nodes raises scalability concerns in ICN (where content name prefixes need to be stored).

Attempting to address content directly means that routing tables have to keep state per content, as opposed to keeping state per end-host as is the case today. Furthermore, in a native network-layer addressable network, routing will likely have to be done *per content chunk*, where each chunk is similar in size to an IP packet. Although the size of a content chunk has not been agreed, it is assumed that it is going to be in the order of a few KBs. This, in turn, means that routing nodes have to keep state per content packet, as opposed to end-host machine, which increases even more the size of the routing table.

Clearly, naming of content items plays an important role in content routing and therefore, the choice of naming convention is a key in addressing routing. Traditionally, there have been two approaches to content naming: i) routing on flat names, and ii) routing on hierarchical names.

Each of those approaches to naming is defining the overall architecture. In case of routing on flat names, the resolution is done based on some Name Resolution System (such as the DHT in case of IPFS). The corresponding routing approach is generally called “name-resolution-based routing” and always involves talking to a higher-layer entity. In contrast, in case of hierarchical names, every routing node is identifying the next-hop node through pointers in the FIB table. This routing approach is generally called “name-based routing”, given that there is hop-by-hop routing on every network node.

In all cases, there are two fundamental steps to resolve content: i) content to location mapping, and ii) build the path to get to that location. This is what defines the architecture and those two need to be designed in a way that is scalable and also efficient in terms of performance. It has been clear from the growth and dominance of CDNs and their extensive localised caching techniques that resolution of content from stored copies needs to be topologically embedded, that is, try to find and resolve local content first before getting to copies that are (topologically) far away.


## State of the Art

> This survey on the State of the Art is not by any means complete, however, it should provide a good entry point to learn what are the existing work. If you have something that is fundamentally missing, please consider submitting a PR to augment this survey. 

### Within the libp2p Ecosystem
> Existing attempts and strategies

In the wider IPFS, libp2p and IPLD systems, each of the components is taking care of a separate part of the architecture. In particular:

- IPLD: (largely) takes care of naming content items
- IPFS: takes care of the “content to location mapping” (i.e., CID to peerID) through DHT
- libp2p: takes care of building the path from peerID to the IP address of the node (using protocols such as pubsub/gossipsub and bitswap).

The current version of the system is using a single DHT for all content published in IPFS and even within individual IPFS Clusters. Although this has been working fine up until now, it is expected to face scalability problems as the system is scaling up and attracting more users and orders of magnitude more traffic. Furthermore, as a system that is expected to provide timely delivery of content to users, IPFS needs to take into account topological characteristics of stored content and resolve the closest possible copies. Closest can be defined both as number of network hops, but also as a round-trip delay.

### Within the broad Research Ecosystem
> How do people try to solve this problem?

In parallel to the initial development of IPFS and libp2p, there has been a parallel stream of work mainly driven by the academic and research community to shift the focus from host-based networking to content- or information-centric networking (CCN/ICN). Most of the work kicked off around 2006, mainly from Van Jacobson and in the form of talks - see [1] for a very inspiring talk by Van Jacobson. This later materialised in several papers and is still going on today under the umbrella of the Named-Data Networking project (see below).

A number of projects have since emerged (in the US, Europe and Asia) to investigate the properties of a content-centric network, where content itself is the addressable and routable primitive and to build a scalable, efficient and secure Future Internet Architecture. The work has (mostly) assumed that this future Internet architecture will build on top of IP and will involve in-network routing and forwarding entities (i.e., network routers), which will be able to “speak” ICN language and related protocols.

1) Named Data Networking (NDN)

NDN is a US-driven project, which however is attracting attention worldwide. NDN follows a “name-based routing” approach, where names are hierarchical and routers do longest-prefix matching to find the next node to forward the request to. This is the most radical of the ICN architectures, which assumes that routers in the network (or at least some of them) understand the NDN stack and support in-network content caching. Although it comes with several challenges (e.g., scaling the routing table size), its expected benefits are significant, being able to natively support client mobility, in-network caching and multicast. 

Material: 
- Website (includes a ton of material): named-data.net 
- Original 2009 paper: https://named-data.net/publications/networkingnamedcontent/ 
- Follow up 2014 paper: https://named-data.net/publications/named_data_networking_ccr/ 

2) DONA: Data-Oriented (and beyond) Network Architecture

DONA is a 2007 ACM SIGCOMM paper, which advocates a hierarchical data resolution structure, where there is at least one “resolution handler” in each ISP domain. Names are structured according to the form P:L, where P is the cryptographic hash of the principal’s (publisher’s) public key and L is a label given by the principal/publisher which needs to ensure that names are unique. Given the hierarchical structure of the architecture, a challenge for DONA is the stress on the top-level resolution-handler (assuming we’re addressing the entire Internet). 

Material: 

- Paper: http://ccr.sigcomm.org/online/files/fp177-koponen1.pdf  

3) NetInf: Network of Information

NetInf is the outcome of an EU project (which ended around 2015 or so) which resembles a lot the IPFS structure. NetInf is building on name-resolution-based routing (through a Name-Resolution System - NRS). The NRS is a multi-level DHT and results have reported that the architecture can scale to several millions of nodes with lookup latencies of less than 100ms. 

Material:
- Overall Architecture paper: https://www.sciencedirect.com/science/article/pii/S0140366413000364 
- Measurement/Simulation study: https://www.sciencedirect.com/science/article/pii/S0140366413000418 

[1] Van Jacobson’s 2006 Google Tech Talk - A New Way to Look at Networking: https://www.youtube.com/watch?v=gqGEMQveoqg

Surveys on the topic

- A Survey of Information-Centric Networking Research (2013), https://ieeexplore.ieee.org/abstract/document/6563278
- A Survey of Information-Centric Networking (2012), https://ieeexplore.ieee.org/abstract/document/6231276

### Known shortcommins of existing solutions
> What are the limitations on those solutions?

All of the above-mentioned approaches are facing scalability issues. This is primarily due to the vast amount of content items that need to be reachable. At the same time, we should not forget that most of the research proposals are targeting the wider Internet as a future network architecture, which is expected to gradually replace host-based networking. There have been several proposals to scale those systems, such as for instance:

- On Demand Routing for Scalable Name-Based Forwarding, https://conferences.sigcomm.org/acm-icn/2018/proceedings/icn18-final53.pdf
- A Note on Routing Scalability in Named Data Networking, https://named-data.net/wp-content/uploads/2019/08/zhang2019a-note.pdf

## Solving this Open Problem

In terms of scalability, a Multi-Layer DHT should be constructed, which should ideally be topologically embedded, meaning that the nodes participating in one DHT should be geographically and (therefore) topologically close to each other.

In addition to the name-resolution based system currently deployed (through the DHT system), we should involve an element of name-based routing (where possible) in order to avoid hitting the DHT with every request. Recall that DHTs are by design stochastically suboptimal, as the overlay structure does not necessarily represent the network topology (i.e., one hop on the DHT could translate to tens of network-layer router hops) and therefore delivery delay increases.

A very recent related paper discussing those issues is here: https://conferences.sigcomm.org/acm-icn/2019/proceedings/icn19-34.pdf

### What is the impact

Solving routing scalability for IPFS and libp2p is of utmost importance. As the network grows and more traffic is inserted in the system, a single DHT is unlikely to perform according to expectations.

### What defines a complete solution?
> What hard constraints should it obey? Are there additional soft constraints that a solution would ideally obey?

A complete solution here should involve the following elements:

- A Multi-Layer DHT, whose separate components are *topologically embedded* in the underlying topology
- A *name-based routing* element in addition to the existing name-resolution-based system (i.e., DHT). This could be an extension of the IPFS gateway, or a proposal for a separate network entity.

## Other

### Existing Conversations/Threads

https://github.com/ipfs/notes/issues/291
https://github.com/ipfs/notes/issues/198
https://github.com/ipfs/notes/issues/162
https://github.com/ipfs/notes/issues/15
https://github.com/libp2p/notes/issues/10
https://github.com/libp2p/notes/issues/3
https://github.com/libp2p/research-dht/issues/6

### Extra notes
