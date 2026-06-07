# Reference Architectures

Proven architectural blueprints ready to adapt for new projects.

Each reference architecture maps to a common manifest combination. When `/generate-repo` encounters a matching combination, it consults the appropriate reference architecture for validated design decisions.

## Index

| Architecture | Manifest Match | Validated | ADR |
|-------------|----------------|-----------|-----|
| [Java Microservice on AWS](java-microservice-aws.md) | java + spring-boot + aws + microservices | ✅ Production | ADR-G001, G002, G005 |
| [Multi-Agent AI Platform](multi-agent-ai-platform.md) | python + langgraph + aws + multi-agent | ✅ PoC/Staging | ADR-G004 |
| [Event-Driven Platform](event-driven-platform.md) | java + kafka + aws + event-driven | ✅ Production | ADR-G002 |
| [Strangler Fig Modernization](strangler-fig-ibmi.md) | java + ibmi + modernization | ✅ Production | — |
