# BrainObjects … need Tracing and Logging

One of the key problems in deployments is to "debug" and "optimize" a running system. Real production deployments behave differently than development environments.

This is why "Tracing and Logging" must be a key component of BrainObjects.

Since it will be heavily used all over the place it must be highly optimized to ensure low overhead … especially in times when it is actively used.

Most deployed real life software systems also depend on domain specific persistent logging (e.g. user session creation and termination, security violations, loading of external data structures, up/down-loading of files, etc.). BrainObjects should supply the core infrastructure for making the development of domain specific loggers a trivial task.

Finally BrainObject's tracing and logging infrastructure should support logging channels compatible with Apple's os\_log_xxx and os\_activity_xxx APSs in order to make it usable by Apple's development and performance tools.

## Tracing Design Ideas

We have two main concepts:

- (Structured) Logging
- (Execution Span) Tracing


### Structured Logging

Logging of "events" is typically defined by:

- Log severity level … trace, debug, info, warn, error, fatal, etc.
- Log message text
- Automatically generated timestamp


Structured Logging is then adding:

- a dictionary with additional (optional) "key-value" annotations (tags?)
  - some of those "tags" could be used for filtering 
  
Our main design goals are:

- Allow customizing the logging rules during runtime
  - we need "dynamic" behavior instead of only "static" compile time instrumentation


### Tracing

Tracing is basically like structured logging, but …

- A trace has two timestamps: a "begin" and an "end"
- A trace know the ID of its `parentTraceSpanID`

> NOTE: We should try to support the full set of the [**Open Telemetry**](#OpenTelemetry) concepts (for details see below)


#### Main "tracing gotchas"

The main problem is … How do "hand down" the `parentTraceSpanID`?

A trivial solution would be: "Just do it by hand"

- (+) Pro
  - this is easy to implement. Simply put a `parentTraceSpanID` variable into every strict/class that should perform tracing and set it to the "correct" value when creating that objects.
- (-) Con
  - in multithreaded systems this can very easily product totally incorrect "traces" if people do not pay attanetion and fully understand what they are doing.
  
  

### LoggingFilters vs LoggingChannels vs … ?

The idea so far is that:

- `LoggingFilters`
  - are stateless .. and (therefore) thread safe
  - know the filter rules
    - and have a highly optimized way of applying (the most common) filters
  - decide if a "LogItem/TraceItem" is processed
    - and to which LoggingChannels or (parent? upstream?) LoggingFilter(s) it is passed along

- `LoggingChannel`
  - ensures proper serialization of `LogEvent` output to a single (storage)"stream"
  - have state (e.g. a ring buffer for incoming log events)
  - are thread safe

What else do we need?

- `LogItem` ? `LogEvent` ? `LogEntry` ?
  - the "immutable" payload of a log event


## Ressources

Here are some additional pointers to other projects which might be helpful for some of the "Tracing and Logging" idea.


### Related and interesting "Swift frameworks"


#### os_signpost … in macOS

- Documentation: [https://developer.apple.com/documentation/os/logging?language=objc](https://developer.apple.com/documentation/os/logging?language=objc)
- Design:
  - low overhead
- Articles:
  - ["Measuring performance with os_signpost"](https://www.donnywals.com/measuring-performance-with-os_signpost/), 2019-12


#### Logging … in SwiftNIO

- Source code: ?
- Design
  - ?
- (+) Pro
- (?) Unclear
  - ?
- (-) Con


#### Wayfair "Jaeger" … Open Tracing for iOS (Swift)

There is a Swift implementation to support "Open Tracing"

- Code: [https://github.com/wayfair/jaeger-swift/tree/master/Example/jaegerMediator](https://github.com/wayfair/jaeger-swift/tree/master/Example/jaegerMediator)
- Articles:
  - [https://tech.wayfair.com/2019/05/open-tracing-for-ios-a-performance-tool-for-everyone/](https://tech.wayfair.com/2019/05/open-tracing-for-ios-a-performance-tool-for-everyone/), 2019-05
- (?) Unclear
  - it seems like "parentSpanIPs" need to be propagated by hand
  
  

### Related frameworks in other programming language ecosystems

#### Tracing … in Rust

… this is one of the (default?) tracing subsystems for Rust processes. It originated within the "Tokio" async-await runtime crate.

- [https://docs.rs/tracing](https://docs.rs/tracing)
- Source code: [https://github.com/tokio-rs/tracing](https://github.com/tokio-rs/tracing)
- Design
  - uses "`subscribers` for emitting traces"
    - support e.g OpenTelemetry


#### Bunyan … Structured Logging in node.js (JavaScript)

… this seems to be one of the default logging subsystems for node.js server processes.

- [https://www.npmjs.com/package/bunyan](https://www.npmjs.com/package/bunyan)
- Artikles
  - [https://nodejs.org/en/blog/module/service-logging-in-json-with-bunyan/](https://nodejs.org/en/blog/module/service-logging-in-json-with-bunyan/)



### Tracing frameworks in operating systems

#### dtrace … in SmartOS, Solaris, macOS etc.

dtrace key comppnent of modern operating systems. It originated at Sun (for Solaris) and later was also integratd into macOS.

- Design
  - …
- (?) Unclear
  - How does it work exactly ?




### Related and interesting "cluster monitoring" technologies

#### Prometheus + Graphana (cluster monitoring + alerting + HTML dashboard)

- Home page: [https://prometheus.io](https://prometheus.io)
- (+) Pro
  - On of the (most?) common toolsets in Linux cluster monitoring
  - Many services offer "automated" exports of "metrics" following the Prometheus naming schema
- (?) Unclear
  - Maybe a "node_exporter" is a fairly trivial service to be a testbed for BrainObjects?


#### Jaeger (Open Tracing)

"open source, end-to-end distributed tracing"

- Home page: [https://www.jaegertracing.io](https://www.jaegertracing.io)
- Project was started at Uber


#### Open Tracing

"Vendor-neutral APIs and instrumentation for distributed tracing"

(!) NOTE: Open Tracing has "morphed" into … OpenTelemetry (see below)

- Home page: [https://opentracing.io](https://opentracing.io)
- [https://opentracing.io/docs/supported-tracers/](https://opentracing.io/docs/supported-tracers/)
- (+) interesting list of related projects (including trace visualizing)
- Design
  - [https://opentracing.io/specification/](https://opentracing.io/specification/)
    - explains the key concepts of a tracing `Span`



#### OpenTelemetry <a name="OpenTelemetry"></a>

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


### Apple WWDC sessions about os\_log and os\_activity
- WWDC 2014
	- #714 Fix bugs faster using activity tracing
- WWDC 2016
	- #721 Unified logging and activity tracing
- WWDC 2018
	- #405 Measuring performance using logging
	- #410 Creating custom instruments


…
 


