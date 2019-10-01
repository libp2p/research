# PubSub at Scale

## Short Description
> In one sentence or paragraph.

Publish/Subscribe (pubsub) messaging systems have been proposed and traditionally used to disintermediate senders and receivers of messages. That is, in pubsub systems, publishers of content do not send the published messages directly to one or a group of receivers, but instead *publishers are sending messages to a topic.* This enhances asynchronous communication and reduces tremendously network traffic and bandwidth-requirements.

## Long Description

Pubsub systems are a subgroup of broadcast trees, according to which, once messages are published, they are broadcast to all nodes in the tree. Although broadcast can ensure timely delivery of messages to all receipients, it causes severe stress to the system in terms of bandwidth needed to deliver the messages.

One of the main benefits of pubsub systems is that receivers of information (or subscribers) can individually pull information when they need to. This reduces a lot the strain put in the system to deliver messages as they are published. However, in case of large-scale systems, the overhead of publishing information to a topic can still overload the system.

## State of the Art

> This survey on the State of the Art is not by any means complete, however, it should provide a good entry point to learn what are the existing work. If you have something that is fundamentally missing, please consider submitting a PR to augment this survey. 

### Within the libp2p Ecosystem
> Existing attempts and strategies

##### libp2p pubsub (floodsub, gossipsub & episub)

- https://github.com/libp2p/specs/blob/master/pubsub/README.md
- https://github.com/libp2p/specs/blob/master/pubsub/gossipsub/README.md
- https://github.com/libp2p/specs/blob/master/pubsub/gossipsub/episub.md

<strong> Gossip Protocols </strong>

Gossipsub is a gossip-based pubsub system developed within libp2p. Gossipsub borrows concepts from related literature (see list below) and blends them together to produce an efficient pubsub protocol. Traditionally, the concept behind gossiping is to address the issue of load-balancing between all nodes forwarding messages in the system. According to gossiping, once a node receives a message, it does not broadcast the message directly to all nodes subscribed to some topic, but instead it is choosing a fraction of nodes (we can use *t* to denote the number of nodes chosen) to distribute the message. In turn, those *t* nodes are choosing a further *t* nodes to distribute the message further. Clearly, receiving the message more than once is perfectly possible in gossiping systems. If a node receives a message twice, it discards the second (and any subsequent) message it receives.

The intrinsic redundancy inserted in the system through gossiping is improving the resilience of the system. At the same time, in order to maintain redundancy, every node in the system has to keep membership information for the entire system. Although gossip-based protocols reduce the stress put in the system in terms of bandwidth and connectivity requirements, it clearly poses scalability concerns due to the state that all nodes need to keep.

In order to overcome those issues, several gossip systems implement what is called *partial views*, according to which the following strategies can be used to propagate messages throughout the network:

- <strong> Eager push: </strong> This is the traditional approach, where once nodes receive a message they forward the message (together with its payload) to a random set of *t* peers.
- <strong> Pull: </strong> Nodes interested in a set of topics periodically send request messages to random nodes to inquire about newly received messages. If queried nodes have updates in the topic specified by the subscriber node, they forward the message to this nodee.
- <strong> Lazy push: </strong> When a node (from within the *t* group of another node) receives a message, it forwards a message identifier (i.e., *not the payload of the message*) to a number of random peers. If those nodes have not already received the message, they send a subsequent pull request to get the full payload of the message.

*Tradeoffs*

As usual with most communication systems, there are tradeoffs in the design choices, which affect the performance and resilience of the system. Inserting more traffic in the system (e.g., *eager push*) increases bandwidth requirements, but achieve higher reliability and lower latency. On the other hand, more relaxed approaches (e.g., *lazy push*) reduce both bandwidth requirements, but also load on end nodes.

Related Literature

- Epidemic Broadcast Trees, 2007, DOI: 10.1109/SRDS.2007.27, http://www.gsd.inesc-id.pt/~ler/docencia/rcs1617/papers/srds07.pdf
- HyParView: a membership protocol for reliable gossip-based broadcast, 2007, DOI: 10.1109/DSN.2007.56, http://asc.di.fct.unl.pt/~jleitao/pdf/dsn07-leitao.pdf
- GoCast: Gossip-enhanced Overlay Multicast for Fast and Dependable Group Communication, 2005, http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.75.4811&rep=rep1&type=pdf
- Exposing and eliminating vulnerabilities to denial of service attacks in secure gossip-based multicast, DSN 2004
- Emergent structure in unstructured epidemic multicast, DSN 2007


##### peer-base collaboration messaging protocol (Dias Peer Set)

- https://github.com/peer-base/peer-base/blob/master/docs/PROTOCOL.md#peer-base---protocol-explanation

### Within the broad Research Ecosystem
> How do people try to solve this problem?

- We've collected a vast amount of Research Material around PubSub. It can be found at https://github.com/libp2p/research-pubsub

Related literature in the broader research community has worked towards two incarnations of pubsub systems: i) content-based pubsub and ii) topic-based pubsub.

<strong> Content-Based Pubsub </strong>

In content-based pubsub, newly published content is tagged with a set of attribute/value pairs. In turn, subscriptions are expressed as predicates of the attributes. When predicates match attributes, the subscriber receives the information.

Related literature

- Pietzuch, P., Bacon, J.: Hermes: a distributed event-based middleware architecture. In: ICDCS Workshops. pp. 611–618 (2002)
- Rosenblum, D.S., Wolf, A.L.: A design framework for internet-scale event observation and notification. In: ESEC. pp. 344–360 (1997)
- Ahmed, N., Linderman, M., Bryant, J.: Papas: Peer assisted publish and subscribe. In: Workshop on Middleware for Next Generation Internet Computing (MW4NG). pp. 7:1–7:6 (2012)
- Li, M., Ye, F., Kim, M., Chen, H., Lei, H.: A scalable and elastic publish/subscribe service. In: IPDPS. pp. 1254–1265 (2011)
- Zhang, B., Jin, B., Chen, H., Qin, Z.: Empirical evaluation of contentbased pub/sub systems over cloud infrastructure. In: Intl. Conference on Embedded and Ubiquitous Computing (EUC). pp. 81–88 (2010)

<strong> Topic-Based Pubsub </strong>

In contrast, in topic-based pubsub systems, subscribers declare interest in some topic. Publishers add/tag topics to their publication and those topics that match subscribers' interests are broadcast to them. Depending on the nature of the application running on top, topic-based pubsub can reduce the complexity of the system.

Related Literature

- Eugster, P.T., Felber, P.A., Guerraoui, R., Kermarrec, A.M.: The many faces of publish/subscribe. ACM Comput. Surv. 35(2), 114–131 (2003)
- Zhao, Y., Kim, K., Venkatasubramanian, N.: Dynatops: A dynamic topicbased publish/subscribe architecture. In: DEBS. pp. 75–86 (2013)
- Julien Gascon-Samson, Franz-Philippe Garcia, Bettina Kemme, Jörg Kienzle, Dynamoth: A Scalable Pub/Sub Middleware for Latency-Constrained Applications in the Cloud, IEEE ICDCS 2015


### Known shortcommins of existing solutions
> What are the limitations on those solutions?

## Solving this Open Problem

As mentioned earlier, there are several tradeoffs at play in the design of the system. Those tradeoffs are made more serious as scalability requirements come into the picture, that is, as the protocols is requested to serve orders of magnitude more users and more pubsub topics. Below, we provide a very brief description of the main issues that a sophisticated pubsub protocol needs to be able to deal with.

- *Load-balancing:* Keeping membership state and forwarding pubsub messages is loading both the memory and communication/networking requirements of a node. This is especially so for p2p systems, where end-nodes are not necessarily powerful servers. Furthermore, as some content is becoming popular, more load is put on the nodes that are relaying those messages. *A sophisticated (gossiping) pubsub protocol needs to be able to balance load among nodes.*

- *Latency:* Some applications require that messages are delivered to all nodes subscribed to a topic with the least possible delay. As pubsub systems are built as overlays on top of the physical Internet infrastructure, the underlying hop-count does not necessarily correspond to the overlay picture. Furthermore, approaches such as "eager-push" or "flooding" can reduce the delivery latency, but increase bandwidth requirements.

- *Authentication:* Whether a pubsub system is open to the public or not, there needs to be some authentication to those that publish to specific topics/channels. As such, there has been discussion (e.g., in https://github.com/ipfs/notes/issues/236) about a pubsub authentication API. According to this, every topic is signed by a public key. Anyone can subscribe to this key, but those that want to publish information to this key/topic need to sign the content with the corresponding private key. In case of a private pubsub system, content can be encrypted and the corresponding keys to decrypt the content should be shared with those that are allowed access to the topics. *Content published in pubsub systems need to be authenticated and in case of a private pubsub system the content itself needs to be encrypted using authenticated encryption.*

- *Scalability:*

### What is the impact

As the IPFS network grows and dependency on underlying libp2p (and supporting protocols) intensifies, we need to make sure that the design of the protocols is able to scale up and maintain performance.

- Where is gossipsub used within libp2p/IPFS?
- Which external systems use gossipsub?

### What defines a complete solution?
> What hard constraints should it obey? Are there additional soft constraints that a solution would ideally obey?

Support for multiple times of usage:
- Blog
- Social Network
- Chat
- Newschannel
- Video Broadcast

## Other

### Existing Conversations/Threads

- [Authenticated PubSub](https://github.com/ipfs/notes/issues/236)
- [pub/sub - publish / subscribe](https://github.com/ipfs/notes/issues/64)
- [Messaging on IPFS](https://github.com/ipfs/notes/issues/33)
- [PubSub notes from the IPFS Workshop - recursion and corecursion, PubSub API and Self Certified Streams](https://github.com/ipfs/notes/issues/154)
- [PulsarCast M.Sc Thesis - Scaling libp2p PubSub](https://github.com/ipfs/notes/issues/266)

### Extra notes
