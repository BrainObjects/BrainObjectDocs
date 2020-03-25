# BrainObjects … need Tracing and Logging

One of the key problems in deployments is to "debug" and "optimize" a running system. Real production deployments behave differently than development environments.

This is why "Tracing and Logging" must be a key component of BrainObjects.

Since it will be heavily used all over the place it must be highly optimized to ensure low overhead … especially in times when it is actively used.


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
  - ensures proper serialization of LogEvent output to a single (storage)"stream"
  - have state (e.g. a ring buffer for incoming log events)
  - are thread safe

What else do we need?

- `LogItem` ? `LogEvent` ? `LogEntry` ?
  - the "immutable" payload of a log event


## Ressources

Here are some additional pointers to other projects which might be helpful for some of the "Tracing and Logging" idea.


### Related and interesting "Swift frameworks"


#### os_signpost … in macOS

- Documentation:
- Design:
  - low overhead


#### Logging … in SwiftNIO

- Source code: ?
- Design
  - ?
- (+) Pro
- (?) Unclear
  - ?
- (-) Con



### Related frameworks in other programming language ecosystems

#### Tracing … in Rust

… this is one of the (default?) tracing subsystems for Rust processes. It originated within the "Tokio" async-await runtime crate.

- [https://docs.rs/tracing](https://docs.rs/tracing)
- Source code: [https://github.com/tokio-rs/tracing](https://github.com/tokio-rs/tracing)
- Design
  - uses "subscribers for emitting traces"
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







…
 

