<div align="center">

# Current Scope and Roadmap

### Project scope, portfolio boundaries and future development direction

</div>

---

## Overview

This document describes the current scope and future development direction of **WMS-lite – Warehouse Operations System**.

The public repository is prepared as a portfolio case study. Its purpose is to show the system logic, warehouse workflows, database-backed stock control, permissions, correction handling and audit approach.

The repository is not meant to expose real warehouse data or production files. It presents the application structure and operational thinking behind the system in a safe public form.

---

## 1. Current Project Scope

WMS-lite covers the main warehouse operation flow:

```text
Inbound receiving
→ Pending stock
→ Stock release
→ Current stock
→ Outbound order
→ Picking plan
→ Picking execution
→ Missing / shortage handling
→ Packing
→ Outbound pallet / box assignment
→ Packed corrections
→ Stock record corrections
→ Audit history
```

The system focuses on warehouse execution and process control.

Current core areas include:

- delivery plan import,
- inbound receiving,
- pending stock control,
- stock release,
- current stock overview,
- stock status control,
- picking plan generation,
- picking execution,
- shortage handling,
- Missing reassignment,
- product found outside stock,
- packing,
- partial packing,
- outbound pallet / box assignment,
- packing list review,
- packed item corrections,
- full stock record correction,
- role-based permissions,
- audit history.

The system is designed around real warehouse process flow, not only around inventory visibility.

---

## 2. Portfolio Repository Boundary

The public repository is prepared for safe review.

It is intended to show:

- workflow design,
- application logic,
- database structure,
- validation approach,
- audit logic,
- permission model,
- correction handling,
- problem-solving workflows.

The repository should not include:

- real warehouse database files,
- real customer data,
- real supplier files,
- real order files,
- real employee data,
- production backups,
- internal logs,
- secrets or credentials.

This boundary protects operational data while still allowing the system design to be reviewed.

---

## 3. Public Demo Data Scope

The repository can include only safe sample data.

Planned sample files:

```text
sample_data/sample_delivery_plan.csv
sample_data/sample_order.csv
```

The sample data should be small, fake and easy to understand.

Its purpose is to show the expected file structure and workflow input format without exposing real warehouse information.

Future demo improvements could include:

- generated demo database,
- fake delivery records,
- fake order records,
- fake picking plan,
- fake packing list,
- fake audit history,
- sample users and roles.

This would make the project easier to review and test without using real data.

---

## 4. Application Structure Scope

The current portfolio version is presented as a single-file Streamlit application:

```text
app.py
```

This keeps the public repository easy to review because the main operational logic is visible in one place.

The application includes:

- Streamlit UI,
- SQLite database operations,
- authentication logic,
- permissions,
- delivery workflows,
- stock workflows,
- picking workflows,
- packing workflows,
- correction workflows,
- audit history logic.

A future engineering version could split the code into modules such as:

```text
database/
services/
repositories/
ui/
auth/
workflows/
reports/
tests/
```

That would improve long-term maintainability, but the current structure is practical for presenting the complete system logic in a portfolio repository.

---

## 5. Database Scope

The current version uses SQLite.

SQLite keeps the project:

- portable,
- simple to run locally,
- easy to back up,
- easy to inspect,
- suitable for a local internal warehouse tool,
- practical for portfolio review.

The database supports:

- stock records,
- current stock summaries,
- delivery plans,
- delivery history,
- picking plans,
- picking area,
- outbound pallet / box structure,
- audit history,
- users,
- permissions,
- login events,
- system state.

For larger multi-site or high-concurrency environments, a server-based database would be the natural next step.

Possible future options:

```text
PostgreSQL
SQL Server
MySQL / MariaDB
```

This would support stronger multi-user scaling, centralized deployment and more advanced reporting infrastructure.

---

## 6. Streamlit Application Scope

Streamlit is used as the application interface.

It allows fast development of operational screens such as:

- login,
- current stock,
- delivery receiving,
- picking,
- packing,
- corrections,
- history,
- admin panels,
- reports.

For this project, Streamlit is a practical choice because it allows the warehouse workflow to be built and reviewed quickly.

A future production version could introduce a separate backend and frontend architecture.

Possible future stack:

```text
FastAPI backend
PostgreSQL database
React / Vue frontend
Role-based API layer
Dedicated scanner UI
```

This direction would make sense if the system needed stronger deployment control, mobile-first scanner screens or larger multi-station usage.

---

## 7. Multi-Station and Synchronization Scope

The system supports multi-station operational work through shared database state, controlled refreshes, session persistence and view preservation mechanisms.

Operational actions such as receiving, picking, packing and corrections are written to the database and then reflected in the application through controlled refresh and rerun logic.

The current version uses:

- shared SQLite state,
- database reads after operational writes,
- cache invalidation,
- session state,
- station persistence,
- controlled rerun logic,
- view preservation after writes.

This is practical for local warehouse use.

A future version could extend this with event-driven live updates.

Possible improvements:

- live update indicators,
- websocket-based event push,
- station activity monitor,
- record lock indicators,
- conflict warnings for simultaneous edits,
- real-time supervisor dashboard,
- background event queue.

The current system already contains practical synchronization mechanisms. The future direction would improve visibility and responsiveness in higher-concurrency environments.

---

## 8. Scanner Workflow Direction

The current system supports scan-based workflows through structured inputs and validation.

This already allows operators to work with PN, pallet, box and station-based actions.

Future improvements could make scanning even more operator-focused:

- larger scan-first screens,
- automatic input focus,
- success / error sound feedback,
- faster scan confirmation,
- mobile-friendly station layout,
- dedicated scanner mode,
- scan queue for unstable network conditions,
- process-step barcode validation.

This would improve speed and ergonomics on the warehouse floor.

---

## 9. Import Workflow Direction

The current system supports file-based imports for delivery plans and outbound orders.

This is practical because many warehouse processes still rely on CSV or Excel files.

Current import areas include:

- delivery plan import,
- order import,
- column validation,
- quantity validation,
- missing field validation,
- duplicate grouping,
- safe parsing of uploaded files.

Future improvements could include:

- import mapping profiles,
- import preview screen,
- detailed validation report,
- duplicate detection dashboard,
- scheduled imports,
- API-based import,
- ERP connector,
- import history.

This would reduce manual preparation work and make data intake more controlled.

---

## 10. ERP / External System Direction

WMS-lite currently works as a standalone warehouse operations system.

It can support warehouse execution independently from a larger ERP.

A future version could integrate with external systems for:

- delivery plan import,
- outbound order import,
- stock adjustment export,
- shipment confirmation export,
- audit export,
- discrepancy reporting,
- status synchronization.

Possible integration methods:

```text
CSV exchange
Scheduled file import/export
REST API
Database connector
ERP-specific connector
```

This would position WMS-lite as an operational execution layer between warehouse floor activity and central business systems.

---

## 11. Reporting Direction

The system already supports operational visibility through stock, delivery, order, packing and history views.

Future reporting can expand management visibility.

Possible future reports:

- stock aging,
- picking productivity by hour,
- packing productivity by station,
- Missing frequency by PN,
- correction frequency by operator,
- damage trend analysis,
- extra stock trend analysis,
- order completion time,
- delivery receiving time,
- outbound readiness dashboard,
- audit activity dashboard.

The goal of future reporting is to support supervisors and leaders in daily decision-making.

---

## 12. Audit Review Direction

The system already records audit history for operational actions and corrections.

Future improvements could focus on making audit review easier.

Possible improvements:

- audit timeline per PN,
- audit timeline per pallet,
- audit timeline per box,
- audit timeline per order,
- audit timeline per operator,
- before / after comparison view,
- audit export filters,
- correction reason categories,
- supervisor approval trail,
- immutable audit export.

The current audit model provides traceability. The future direction is to make that traceability easier to analyze.

---

## 13. Permissions Direction

The system includes role-based permissions.

Current roles include:

```text
ADMIN
SUPERVISOR
LEADER
OPERATOR
VIEWER
```

Permissions cover areas such as:

- current stock,
- export,
- worker performance,
- orders,
- deliveries,
- receiving,
- picking,
- packing,
- stock correction,
- packed correction,
- user management,
- admin tools.

Future improvements could include:

- permission templates,
- permission change approval,
- role history,
- temporary permissions,
- station-based permissions,
- shift-based access,
- read-only audit mode,
- supervisor approval for high-risk corrections.

This would make access control stronger for larger teams.

---

## 14. Correction Workflow Direction

The system already supports important warehouse correction workflows:

```text
Stock record correction
Return PACKED to PICKED
Transfer PACKED between outbound structures
Edit packed outbound labels
Missing reassignment
Product found outside stock
```

Future improvements could make correction handling more structured.

Possible improvements:

- correction reason codes,
- mandatory correction comment,
- correction impact preview,
- supervisor approval for selected correction types,
- correction dashboard,
- blocked correction if stock consistency would break,
- attachment support for evidence,
- anomaly detection for suspicious corrections.

Corrections are powerful actions, so the future direction is stronger control and clearer review.

---

## 15. Testing and Validation Direction

The current version contains workflow-level validation inside the application.

The system validates key operational actions such as:

- delivery file structure,
- required fields,
- product numbers,
- quantities,
- stock statuses,
- picking availability,
- packing quantities,
- user permissions,
- station requirements,
- correction rules.

For a larger production version, automated tests would be useful for critical workflows.

Priority areas:

- delivery import validation,
- stock release,
- current stock recalculation,
- picking allocation,
- partial picking,
- Missing reassignment,
- product found outside stock,
- partial packing,
- return PACKED to PICKED,
- packed transfer,
- stock record correction,
- permission checks,
- audit history writes.

The purpose of testing would be to reduce regression risk when expanding the system.

---

## 16. Database Migration Direction

The current application contains schema creation and schema adjustment logic.

This is practical for a local internal tool.

A future engineering version could introduce a structured migration system.

Possible improvements:

- schema version table,
- migration files,
- migration runner,
- rollback strategy,
- migration logs,
- demo data seeding,
- separate demo database generator.

This would make database evolution easier to manage over time.

---

## 17. UI / UX Direction

The current UI focuses on operational control and process safety.

Future improvements could make the interface cleaner and faster for operators.

Possible improvements:

- clearer screen grouping,
- role-specific navigation,
- scan-first layouts,
- larger operational buttons,
- clearer status badges,
- better action hierarchy,
- more compact tables,
- simplified correction panels,
- dashboard landing page,
- mobile-friendly station layout.

The goal is to keep process safety while reducing screen complexity.

---

## 18. Performance Direction

The current system is suitable for local warehouse datasets and operational workflows.

Future performance improvements could focus on larger datasets and heavier usage.

Possible improvements:

- indexes on frequently filtered columns,
- optimized SQL queries,
- pagination for large tables,
- archived history views,
- faster stock summaries,
- better cache strategy,
- background maintenance tasks,
- server database migration.

These improvements would support larger operational scale.

---

## 19. Security Direction

The system includes authentication, password hashing, roles and permissions.

Future production hardening could include:

- HTTPS deployment,
- environment-based secrets,
- stronger password policy,
- account lockout rules,
- session management review,
- backup encryption,
- least-privilege deployment user,
- audit export protection,
- separate admin access logs,
- security review before production use.

The public repository must remain free from secrets, real users and production data.

---

## 20. Backup and Recovery Direction

The system is based on a local database file, so backup discipline is important.

Future improvements could include:

- scheduled backups,
- backup retention policy,
- backup integrity checks,
- restore procedure,
- admin restore screen,
- external backup location,
- backup encryption,
- backup audit log.

For real warehouse use, backup and recovery should be treated as part of the operational process.

---

## 21. Short-Term Roadmap

Short-term roadmap focuses on portfolio clarity and safe public presentation.

Planned steps:

```text
1. Keep the repository free from real data.
2. Add safe sample CSV files.
3. Add screenshots for main workflows.
4. Improve README structure.
5. Complete feature documentation.
6. Complete workflow documentation.
7. Complete problem-solving documentation.
8. Complete permissions documentation.
9. Complete database and audit documentation.
10. Keep roadmap focused on project scope and future direction.
```

Short-term goal:

```text
Make the project easy to understand for recruiters, clients and technical reviewers.
```

---

## 22. Medium-Term Roadmap

Medium-term roadmap focuses on maintainability and operational polish.

Planned improvements:

- split application logic into modules,
- improve scan-first UI,
- add demo database generator,
- improve audit review screens,
- add correction reason codes,
- improve reporting dashboards,
- improve import validation reports,
- add permission templates,
- expand sample data,
- document setup process more clearly.

Medium-term goal:

```text
Make the system easier to maintain, test, review and extend.
```

---

## 23. Long-Term Roadmap

Long-term roadmap focuses on production scalability.

Possible future direction:

- migrate SQLite to PostgreSQL or SQL Server,
- separate backend and frontend,
- add API layer,
- add event-driven live updates,
- integrate with ERP / external WMS,
- add mobile scanner interface,
- add supervisor approval workflows,
- add advanced reporting,
- add deployment documentation,
- add monitoring and alerting,
- add formal backup and restore process.

Long-term goal:

```text
Evolve the project from a local warehouse operations tool into a scalable warehouse execution platform.
```

---

## 24. Not Included in the Public Repository

The public portfolio repository should not include:

- real warehouse database,
- real operational logs,
- real employee data,
- real customer data,
- production credentials,
- private company workflows,
- confidential business rules,
- direct production deployment setup.

This protects sensitive information while still showing the system architecture and process logic.

---

## 25. Summary

WMS-lite demonstrates a practical warehouse operations system built around:

- inbound receiving,
- active stock control,
- status-based inventory logic,
- picking plan generation,
- picking execution,
- Missing handling,
- product found outside stock,
- packing,
- outbound assignment,
- packed corrections,
- stock record correction,
- role-based permissions,
- audit history.

The current public version focuses on safe portfolio presentation.

The roadmap shows how the system can grow in maintainability, reporting, integration, scanner usability, database scalability and production readiness.

---

<div align="center">

**WMS-lite – Warehouse Operations System**  
A practical warehouse operations system with a clear direction toward stronger modularity, reporting, integration and production readiness.

</div>
