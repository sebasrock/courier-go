<!-- Code generated by gomarkdoc. DO NOT EDIT -->

# otelcourier

```go
import "github.com/gojek/courier-go/otelcourier"
```

Package otelcourier instruments the github\.com/gojek/courier\-go package\.

## Index

- [Constants](<#constants>)
- [func DisableCallbackTracing(opts *traceOptions)](<#func-disablecallbacktracing>)
- [func DisablePublisherTracing(opts *traceOptions)](<#func-disablepublishertracing>)
- [func DisableSubscriberTracing(opts *traceOptions)](<#func-disablesubscribertracing>)
- [func DisableUnsubscriberTracing(opts *traceOptions)](<#func-disableunsubscribertracing>)
- [type Option](<#type-option>)
  - [func WithTracerProvider(provider oteltrace.TracerProvider) Option](<#func-withtracerprovider>)
- [type Tracer](<#type-tracer>)
  - [func NewTracer(service string, opts ...Option) *Tracer](<#func-newtracer>)
  - [func (t *Tracer) ApplyTraceMiddlewares(c *courier.Client)](<#func-tracer-applytracemiddlewares>)


## Constants

```go
const (
    // MQTTTopic is the attribute key for tracing message topic
    MQTTTopic = attribute.Key("mqtt.topic")
    // MQTTQoS is the attribute key for tracing message qos
    MQTTQoS = attribute.Key("mqtt.qos")
    // MQTTTopicWithQoS is the attribute key for tracing message topic and qos together
    MQTTTopicWithQoS = attribute.Key("mqtt.topicwithqos")
    // MQTTRetained is the attribute key for tracing message retained flag
    MQTTRetained = attribute.Key("mqtt.retained")
)
```

## func [DisableCallbackTracing](<https://github.com/gojek/courier-go/blob/main/otelcourier/options.go#L43>)

```go
func DisableCallbackTracing(opts *traceOptions)
```

DisableCallbackTracing disables implicit tracing on subscription callbacks\.

## func [DisablePublisherTracing](<https://github.com/gojek/courier-go/blob/main/otelcourier/options.go#L48>)

```go
func DisablePublisherTracing(opts *traceOptions)
```

DisablePublisherTracing disables courier\.Publisher tracing\.

## func [DisableSubscriberTracing](<https://github.com/gojek/courier-go/blob/main/otelcourier/options.go#L53>)

```go
func DisableSubscriberTracing(opts *traceOptions)
```

DisableSubscriberTracing disables courier\.Subscriber tracing\.

## func [DisableUnsubscriberTracing](<https://github.com/gojek/courier-go/blob/main/otelcourier/options.go#L58>)

```go
func DisableUnsubscriberTracing(opts *traceOptions)
```

DisableUnsubscriberTracing disables courier\.Unsubscriber tracing\.

## type [Option](<https://github.com/gojek/courier-go/blob/main/otelcourier/options.go#L25>)

Option helps configure trace options\.

```go
type Option func(*traceOptions)
```

### func [WithTracerProvider](<https://github.com/gojek/courier-go/blob/main/otelcourier/options.go#L36>)

```go
func WithTracerProvider(provider oteltrace.TracerProvider) Option
```

WithTracerProvider specifies a tracer provider to use for creating a tracer\. If none is specified\, the global provider is used\.

## type [Tracer](<https://github.com/gojek/courier-go/blob/main/otelcourier/trace.go#L14-L18>)

Tracer implements tracing abilities using OpenTelemetry SDK\.

```go
type Tracer struct {
    // contains filtered or unexported fields
}
```

### func [NewTracer](<https://github.com/gojek/courier-go/blob/main/otelcourier/trace.go#L21>)

```go
func NewTracer(service string, opts ...Option) *Tracer
```

NewTracer creates a new Tracer with Option\(s\)\.

<details><summary>Example</summary>
<p>

```go
package main

import (
	"context"
	courier "github.com/gojek/courier-go"
	"github.com/gojek/courier-go/otelcourier"
	"go.opentelemetry.io/otel"
	"go.opentelemetry.io/otel/sdk/trace"
	"os"
	"os/signal"
	"syscall"
)

func main() {
	tp := trace.NewTracerProvider()
	defer tp.Shutdown(context.Background())

	otel.SetTracerProvider(tp)

	c, _ := courier.NewClient()
	otelcourier.NewTracer("service-name").ApplyTraceMiddlewares(c)

	if err := c.Start(); err != nil {
		panic(err)
	}

	stopCh := make(chan os.Signal, 1)
	signal.Notify(stopCh, []os.Signal{os.Interrupt, syscall.SIGTERM}...)

	if err := c.Publish(
		context.Background(), "test-topic", "message", courier.QOSOne); err != nil {
		panic(err)
	}
	<-stopCh

	c.Stop()
}
```

</p>
</details>

### func \(\*Tracer\) [ApplyTraceMiddlewares](<https://github.com/gojek/courier-go/blob/main/otelcourier/trace.go#L41>)

```go
func (t *Tracer) ApplyTraceMiddlewares(c *courier.Client)
```

ApplyTraceMiddlewares will instrument all the operations of a courier\.Client instance



Generated by [gomarkdoc](<https://github.com/princjef/gomarkdoc>)