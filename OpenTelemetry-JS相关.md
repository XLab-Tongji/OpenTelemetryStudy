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