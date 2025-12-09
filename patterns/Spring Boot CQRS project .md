Here are several good **GitHub projects** you can explore which demonstrate CQRS + Event Sourcing (or CQRS alone) — useful as reference or starting points. I picked ones using Java / Spring Boot (or related JVM tools) so they map well to what you asked for.

## ✅ Recommended Sample CQRS / CQRS+ES Projects

| Repository & Short Description     | What’s Good / Why Pick It                                                                                                                                                                                                |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **cqrs-event-sourcing-springboot** | A Spring Boot microservices project that demonstrates CQRS + Event Sourcing. Good for seeing a real-world microservice layout. ([GitHub][1])                                                                             |
| **cqrs-eventsourcing-springboot**  | Another sample Spring Boot project implementing CQRS + Event Sourcing — simple enough to start exploring quickly. ([GitHub][2])                                                                                          |
| **axon-kafka-example**             | An example using the Axon Framework + Spring Boot + Kafka to implement write-side (commands & events) and separate read-side (queries). Good if you want to see event bus + projection-based architecture. ([GitHub][3]) |
| **ddd-cqrs-4-java-example**        | Shows how to implement DDD + CQRS + Event Sourcing without heavy frameworks — using standard Java + Spring Boot (or Quarkus). Good if you want to understand lower-level implementation. ([GitHub][4])                   |
| **event-source-cqrs-sample**       | A small sample project (often used as a learning example) that implements CQRS + Event Sourcing — useful for understanding core concepts in a simplified domain. ([GitHub][5])                                           |

---

## 🔍 What You Should Look for When Inspecting Such Projects

* Separation between *command/write side* and *query/read side* (different modules or at least different packages).
* Use of **events** (not just direct data updates) — commands produce events, and events are persisted (event-store or similar), then used to update read model / projections.
* Clear domain modeling (e.g. aggregates, commands, events) — rather than monolithic CRUD entities.
* If applicable: asynchronous or event-bus based propagation (e.g. using Kafka) to decouple write and read sides.
* Simplicity — a small domain (like orders, banking, todo, etc.) that helps you focus on architecture rather than business complexity.

---

## 💡 How to Use These for Your Learning / Project Scaffold

1. **Clone** one or two of these repos (preferably a simpler one first, e.g. `cqrs-eventsourcing-springboot` or `event-source-cqrs-sample`).
2. **Run** — see how commands / events / queries are handled.
3. **Inspect architecture** — how command vs query packages/modules are organized; how events are defined & stored; how projections/read models are updated.
4. **Use as base / template** — adapt to your own domain (e.g. orders, inventory, booking) by renaming, extending domain events/commands/aggregates.
5. Optionally **swap storage or bus** — e.g. if repo uses in-memory or H2, you may plug in your own event store (PostgreSQL, EventStoreDB, etc.), or swap messaging for database-backed event propagation.

[1]: https://github.com/FernandoUnix/cqrs-event-sourcing-springboot?utm_source=chatgpt.com "FernandoUnix/cqrs-event-sourcing-springboot"
[2]: https://github.com/robinhosz/cqrs-eventsourcing-springboot?utm_source=chatgpt.com "robinhosz/cqrs-eventsourcing-springboot ..."
[3]: https://github.com/horzel91/axon-kafka-example?utm_source=chatgpt.com "Axon CQRS/Event Sourcing example with Kafka"
[4]: https://github.com/fuinorg/ddd-cqrs-4-java-example?utm_source=chatgpt.com "fuinorg/ddd-cqrs-4-java-example"
[5]: https://github.com/ddd-by-examples/event-source-cqrs-sample?utm_source=chatgpt.com "ddd-by-examples/event-source-cqrs-sample"
