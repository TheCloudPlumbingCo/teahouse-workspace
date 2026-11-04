Need config for URL.

tea-service calls water-service over HTTP/REST through a declarative Spring Cloud OpenFeign client (`WaterClient`) pointed at `http://localhost:8091`, and it shares DTOs through the `water-api` artifact.

- `teahouse-tea-service/src/main/java/org/example/teahouse/tea/water/WaterClient.java:12` declares `@FeignClient(name = "water", configuration = FeignClientConfig.class)`.
- `WaterClient.java:14-15` maps `GET /waters/search/findBySize?size=…` to `SimpleWaterModel findBySize(String size)`.
- `WaterClient.java:17-18` maps `GET /waters` to `PagedModel<SimpleWaterModel> waters(Pageable)`.
- `WaterClient.java:13` extends `HealthClient`.
- `teahouse-tea-service/src/main/java/org/example/teahouse/tea/TeaServiceApplication.java:18` enables Feign with `@EnableFeignClients`.
- `TeaServiceApplication.java:29-30` injects `WaterClient` into `DefaultTeaService`, wrapped by `ObservedTeaService`.
- `teahouse-tea-service/src/main/resources/application.properties:41-43` sets the water client URL to `http://localhost:8091`, connect timeout 100 ms and read timeout 5000 ms. The URL is hard-coded, with no service discovery.
- `teahouse-tea-service/src/main/resources/application.properties:50` sets `WaterClient` logging to DEBUG.
- `teahouse-tea-service/src/main/java/org/example/teahouse/tea/service/DefaultTeaService.java:27` calls `waterClient.findBySize(size)` and uses the amount in the `TeaResponse`.
- `DefaultTeaService.java:65` calls `waterClient.waters(Pageable.unpaged())` to list the waters.
- `teahouse-tea-service/build.gradle:34` depends on `org.example.teahouse:water-api:$waterApiVersion`.
- `teahouse-tea-service/gradle.properties:5` sets `waterApiVersion=0.1.0-SNAPSHOT`.
- `gradle.properties:2` lists `teahouse-water-service` among the upstream repos.
- The shared model types (`SimpleWaterModel`, `WaterModel`) come from `teahouse-water-service/water-api/src/main/java/org/example/teahouse/water/api/`.
- `teahouse-tea-service/src/main/java/org/example/teahouse/tea/config/ActuatorConfig.java:20` adds a health indicator that uses `WaterClient`.
- `teahouse-tea-service/src/main/java/org/example/teahouse/tea/observation/BassObservationHandler.java:36-39` applies a Feign observation handler only to URLs containing `/waters/`.

## Affected repos
- `teahouse-tea-service`: the caller.
- `teahouse-water-service`: the callee, and the source of `water-api`.
- `teahouse-core`: listed as an upstream repo in `gradle.properties`.

## Open questions
- I did not read the water-service controllers, so I haven't confirmed that its `/waters` and `/waters/search/findBySize` endpoints match the client mappings. I also haven't confirmed that it listens on port 8091.
- I did not inspect `FeignClientConfig`, so I don't know what interceptors, error decoding or retries it sets.
- I don't know how the URL is overridden outside local development.

Skill used: research-style

