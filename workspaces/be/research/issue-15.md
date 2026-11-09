The tea service does set a 5s Feign read timeout, so I need to check whether that's overridden or bypassed.

Read timeout is 5s in config, but the `make` path has no fallback. Next I'll check the controller, the UI request, and the water service for artificial delay.

Water-service calls from tea-service do have a 5s read timeout, so the hang is bounded at about 5s per call, not unbounded. The real gaps are no fallback, sequential calls, and no timeout on the browser `fetch`.

- `repos/teahouse-tea-service/src/main/resources/application.properties:42-43`: water client has `connect-timeout=100` and `read-timeout=5000`; the only override is this file, so no timeout is missing here.
- `repos/teahouse-tea-service/src/main/java/org/example/teahouse/tea/water/WaterClient.java:14-15`: `findBySize` has no per-method timeout, retry, or fallback.
- `repos/teahouse-tea-service/src/main/java/org/example/teahouse/tea/config/FeignClientConfig.java:9-13`: only sets logger level; no `Request.Options` or `Retryer` bean.
- `repos/teahouse-tea-service/src/main/java/org/example/teahouse/tea/service/DefaultTeaService.java:27-28`: water call is synchronous, then the tealeaf call runs after it, so the two timeouts add up.
- `repos/teahouse-tea-service/src/main/java/org/example/teahouse/tea/controller/TeaController.java:26-34`: `make` has no timeout or error handling; a Feign timeout propagates as a 500. No `@ControllerAdvice`/`@ExceptionHandler` exists in tea-service.
- `repos/teahouse-tea-service/src/main/resources/public/assets/steep.js:18-20`: browser `fetch` has no abort timeout; the customer sees no progress indicator until the response arrives or fails.
- `repos/teahouse-tea-service/src/main/resources/templates/steep.html:36,53`: "Steep it!" button and alert container; no spinner element.
- `repos/teahouse-water-service/src/main/java/org/example/teahouse/water/controller/WaterController.java:47-52`: `findBySize` has no artificial delay; slowness would come from its datastore (`repos/teahouse-water-service/src/main/resources/application-mysql.properties:8` notes latency injection via toxiproxy).

Affected repos
- teahouse-tea-service (primary: timeouts, fallback, sequential calls, UI)
- teahouse-water-service (slow dependency; likely no code change)

Open questions
- Does the reported "long" hang exceed 5s? If so, production config may override the property (env var or config server), which I could not check.
- Is the tealeaf-service call also bounded similarly (its timeout is 5s, same pattern)?
- What exact error text does the customer see on timeout? Expected a generic Spring 500, not verified at runtime.

