---------------------------- 
### Chasing Down Performance Issues Using Distributed Tracing.

Distributed tracing is quickly becoming a must-have component in the tools that organizations use to monitor their complex, microservice-based architectures.

This Marekdown define the role of tracing in microservices and demonstrate how to instrument and monitor a spring Java Application using Jaeger, Uber Tracing and Monitoring Open Source Tool.

Jaeger also supports other client library like: Go, Node JS and Python.

--------------------
### Table of Contents 

- [The role of tracing in MicroServices ](#the-role-of-tracing-in-microservices)  
- [Glossary of Tracing Terms ](#glossary-of-tracing-terms)  
- [Jaeger](#jaeger)  
- [Jaeger Architecture](#jaeger-architecture)  
- [Spring Application Instumentation](#spring-application-instumentation)
- [Node JS Application Instumentation](#node-js-application-instumentation)
- [Dockerizing Jaeger](#dockerizing-jaeger)
- [Traces View](#traces-view)
- [Bugs and Feedback](#bugs-and-feedback)  
- [Sources](#sources)

----------------------------
### The role of tracing in MicroServices 

```bash 
External monitoring only tells you the overall response time and number of invocations - no insight into the individual operations
```

<img src="https://raw.githubusercontent.com/elghoujdami-labs/distributed-tracing/master/img/the-role-dt.png" width='600' >



----------------------------
### Glossary of Tracing Terms 

A Span represents a logical unit of work in the system that has an operation name, the start time of the operation, and the duration. Spans may be nested and ordered to model causal relationships. An RPC call is an example of a span.

A Trace is a data/execution path through the system, and can be thought of as a directed acyclic graph of spans.

<img src="https://raw.githubusercontent.com/elghoujdami-labs/distributed-tracing/master/img/span-tarce.png" width='400' >

----------------------------
### Jaeger

Jaeger, inspired by Dapper and OpenZipkin, is a distributed tracing system released as open source by Uber Technologies. It can be used for monitoring MicroServices-based architectures:

- Distributed context propagation
- Distributed transaction monitoring
- Root cause analysis
- Service dependency analysis
- Performance / latency optimization

---------------
### Jaeger Architecture 

<img src="http://jaeger.readthedocs.io/en/latest/images/architecture.png" width='600' >


----------------------------------------
### Spring Application Instumentation

- Run Jaegertracing via Docker

```bash
docker run -d -p5775:5775/udp -p6831:6831/udp -p6832:6832/udp \
  -p5778:5778 -p16686:16686 -p14268:14268 jaegertracing/all-in-one:latest
```

```bash
http://localhost:16686/
```

- Add a Gradle Dependencies

```bash
  compile group: 'io.opentracing.contrib', name: 'opentracing-spring-web-autoconfigure', version: '0.0.6'
  compile group: 'com.uber.jaeger', name: 'jaeger-core', version: '0.20.5'
  compile group: 'io.opentracing.brave', name: 'brave-opentracing', version: '0.20.0'

```

- SpringApplication.java 

```bash
@Bean
    public io.opentracing.Tracer jaegerTracer() {
        return new Configuration("Accessories-Application", new Configuration.SamplerConfiguration(ProbabilisticSampler.TYPE, 1),
                new Configuration.ReporterConfiguration())
                .getTracer();
    }
```

----------------------------------------
### Node JS Application Instumentation


- Installation

```bash
npm install --save jaeger-client
```

- Instrumentation 

```bash
var initTracer = require('jaeger-client').initTracer;

// See schema https://github.com/uber/jaeger-client-node/blob/master/src/configuration.js#L37
var config = {
  'serviceName': 'my-awesome-service'
};
var options = {
  'tags': {
    'my-awesome-service.version': '1.1.2'
  },
  'metrics': metrics,
  'logger': logger
};
var tracer = initTracer(config, options);
```
-----------------------
### Dockerizing Jaeger

- Ho to add Jaeger to your Docker-Compose 

```
    jaeger:
        image: jaegertracing/all-in-one
        ports:
            - "16686:16686"
            - "5778:5778"
            - "14268:14268"
            - "6831:6831/udp"
            - "6832:6832/udp"
            - "5775:5775/udp"
        networks:
            - cns-network
```

```bash
  your_service:
    image: your_service_image
    environment:
        JAEGER_SERVICE_NAME: you_service_name
        JAEGER_SAMPLER_TYPE:  probabilistic
        JAEGER_SAMPLER_PARAM:  1
        JAEGER_AGENT_HOST: jaeger
        JAEGER_AGENT_PORT: 5775
```

-----------------------
### Traces View

<img src="http://jaeger.readthedocs.io/en/latest/images/traces-ss.png" width='600' >


---------------------
### Trace Detail View

<img src="http://jaeger.readthedocs.io/en/latest/images/trace-detail-ss.png" width='600' >

----------------------------
### Bugs and Feedback

For bugs, questions and discussions please use the [Github Issues](https://github.com/cloud-native-stack/Distributed-Tracing/issues).


----------------------------
### Sources 

- https://eng.uber.com/distributed-tracing/ 
- http://jaeger.readthedocs.io/en/latest/ 
- https://medium.com/@YuriShkuro/take-opentracing-for-a-hotrod-ride-f6e3141f7941 
- https://github.com/uber/jaeger 
- http://microservices.io/patterns/observability/distributed-tracing.html
- https://github.com/uber/jaeger-client-java/tree/master/jaeger-core
