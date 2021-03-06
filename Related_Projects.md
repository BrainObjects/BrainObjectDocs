# BrainObjects … and related Projects



## SwiftNIO relationship

Is there something that we can learn or "copy" from SwiftNIO?

The SwiftNIO framework basically targets:

 - a modern access to sockets … and lot level byte transport protocols

As the core design building blocks it is (seems to be?) providing:

 - Promise + Future … concepts for async data processing
 - Handlers … as building blocks to process data
 - Channels + Queues … in order to 
	- define entry points into the system
	- define handler pipelines to process the data


### What could BrainObjects reuse from or how to integrate with SwiftNIO?


Should we call it BraiNIO ? :-)

There seem to be a lot of similar concepts in SwiftNIO.

- BraionObjects might need to provide "subclasses" of key SWiftNIO objects in or to introduce the "instrumentation" capabilities for the NIO Pipelines
- Maybe the SwiftNIO APIs for channels and pipelines are good … so BrainObjects could reuse the APIs?

There are some projects in the context of the SwiftServer group which might be interesting to follow:

- Logging
  - [https://forums.swift.org/t/feedback-server-logging-api-with-revisions/19375](https://forums.swift.org/t/feedback-server-logging-api-with-revisions/19375)
- Metrics
  - [https://forums.swift.org/t/metrics/19353](https://forums.swift.org/t/metrics/19353)
  - there is a Prometheus "exporter"
    - (-) but the code looks a little strange (no support for multiple threads)
    - Prometheus is moving to … OpenMetrics ([https://openmetrics.io](https://openmetrics.io))
      - ? but that project shows very little activity in 2019?





## Ressources)

Here are some additional pointers to other projects which might be helpful for some of the BrainObjects ideas.


### Related and interesting "Swift frameworks"


#### Alamofire (server framework)

- Source code: [https://github.com/Alamofire/Alamofire](https://github.com/Alamofire/Alamofire)
- Design
  - "Alamofire is an HTTP networking library written in Swift"
- (+) Pro
- (?) Unclear
  - ?Tomi thinks this is somewhat like CococaHTTPServer

- (-) Con



#### Perfect (server framework ecosystem)

- Home page: [https://www.perfect.org](https://www.perfect.org)
- Source code: [https://github.com/PerfectlySoft/Perfect](https://github.com/PerfectlySoft/Perfect)
- Design
  "Is a web server toolkit to build applications and other REST services in Swift"
- (+) Pro
  - rich ecosystem with other "frameworks"
    - IO adaptors (http/1, cURL, WebSockets, Apache FastCGI)
    - database adaptors (PostgreSQL, Redis, MongoDB, Sqlite, MySQL, CouchDB)
    - encoding/decoding (xml, zip)
    - services (Apple push notifications, SMTP, OAuth2)
    - content generation (Mustache templates, Markdown)
    - monitoring (New Relic?)
    - low level support (logging, remote logging, cron timers, …)
  - gets tested on Ubuntu Linux too
  - lead developer is an old-time macOS person
    - uses Swift + Perfect on embedded systems too
- (?) Unclear
  - Support for SwiftNIO seem work in progress (2019.02)
  - "New Relic" Monitoring
    - [https://newrelic.com](https://newrelic.com) .. seems like a commercial product
- (-) Con


#### Kitura (server framework ecosystem)

- Home page: [https://www.kitura.io](https://www.kitura.io)
- Source code: [https://github.com/IBM-Swift](https://github.com/IBM-Swift)
- Design
  - ecosystem
    - IO adaptors (socket, TLS)
    - database adaptors (Redis)
  - related (IBM?) projects
    - DB access … via SwiftKuery (which "is a pluggable SQL database driver/SDK abstraction layer") … [https://github.com/IBM-Swift/Swift-Kuery](https://github.com/IBM-Swift/Swift-Kuery)
      - for details see: [https://developer.ibm.com/swift/2018/12/18/announcing-swiftkuery-3-0/](https://developer.ibm.com/swift/2018/12/18/announcing-swiftkuery-3-0/)
      - ? … is this some kind of EOF idea?
      - Tomi does NOT like the "async" call design
    - Session management … via Kitura-Session … [https://github.com/IBM-Swift/Kitura-Session](https://github.com/IBM-Swift/Kitura-Session) 
    - [JSON Web Tokens](https://jwt.io/introduction/) data exchange … via SwiftJWT … [https://github.com/IBM-Swift/Swift-JWT](https://github.com/IBM-Swift/Swift-JWT)
- (+) Pro
- (?) Unclear
  - Originated at IBM .. is that a good or bad sign?
  - What does it do? Is is really just "routing" URIs from HTTP requests to functions?
  - KituraNIO … is still work in progress (2019.02)
- (-) Con



#### MicroExpress (framework)

- Home page: [https://github.com/NozeIO/MicroExpress](https://github.com/NozeIO/MicroExpress)
- Design
  - Was originally based on "noze.io" but is now sitting on top of SwiftNIO   
- (+) Pro
  - comes with Xcode templates
- (?) Unclear
- (-) Con


#### Noze.io (framework)

- Home page: [http://noze.io](http://noze.io)
- Design
    - tries to replicated to node.js concept with non-blocking IO for HTTP event processing
- (+) Pro
  - very compact
- (?) Unclear
- (-) Con
  - Tomi does NOT like the node.js coding style
  - not based of SwiftNIO … and therefore might not get any more real updates


#### SwiftNIO (IO framework)

- Home page: [https://swift.org/server/](https://swift.org/server/)
- Source code: [https://github.com/apple/swift-nio](https://github.com/apple/swift-nio)
- Community: [https://forums.swift.org/c/server](https://forums.swift.org/c/server)
- Design
  - "SwiftNIO is a cross-platform asynchronous event-driven network application framework for rapid development of maintainable high performance protocol servers & clients"
  - A simple guide to async on the server: [https://www.raywenderlich.com/1124580-swiftnio-a-simple-guide-to-async-on-the-server](https://www.raywenderlich.com/1124580-swiftnio-a-simple-guide-to-async-on-the-server)
- (+) Pro
  - Designed to handle streams
  - Everybody seems to be going there
  - Supported by Apple
- (?) Unclear
- (-) Con


#### SwiftEngine (server framework)

- Home page: [https://swiftengine.io](https://swiftengine.io)
- Source code: [https://github.com/swiftengine](https://github.com/swiftengine)
- Design
  - "How to make server side Swift as easy to use as PHP?"
  - a "server less" system with an HTML UI, which allows "uploading" Swift functions (code)
- (+) Pro
  - is based on SwiftNIO
- (?) Unclear
  - There is an "automatic routing" system … how do request find the procesing module (function)
    - … for details read: [https://github.com/swiftengine/SwiftEngine/blob/master/TechnicalOverview.md](https://github.com/swiftengine/SwiftEngine/blob/master/TechnicalOverview.md) 
- (-) Con
  - Seems to have a very very limited feature set (scope)
  

#### Vapor (server framework ecosystem)

- Home page (and code): [https://github.com/vapor](https://github.com/vapor)
- API documentation: [https://api.vapor.codes](https://api.vapor.codes)
- Design (based on Vapor 3)
  - Talk on Vapor 3 at SwiftServerConf: [https://www.youtube.com/watch?v=_615REC32Ts](https://www.youtube.com/watch?v=_615REC32Ts)
- (+) Pro
  - based on SwiftNIO since Vapor v3
  - rich ecosystem with other "frameworks"
    - database adaptors (postgres, redis, …?) .. are based on a async API
- (?) Unclear
  - seems to be supported by at least on (big?) software house .. Nodes
  - developer teams looks very young .. and is learning via Vapor
  	- Vapor 3 is a complete rewrite … which can be a good thing
- (-) Con


#### Book, articles, tutorials, reports or benchmarks

- www.raywenderlich.com
-  they have several books, and (video tutorials). 
-  As of 25.02.2019 they started a special section on server-side Swift: https://www.raywenderlich.com/1481430-introducing-the-server-side-swift-celebration and Georg probably will subscribe...
  - 2019.02 … [https://www.raywenderlich.com/1481430-introducing-the-server-side-swift-celebration](https://www.raywenderlich.com/1481430-introducing-the-server-side-swift-celebration)
  - They also have few tutorials on Vapor:
    - On UI template engine Leaf: https://www.raywenderlich.com/1002448-templating-vapor-applications-with-leaf and https://www.raywenderlich.com/1004734-beautifying-templated-websites-with-leaf-and-bootstrap
    - Testing: https://www.raywenderlich.com/1002044-testing-in-vapor

- Paul Hudson at https://www.hackingwithswift.com has books on Kitura and Vapor




### Related and interesting "data processing" technologies

      
#### Apache Spark (analytics engine, cluster computing)

- Home page: [https://spark.apache.org](https://spark.apache.org)
- On WikiPedia: [https://en.wikipedia.org/wiki/Apache_Spark](https://en.wikipedia.org/wiki/Apache_Spark)
  - "Spark is an open-source distributed general-purpose cluster-computing framework. … for programming … clusters with implicit data parallelism and fault tolerance."
- Slogan: "Apache Spark is a unified analytics engine for large-scale data processing."
- Design
  - A distributed "job processing" engine for … "map reduce, streaming, interactive queries, and machine learning"
  - ? It looks like it is targeted at analytics and (big data) batch processing
- (+) Pro
- (?) Unclear
- (-) Con


#### Apache Hive (analytics engine, cluster computing)

- Home page: ?
- Source: [https://github.com/apache/hive](https://github.com/apache/hive)
- On WikiPedia: [https://en.wikipedia.org/wiki/Apache_Hive](https://en.wikipedia.org/wiki/Apache_Hive)
- Design
  - written by Facebook to process data from Hadoop clusters
  - was the precursor to "Presto"
- (+) Pro
- (?) Unclear
- (-) Con
  - Written in Java
  
  
#### Apache OpenWhisk ("serverless" data processing pipeline)

- Home page: [http://openwhisk.incubator.apache.org](http://openwhisk.incubator.apache.org)
- Design
  - Event/Message passing system that will execute custom "actions" (=functions) .. which can be written in many different languages (including Swift 3 and 4)
  - Events can be generated by (watching ?) databases
- (+) Pro
  - Native Swift support
- (?) Unclear
  - Is this a "big distributed UNIX pipe" system .. where "binaries" are functions?
  - How does "watching" a database work .. is that a push or a pull?


#### Presto (analytics engine, cluster computing)

- Home page: [https://prestodb.github.io](https://prestodb.github.io)
- On WikiPedia: [https://en.wikipedia.org/wiki/Presto_(SQL_query_engine)](https://en.wikipedia.org/wiki/Presto_(SQL_query_engine))
  - "Presto is a high performance, distributed SQL query engine for big data. … One can even query data from multiple data sources within a single query."
- Slogan: ""
- Design
  - written by Facebook to replace the "Apache Hive" engine
- (+) Pro
  - can read directly from HDFS .. so less "overhead" 
- (?) Unclear
- (-) Con
  - Written in Java



### Related and interesting "cluster monitoring" technologies

#### Prometheus + Graphana (cluster monitoring + alerting + HTML dashboard)

- Home page: [https://prometheus.io](https://prometheus.io)
- (+) Pro
  - On of the (most?) common toolsets in Linux cluster monitoring
  - Many services offer "automated" exports of "metrics" following the Prometheus naming schema
- (?) Unclear
  - Maybe a "node_exporter" is a fairly trivial service to be a testbed for BrainObjects?


#### OpenTelemetry

… is a "standard" for tracing distributed systems during deployment.

- Home page: [https://opentelemetry.io](https://opentelemetry.io)
- Presentation at FOSDEM 2020 … [https://fosdem.org/2020/schedule/event/beam_opentelemetry_xkcd_927_success_story/](https://fosdem.org/2020/schedule/event/beam_opentelemetry_xkcd_927_success_story/)
- Design
  - seems to have originated in the Erlang community
  - based on context tracing .. looks very similar to what Apple is doing for GCD debugging
- (+) Pro
  - there are (will be) viewers for this type of data strucuture


### Related and interesting "communication monitoring" technologies


#### Matrix.org (… has a visual cluster debugger)

Matrix is a "realtime chat client" protocol.

- The "chat" system comes with a test system where communication networks can be visually created … and the message flow can be "watched in slow motion" for debugging purposes"
- [https://youtu.be/DZBvy4abB1o](https://youtu.be/DZBvy4abB1o) .. is a video where one can see the feature
- [the related page for the Matrix event](https://matrix.org/blog/2019/02/04/matrix-at-fosdem-2019/)






### Related and interesting "(communication) transport" technologies

In order to test BrainObjects (and ensure that it is not too focused around HTTP) we might take a look at some other transport technologies.


#### CoAP - Constrained Application Protocol

… is a low overhead binary replacement for HTTP for low power IoT hardware.

- Home page: 
- Specification: [https://tools.ietf.org/html/rfc7252](https://tools.ietf.org/html/rfc7252)
- (+) Pro
  - seems fairly easy to implement 
- (?) Unclear
  - so farbased on UDP .. but a TCP version should also be specified soon: [https://tools.ietf.org/html/rfc8323#section-9](https://tools.ietf.org/html/rfc8323#section-9)
  
  

#### DDS - Data Distribution Services

… is a low level transport protocol for robotics which is pushed by the OMG.

- Home page: [https://www.dds-foundation.org](https://www.dds-foundation.org)
- Design:
  - based on development (and patents) from RTI [https://www.rti.com](https://www.rti.com)
  - … but OMG ensured that the patents got handed over to the DDS Foundation
- (+) Pro
  - has a focus on high reliability and low latency (because of robots)
  - has a well defined set of "Quality of Services" classes for timeout, failure etc handling
- (-) Con
  - very complicated to implement
  


#### JSON-RPC over WebSockets

WebSockets is a common protocol in the JavaScript Web Development world. It basically is a well defined way on how to upgrade a temporary HTTP connection into a persistent bidirectional TCP(TLS) socket. This then can be used to exchange "RPC" messages encoded in JSON.

- Home page:
  - ?
  - [https://www.jsonrpc.org](https://www.jsonrpc.org)
- Wikipedia:
  - [https://en.wikipedia.org/wiki/WebSocket](https://en.wikipedia.org/wiki/WebSocket)
  - [https://en.wikipedia.org/wiki/JSON-RPC](https://en.wikipedia.org/wiki/JSON-RPC)



#### MQTT

… is a simple binary protocol from IBM for "Message Queuing Telemetry Transport"

- Home page: [https://mqtt.org](https://mqtt.org)
- Wikipedia: [https://en.wikipedia.org/wiki/MQTT](https://en.wikipedia.org/wiki/MQTT)
- Design:
  - low overhead binary protocol
  - payload agnostic
  - focus on "message passing" .. in the IoT sensor context




#### XMPP

… is a XML over TCP sockets protocol used and invented for the Jabber chat system. Today it is also used as a foundation for the ETSI "OneM2M" IoT standard.

- Home page: [https://xmpp.org](https://xmpp.org)
- Wikipedia: [https://en.wikipedia.org/wiki/XMPP](https://en.wikipedia.org/wiki/XMPP)
- (+) Pro
  - widely used
  - fairly easy to implement


  
### Related and interesting "whatever" technologies


#### Redshift

- is what ?? seems similar to Presto?



### Books and tutorials … which are not related to any of the categories above 

…
 


