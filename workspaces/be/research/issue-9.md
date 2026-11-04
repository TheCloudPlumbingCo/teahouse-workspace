Too noisy; focus on tea-service.

Falling back to built-in tools.

Adding iced teas is a small, mostly tea-service change, but it needs a product decision on how "iced" is modelled. Nothing in the codebase currently represents hot versus iced.

- **Tea endpoint:** `GET /tea/{name}?size=` is the only entry point. It takes just a name and a size, with no serving-style parameter (`teahouse-tea-service/src/main/java/org/example/teahouse/tea/controller/TeaController.java:25-27`). An `iced` or `style` request parameter would go here. It would also go into the `tea.make` counter tags at lines 29-33.
- **Brewing logic:** `DefaultTeaService.make` copies the tealeaf's suggested temperature, amount and steeping time straight into the response, and the tealeaf supplies all of them (`teahouse-tea-service/src/main/java/org/example/teahouse/tea/service/DefaultTeaService.java:26-46`). Iced tea needs new rules here. Likely options are brewing stronger or shorter, then adding an ice or chill step.
- **Response shape:** `TeaResponse` has only `water`, `tealeaf` and `steepingTime` (`teahouse-tea-service/tea-api/src/main/java/org/example/teahouse/tea/api/TeaResponse.java:10-12`). `Water` has only `amount` and `temperature` (`teahouse-tea-service/tea-api/src/main/java/org/example/teahouse/tea/api/Water.java:10-11`). Ice would need new fields, such as `ice` or `serve`. The `tea-api` module is published, so changes to it ripple to consumers.
- **Water service data:** water is only a size and an amount (`teahouse-water-service/src/main/resources/db/migration/V1_1__insert_water_resources.sql:1-4`). Ice could be modelled as a water attribute, or computed in tea-service. Doing it in the water service would mean a new Flyway migration in both the `db/migration` and `db/mysql-migration` folders.
- **Tealeaf service data:** each tealeaf has a single hot-brew set of suggested values (`teahouse-tealeaf-service/src/main/resources/db/migration/V1_1__insert_tealeaf_resources.sql:1-5`). If per-leaf iced parameters are wanted, this needs a new migration (again in both folders), and `tealeaf-api` model changes (`teahouse-tealeaf-service/tealeaf-api/src/main/java/org/example/teahouse/tealeaf/api/TealeafModel.java:10`).
- **Observability:** `DefaultMakeTeaConvention` tags observations only by tea name and water size (`teahouse-tea-service/src/main/java/org/example/teahouse/tea/service/DefaultMakeTeaConvention.java:19-22`). A serving-style tag could be added there and to `MakeTeaDocumentation` and `MakeTeaContext`.
- **UI:** `/steep` lists tealeaves and waters only (`teahouse-tea-service/src/main/java/org/example/teahouse/tea/controller/SteepController.java:19-20`). The template is `teahouse-tea-service/src/main/resources/templates/steep.html`. It would need an iced option.
- **Tests and docs:** `TeaServiceApplicationTest` and `docs/index.adoc` in tea-service would need updating.

## Affected repos
- `teahouse-tea-service`: required. Controller, service, `tea-api` response, observability, template, tests and docs.
- `teahouse-tealeaf-service`: only if iced parameters are stored per leaf. New migrations and a `tealeaf-api` change, with a version bump.
- `teahouse-water-service`: only if ice is modelled as water data.
- `teahouse-core`: no change expected. It holds shared error, actuator and logging code.

## Open questions
- Should iced be a request flag on the existing `/tea/{name}`, or a separate endpoint?
- Are iced parameters fixed rules (for example a flash-chill formula) or per-leaf data?
- Is ice an ingredient with its own amount, or just a serving note?
- Which tea types support iced? Delicate greens such as gyokuro may need rules.
- Can the `TeaResponse` shape change, given that `tea-api` is published?

I could only read files with the built-in tools, because shell commands were being denied. I did not open `steep.html`, `MakeTeaContext`, the tests or `docs/index.adoc`, so the notes on those are inferred from file names, not read.

Skill used: research-style

