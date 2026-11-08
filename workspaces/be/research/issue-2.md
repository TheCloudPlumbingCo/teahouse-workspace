The Feign client for the water service lives in `teahouse-tea-service`, as `WaterClient`.

- `repos/teahouse-tea-service/src/main/java/org/example/teahouse/tea/water/WaterClient.java:12` declares `@FeignClient(name = "water", configuration = FeignClientConfig.class)`.
- `WaterClient.java:14-18` defines `findBySize` (`/waters/search/findBySize`) and `waters` (`/waters`).
- `WaterClient.java:5` imports `SimpleWaterModel` from the water API package.
- `repos/teahouse-tea-service/src/main/resources/application.properties:41-43` sets the `water` client URL to `http://localhost:8091`, with timeouts of 100 ms connect and 5000 ms read.
- A repo-wide search found no other `@FeignClient` for water in `teahouse-water-service` or `teahouse-core`.

Affected repos:
- teahouse-tea-service (owns the Feign client)
- teahouse-water-service (serves the endpoints; its `water-api` module supplies `SimpleWaterModel`)

Open questions:
- I did not confirm that the water service actually listens on port 8091.
- Whether `teahouse-water-service` should be listed as affected depends on how the workspace treats the shared `water-api` module.

