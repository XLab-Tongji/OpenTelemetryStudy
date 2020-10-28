### /backwards-compatability

分别包含了node8，node10，node12的向后兼容文件。每个版本的文件结构如下：

```
./
├── index.ts
└── package.json
```

### /benchmark

如何运行benchmark，以及运行的结果

### /doc

api文档及指导说明文档

```
./
├── batcher-api.md
└── development-guide.md
└── exporter-guide.md
└── plugin-guide.md
```

### /examples

这个目录包含了一些例子，告诉我们如何使用OpenTelemetry JS运行真实的应用程序。

| Name                                                         | Description                                                  | Complexity Level |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ---------------- |
| [basic-tracer-node](https://github.com/open-telemetry/opentelemetry-js/blob/master/examples/basic-tracer-node) | 在Node.js应用中跟踪的基本使用方法                            | Beginner         |
| [tracer-web](https://github.com/open-telemetry/opentelemetry-js/blob/master/examples/tracer-web) | 应用程序中跟踪的基本使用方法                                 | Beginner         |
| [prometheus](https://github.com/open-telemetry/opentelemetry-js/blob/master/examples/prometheus) | 普罗米修斯的基本Metric使用                                   | Beginner         |
| [http](https://github.com/open-telemetry/opentelemetry-js/blob/master/examples/http) | HTTP instrument自动收集跟踪数据，并将其导出到所选的后端      | Intermediate     |
| [https](https://github.com/open-telemetry/opentelemetry-js/blob/master/examples/https) | HTTPS instrument自动收集跟踪数据，并将其导出到所选的后端     | Intermediate     |
| [grpc](https://github.com/open-telemetry/opentelemetry-js/blob/master/examples/grpc) | gRPC instrument自动收集跟踪数据，并将其导出到所选的后端      | Intermediate     |
| [express](https://github.com/open-telemetry/opentelemetry-js/blob/master/examples/express) | Express Instrumentation 可自动收集跟踪数据并将其导出到所选的后端 | Intermediate     |
| [collector-exporter-node](https://github.com/open-telemetry/opentelemetry-js/blob/master/examples/collector-exporter-node) | 如何使用@opentelemetry/exporter-collector来工具一个简单的Node.js应用 | Intermediate     |
| [opentracing-shim](https://github.com/open-telemetry/opentelemetry-js/blob/master/examples/opentracing-shim) | 如何将现有的OpenTracing仪器与OpenTelemetry Intermediate集成  | Intermediate     |

### /getting-started

开始使用OpenTelemetry JS。

目录结构：

```
./
├── example
└── images
└── monitored-example
└── traced-example
└── ts-example
└── README.md
```

此文件夹将带用户了解：tracing后端（在本例中是Zipkin，但是Jaeger也很容易使用）、metrics后端（如Prometheus）和NodeJS的自动检测的设置和配置过程。具体指令的使用可见该目录中的ReadMe文件。

### /integration-tests

集成测试。

目录结构：

```
./
├── propagation-validation-server
	└── package.json
	└── validation-server.js
└── tracecontext-integration-test.sh
```

### /metapackages

目录结构：

```
./
├── plugins-node-core
└── plugins-web-core
```

#### Plugins

OpenTelemetry可以使用插件自动收集跟踪数据。供应商/用户也可以创建和使用他们自己的。目前，OpenTelemetry支持自动跟踪：

##### ·Node Plugins

###### Core

- @opentelemetry/plugin-grpc
- @opentelemetry/plugin-grpc-js
- @opentelemetry/plugin-http
- @opentelemetry/plugin-https

###### Contrib

这些插件托管在  <https://github.com/open-telemetry/opentelemetry-js-contrib/tree/master/plugins/node>

- @opentelemetry/plugin-mongodb
- @opentelemetry/plugin-mysql
- @opentelemetry/plugin-pg
- @opentelemetry/plugin-pg-pool
- @opentelemetry/plugin-redis
- @opentelemetry/plugin-ioredis
- @opentelemetry/plugin-express
- @opentelemetry/plugin-dns
- @opentelemetry/hapi-instrumentation
- @opentelemetry/koa-instrumentation

##### ·Web Plugins

###### Core

- @opentelemetry/plugin-xml-http-request
- @opentelemetry/plugin-fetch

###### Contrib

这些插件托管在  <https://github.com/open-telemetry/opentelemetry-js-contrib/tree/master/plugins/web>

- @opentelemetry/plugin-document-load
- @opentelemetry/plugin-user-interaction

### /packages

#### ·API

| Package             | Description                                                  |
| :------------------ | :----------------------------------------------------------- |
| @opentelemetry/api  | 这个包为OpenTelemetry核心跟踪和度量模型提供了TypeScript接口、枚举和无操作实现。它可以在服务器和浏览器中使用。 |
| @opentelemetry/core | 这个包提供了OpenTelemetry api的默认实现和非操作实现，用于跟踪和度量。它可以在服务器和浏览器中使用。 |

#### ·Implementation / SDKs

|        Package         | Description                                                  |
| :--------------------- | :----------------------------------------------------------- |
| @opentelemetry/tracing | 此模块提供了对插装和span创建的完全控制。默认情况下，它不加载async_hooks或任何插装插件。它可以在服务器和浏览器中使用。 |
| @opentelemetry/metrics | 本模块提供用于时间序列数据报告的仪器和仪表。 |
| @opentelemetry/node   | 这个模块为Node.js应用程序提供了自动跟踪。它仅用于服务器上。 |
| @opentelemetry/web   | 此模块为Web应用程序提供自动化的检测和跟踪。它仅用于浏览器中。 |

### /scripts 

其下是version-update.js，与版本更新有关。