---
name: pureza-del-handler
description: Regla estricta de no usar Spring en Application.
---

# Pureza del Handler
- Los Handlers orquestan dominio y ports.
- **Application pura:** No llevan anotaciones Spring (`@Service`, `@Autowired`) ni JPA.
