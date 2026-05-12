<div align="center">

# Limitations and Roadmap

### Current project boundaries, known limitations and planned improvements

</div>

---

## Overview

This document describes the current limitations and planned improvements for **WMS-lite – Warehouse Operations System**.

The project is presented as a portfolio case study of a practical warehouse operations system. It focuses on process control, stock traceability, exception handling, role-based permissions and audit history.

The public repository is not intended to be a production deployment package. It is intended to show how the system was designed, what problems it solves and what could be improved in future versions.

---

## 1. Current Project Scope

WMS-lite currently covers the main warehouse operation flow:

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

The system is focused on warehouse process execution rather than financial, purchasing or ERP-level planning.

Current core areas:

- delivery plan import,
- inbound receiving,
- pending stock control,
- current stock overview,
- stock statuses,
- picking plan generation,
- picking execution,
- shortage handling,
- missing reassignment,
- product found outside stock,
- packing,
- partial packing,
- outbound pallet / box structure,
- packing list review,
- packed item corrections,
- full stock record correction,
- permissions,
- audit history.

---

## 2. Portfolio Version Boundary

The current public version is prepared for portfolio review.

That means the repository focuses on showing:

- application logic,
- workflow design,
- database structure,
- process validation,
- audit approach,
- problem-solving logic,
- permissions model,
- correction handling.

The repository should not contain:

- real warehouse database files,
- real customer data,
- real supplier files,
- real order files,
- real employee data,
- production backups,
- internal logs,
- secrets or credentials.

This boundary is intentional.

The goal is to present the system safely without exposing operational data.

---

## 3. Single-File Application Structure

The current portfolio version is presented as a single-file Streamlit application.

Most UI logic, database operations and workflow logic are placed in:

```text
app.py
```

This makes the project easy to review in a public repository because the full operational scope is visible in one place.

However, this structure has limits.

As the system grows, a better long-term structure would split the code into dedicated modules, for example:

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

This would improve maintainability, testing and future development.

The current structure is acceptable for a portfolio case study, but a production-ready version should be modularized.

---

## 4. Local SQLite Limitation

The system uses SQLite as a local database.

SQLite is practical for a local internal tool because it is simple, portable and easy to back up.

However, SQLite has natural limitations:

- limited concurrent write performance,
- local-file dependency,
- no built-in user-level database server,
- no native multi-site synchronization,
- higher risk of locking under heavy parallel use,
- limited scalability compared to PostgreSQL or SQL Server.

For a small internal warehouse tool, SQLite can be enough.

For a larger multi-user or multi-site deployment, the database should be migrated to a server-based engine.

Possible future options:

```text
PostgreSQL
SQL Server
MySQL / MariaDB
```

---

## 5. Streamlit Deployment Limitation

Streamlit is useful for fast internal tools and data apps.

It allows quick creation of operational screens, forms, filters and reports.

However, Streamlit has limitations for warehouse production use:

- rerun-based interaction model,
- limited native control over complex UI state,
- browser session dependency,
- limited offline behavior,
- no native mobile scanning interface,
- less control than a custom web frontend.

For the current project, Streamlit is a practical choice.

For a larger production system, a custom web application could be considered.

Possible future stack:

```text
FastAPI backend
PostgreSQL database
React / Vue frontend
Role-based API layer
Dedicated scanner UI
```

---

## 6. Real-Time Synchronization and Multi-Station Behavior

The system already supports multi-station operational work through shared SQLite state, controlled refreshes, session persistence and view preservation mechanisms.

Operational changes such as receiving, picking, packing and corrections are written to the database and then reflected in the application through controlled refresh and rerun logic.

The current version is suitable for local warehouse use where multiple users work against the same database file or local deployment.

However, the system does not yet use a dedicated websocket or event-driven push layer.

That means updates are synchronized through database reads, cache invalidation and application refresh logic, rather than instant push notifications between all connected stations.

Future improvements could include:

- live update indicators,
- websocket-based event push,
- station activity monitor,
- record lock indicators,
- conflict warnings for simultaneous edits,
- real-time supervisor dashboard,
- background event queue.

This would improve visibility in high-concurrency environments, but the current system already contains practical synchronization mechanisms for local operational workflows.
---

## 7. Limited Barcode Scanner Optimization

The system supports scanning-style workflows through text inputs and operational forms.

However, the current version is not a dedicated scanner-first interface.

Limitations:

- no hardware scanner profile management,
- no mobile-first scan screen,
- limited scan sound / visual confirmation,
- limited offline scan queue,
- limited scan speed optimization,
- no dedicated error beep / success beep logic.

Future improvements:

- scanner-first UI layout,
- large input fields,
- automatic focus handling,
- scan confirmation sounds,
- mobile-friendly station mode,
- barcode validation by process step,
- scan queue for unstable network conditions.

---

## 8. Manual File Import Dependency

The system currently supports file-based imports for delivery plans and orders.

This is practical for warehouses that work with CSV or Excel files.

However, this approach has limitations:

- files must be prepared correctly,
- column names must match accepted formats,
- bad files require validation handling,
- manual upload can be slower than integration,
- there is no direct ERP / WMS API connection.

Future improvements:

- scheduled imports,
- API integration,
- direct ERP connector,
- import mapping profiles,
- import preview with validation report,
- automatic duplicate detection,
- import history dashboard.

---

## 9. Limited ERP Integration

WMS-lite is currently a standalone warehouse operations tool.

It does not directly synchronize with an external ERP system.

This means that inbound plans, outbound orders and stock corrections are handled inside the local system or through files.

Future integration options:

- ERP order import,
- ERP delivery plan import,
- stock adjustment export,
- shipment confirmation export,
- audit export,
- API-based synchronization.

This would make the system stronger as a real operational layer between warehouse floor execution and central business systems.

---

## 10. Reporting Limitations

The system includes operational views and audit data, but reporting can still be expanded.

Current reporting is focused mainly on process control:

- current stock,
- delivery discrepancy,
- order progress,
- packing list,
- history,
- worker performance.

Potential reporting improvements:

- stock aging,
- picking productivity by hour,
- packing productivity by station,
- missing frequency by PN,
- correction frequency by operator,
- damage trend analysis,
- extra stock trend analysis,
- order completion time,
- delivery receiving time,
- outbound readiness dashboard.

A future reporting layer could make the system more useful for supervisors and managers.

---

## 11. Audit History Limitations

The system contains audit history for operational actions and corrections.

However, future improvements could make audit review stronger.

Possible improvements:

- clearer audit event categories,
- before / after comparison view,
- audit timeline per PN,
- audit timeline per pallet,
- audit timeline per order,
- audit timeline per operator,
- audit export filters,
- immutable audit export,
- supervisor approval for selected corrections.

The current audit model already supports traceability, but the review experience could be improved.

---

## 12. Permissions Limitations

The system includes role-based permissions.

Current roles include:

```text
ADMIN
SUPERVISOR
LEADER
OPERATOR
VIEWER
```

Permissions cover operational and administrative areas such as:

- current stock,
- deliveries,
- picking,
- packing,
- corrections,
- exports,
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
- supervisor approval for risky actions.

This would improve control in larger warehouse teams.

---

## 13. Correction Workflow Limitations

The system supports important correction workflows:

```text
Stock record correction
Return PACKED to PICKED
Transfer PACKED between outbound structures
Edit packed outbound labels
Missing reassignment
Product found outside stock
```

These workflows are necessary for real operations.

However, correction workflows can always be improved.

Future improvements:

- correction reason codes,
- mandatory comment for all corrections,
- supervisor approval for selected correction types,
- correction dashboard,
- correction impact preview,
- blocked correction if it breaks stock consistency,
- attachment support for evidence,
- automatic anomaly detection.

Corrections are powerful and should remain controlled.

---

## 14. Testing Limitation

The current public version is focused on application logic and workflow demonstration.

A stronger engineering version should include a formal test suite.

Future tests should cover:

- delivery plan parsing,
- order parsing,
- stock release,
- current stock recalculation,
- picking allocation,
- partial picking,
- missing reassignment,
- product found outside stock,
- partial packing,
- return PACKED to PICKED,
- packed transfer,
- stock record correction,
- permission checks,
- audit writes.

Recommended future testing structure:

```text
tests/
├── test_imports.py
├── test_stock_statuses.py
├── test_picking.py
├── test_packing.py
├── test_corrections.py
├── test_permissions.py
└── test_audit.py
```

This would make future changes safer.

---

## 15. Database Migration Limitation

The current project contains schema creation and schema adjustment logic inside the application.

This works for a local tool, but a larger system should use proper database migrations.

Future improvements:

- migration files,
- schema version table,
- migration runner,
- rollback strategy,
- migration logs,
- seed data for demo use,
- separate demo database generator.

Possible tools:

```text
Alembic
SQL migration scripts
Custom migration runner
```

This would make database evolution safer and easier to track.

---

## 16. Demo Data Limitation

The public repository should use only sample data.

Sample files should be small and safe to publish.

Current planned sample files:

```text
sample_data/sample_delivery_plan.csv
sample_data/sample_order.csv
```

Future improvements:

- generated demo database,
- realistic but fake warehouse dataset,
- sample users,
- sample permissions,
- sample delivery,
- sample picking plan,
- sample packed shipment,
- sample audit history.

This would make the project easier to test and review without using real data.

---

## 17. UI / UX Limitations

The current UI focuses on function and operational control.

Some screens can become dense because warehouse workflows contain many fields, validations and exception states.

Future UI improvements:

- cleaner screen grouping,
- improved scan-first layout,
- clearer status badges,
- better action hierarchy,
- more compact tables,
- clearer correction panels,
- role-specific navigation,
- dashboard landing page,
- better mobile layout for scanner use.

The goal should be to keep operational safety while reducing screen complexity.

---

## 18. Performance Limitations

The current application is suitable for small or medium local datasets.

As data grows, performance may require improvement.

Potential bottlenecks:

- large stock tables,
- large history table,
- repeated dataframe loading,
- heavy filters,
- repeated recalculation,
- Streamlit reruns,
- SQLite write locking.

Future improvements:

- indexes on frequently filtered columns,
- optimized SQL queries,
- pagination,
- archived history views,
- incremental stock summaries,
- background maintenance tasks,
- database server migration.

---

## 19. Security Limitations

The application includes authentication, password hashing, roles and permissions.

However, a production deployment would require additional security hardening.

Future improvements:

- HTTPS deployment,
- environment-based secrets,
- stronger password policy,
- account lockout rules,
- session management review,
- audit export protection,
- backup encryption,
- least-privilege deployment user,
- separate admin access logs,
- security review before production use.

The public portfolio repository must not expose secrets, real users or production data.

---

## 20. Backup and Recovery Limitations

The system is local-file based, so backup discipline is important.

Future improvements:

- automatic scheduled backups,
- backup retention policy,
- backup integrity check,
- restore procedure,
- one-click restore for admin,
- external backup location,
- backup encryption,
- backup audit log.

For real warehouse use, backup and recovery should be treated as part of the operational process.

---

## 21. Roadmap — Short Term

Short-term improvements focus on making the portfolio repository clearer and safer.

Planned steps:

```text
1. Keep public repository free from real data.
2. Add clean sample CSV files.
3. Add screenshots for main workflows.
4. Improve documentation structure.
5. Add clear feature descriptions.
6. Add database and audit explanation.
7. Add limitations and roadmap.
8. Prepare README as a portfolio landing page.
```

Short-term goal:

```text
Make the project easy to understand for recruiters, clients and technical reviewers.
```

---

## 22. Roadmap — Medium Term

Medium-term improvements focus on maintainability and operational polish.

Planned improvements:

- split `app.py` into modules,
- add formal test suite,
- add database migration system,
- add demo database generator,
- improve scan-first UI,
- improve audit review screens,
- add correction reason codes,
- improve reporting dashboards,
- add better import validation reports,
- add clearer role permission templates.

Medium-term goal:

```text
Make the system easier to maintain, test and extend.
```

---

## 23. Roadmap — Long Term

Long-term improvements focus on production scalability.

Possible future direction:

- migrate SQLite to PostgreSQL,
- separate backend and frontend,
- add API layer,
- add real-time station synchronization,
- integrate with ERP / external WMS,
- add mobile scanner interface,
- add supervisor approval workflows,
- add advanced reporting,
- add deployment documentation,
- add monitoring and alerting,
- add formal backup and restore process.

Long-term goal:

```text
Evolve the project from local warehouse operations tool into a scalable warehouse execution platform.
```

---

## 24. Not Planned for This Repository

The public portfolio repository is not planned to include:

- real warehouse database,
- real operational logs,
- real employee data,
- real customer data,
- production credentials,
- private company workflows,
- confidential business rules,
- direct production deployment setup.

This protects sensitive operational information while still showing the technical and process design of the project.

---

## 25. Summary

WMS-lite already demonstrates a strong operational foundation:

- inbound receiving,
- active stock control,
- status-based inventory logic,
- picking plan generation,
- picking execution,
- missing handling,
- product found outside stock,
- packing,
- outbound assignment,
- packed corrections,
- stock record correction,
- role-based access,
- audit history.

The main limitations are not about the concept itself.

The main limitations are engineering maturity areas:

```text
single-file structure,
local SQLite backend,
manual imports,
limited real-time sync,
limited formal tests,
limited production deployment layer.
```

These are clear next steps, not blockers for presenting the project as a portfolio case study.

---

<div align="center">

**WMS-lite – Warehouse Operations System**  
A practical warehouse operations system with a clear roadmap toward stronger modularity, scalability, testing and production readiness.

</div>
