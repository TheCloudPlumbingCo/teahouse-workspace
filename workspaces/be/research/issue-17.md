Key files are identified. I'll read the water service model, its seed data, and the tea service's mapping and client code.

The second batch was denied by the shell. I'll read the remaining files with the view tool instead.

Water temperature already comes from tealeaf data, not the water service, so the water service has no temperature concept to change; the tea service only passes that value through.

- `teahouse-tea-service/src/main/java/org/example/teahouse/tea/service/DefaultTeaService.java:34` sets `Water.temperature` from `tealeafModel.getSuggestedWaterTemperature()`.
- `teahouse-tea-service/src/main/java/org/example/teahouse/tea/service/DefaultTeaService.java:44` sets `steepingTime` from the tealeaf's suggested steeping time.
- `teahouse-tea-service/tea-api/src/main/java/org/example/teahouse/tea/api/Water.java:78-79` defines `amount` and `temperature` only.
- `teahouse-tea-service/src/main/resources/public/assets/steep.js:39` renders `tea.water.temperature` on the steep page.
- `teahouse-tealeaf-service/src/main/resources/db/migration/V1_1__insert_tealeaf_resources.sql:3-5` seeds per-tea temperatures: sencha 75 °C, gyokuro 70 °C, da hong pao 99 °C.
- `teahouse-tealeaf-service/src/main/resources/db/migration/V1__create_tealeaf_table.sql:6` stores `suggested_water_temperature` as a `varchar(255)`.
- `teahouse-tealeaf-service/src/test/java/org/example/teahouse/tealeaf/TealeafServiceApplicationTest.java:46,54,62` asserts those seeded temperatures.
- `teahouse-water-service/src/main/java/org/example/teahouse/water/repo/Water.java:27-31` has only `size` and `amount`, with no temperature.
- `teahouse-water-service/water-api/src/main/java/org/example/teahouse/water/api/WaterModel.java:66-70` exposes `id`, `size` and `amount` only.
- `teahouse-water-service/src/main/resources/db/migration/V1_1__insert_water_resources.sql:59-61` seeds vessel sizes and amounts, not temperatures.

Affected repos:
- teahouse-tealeaf-service (likely the real data owner: seed values, schema and tests)
- teahouse-tea-service (`DefaultTeaService`, `steep.js`, `tea-api` only if the response shape changes)
- teahouse-water-service (probably not affected, unless temperature should vary by vessel size)

Open questions:
- Issue #17 names teahouse-water-service, but the temperature is per tealeaf, so is that repo really in scope?
- Does "right temperature" mean the existing per-tea values are wrong, or that the tea list is missing teas? Only three teas are seeded.
- Should the temperature vary with water amount (100/200/300 ml)?
- Is a "bitter or weak" warning or a different steeping time also wanted?

