<div align="center">

# Database and Audit

### Data model, stock traceability and audit logic in WMS-lite

</div>

---

## Overview

WMS-lite uses a local SQLite database as the operational backend for warehouse processes.

The database is not used only as simple storage. It supports the main process flow of the system:

```text
Inbound receiving
→ Pending stock
→ Active stock
→ Picking plan
→ Picking execution
→ Packing
→ Outbound assignment
→ Corrections
→ Audit history
```

The database model is built around warehouse handling units:

- product number,
- quantity,
- pallet,
- box,
- country / flow context,
- stock status,
- operator,
- station,
- source record,
- operation history.

This allows the system to track not only how much stock exists, but also where it is, what process stage it belongs to and what happened to it over time.

---

## 1. Database Engine

The project uses SQLite as a local database engine.

SQLite was selected because it is practical for a local internal warehouse tool:

- no external database server is required,
- the database is stored as a local file,
- it works well for single-site operational use,
- it is easy to back up,
- it keeps the project portable for portfolio review.

The application uses SQLite connection settings such as:

```text
busy timeout
foreign keys enabled
WAL journal mode
row factory for dictionary-like access
```

This improves reliability when the app performs multiple operational writes, such as receiving, picking, packing and corrections.

---

## 2. Main Database Tables

The database contains several operational table groups.

The most important areas are:

```text
Stock records
Delivery plans
Picking plans
Picking area
Outbound structure
Audit history
Users and permissions
System state
```

The system separates process stages into different structures instead of storing everything as one flat inventory table.

This is important because warehouse stock is not only a number. It moves through operational states.

---

## 3. Stock Records

The core stock records are stored at pallet / box level.

A stock record can contain information such as:

- PN,
- quantity,
- country code,
- status,
- pallet label,
- pallet prefix,
- pallet number,
- box label,
- box prefix,
- box number,
- source file,
- delivery number,
- target status,
- last operator.

This makes the stock model more precise than a simple product summary.

The system can identify:

```text
Which PN exists?
How many pieces exist?
Which pallet contains it?
Which box contains it?
What is the current status?
Where did the record come from?
Who changed it last?
```

This is required for picking, packing, problem solving and audit history.

---

## 4. Current Stock Table

The system keeps a summarized current stock view.

The current stock summary is based only on active stock statuses:

```text
P  = normal stock available for operations
EX = extra stock available for controlled use
DA = damaged stock separated from normal picking
```

The current stock summary stores:

- PN,
- country code,
- status,
- total quantity,
- pallet count,
- box count.

This makes the stock overview faster and easier to read.

The summary is rebuilt from detailed stock records, so the detailed pallet / box level data remains the source of truth.

---

## 5. Status Model

The database uses technical statuses to separate physical stock flow from process state.

Main statuses include:

```text
P          = normal active stock
EX         = extra active stock
DA         = damaged active stock
PENDING    = received or created record waiting for final classification
PENDING_P  = received stock waiting for release as normal stock
PENDING_DA = received stock waiting for release as damaged stock
PENDING_EX = received stock waiting for release as extra stock
MISSING    = stock expected in a source location but not physically found
PICKED     = stock already moved from warehouse stock into picking area
PACKED     = stock already packed into an outbound shipment structure
USUNIĘTY   = removed / zeroed stock record kept for traceability
```

This status model is one of the key parts of the system.

It prevents the application from treating every database row as available stock.

For example:

- pending stock is not yet released,
- picked stock is already assigned to picking,
- packed stock is already assigned to outbound,
- missing stock should not be used,
- removed / zeroed records remain visible for traceability.

---

## 6. Delivery Plan Data

Delivery plans define expected inbound stock.

A delivery plan can include:

- delivery number,
- PN,
- description,
- planned quantity,
- received quantity,
- supplier pallet reference,
- supplier box reference,
- inbound country configuration,
- pallet prefix,
- box prefix,
- creation date,
- closing date,
- delivery status.

The delivery plan is used as the baseline for receiving validation.

The system compares what was planned against what was physically received.

This supports controlled receiving and discrepancy reporting.

---

## 7. Delivery History

The system stores delivery archive records separately from active delivery work.

Delivery history can contain archived delivery data and discrepancy reports.

This allows the system to keep a trace of closed deliveries without relying only on the current operational state.

The goal is to preserve evidence of what was planned, what was received and what discrepancies existed when the delivery was closed.

---

## 8. Picking Plan Tables

Picking uses dedicated planning tables.

The picking plan structure separates:

```text
Picking plan header
Picking plan items
Picking shortages
```

The picking plan header stores the order number, plan status, creation time and creator.

Picking plan items store the planned source records:

- plan ID,
- source box ID,
- source pallet,
- source box,
- PN,
- description,
- destination country,
- quantity to pick,
- remaining quantity,
- pallet number,
- box number,
- item status.

Picking shortages store missing quantities when the order cannot be fully covered from available stock.

This prevents shortages from being hidden.

---

## 9. Picking Area

The picking area represents stock that has already moved out of active warehouse stock.

A picking area record can store:

- order number,
- destination country,
- PN,
- description,
- quantity,
- source box ID,
- picked box ID,
- source pallet label,
- source box label,
- current status,
- picked timestamp,
- picked operator,
- outbound pallet label,
- outbound box label,
- packed timestamp,
- packed operator.

This table connects picking and packing.

The process stage is:

```text
Active stock
→ Picking area
→ Packed outbound structure
```

This design allows the system to track the operational movement of stock instead of only changing totals.

---

## 10. Outbound Structure

Outbound preparation is stored separately from warehouse storage locations.

The system supports outbound pallet and outbound box assignment.

Outbound data can include:

- outbound pallet label,
- destination country,
- shipment key,
- pallet dimensions,
- pallet weight,
- creation date,
- creator.

This is important because internal warehouse pallets and outbound shipment pallets are not the same thing.

A product can be stored on one warehouse pallet, picked from that pallet and later packed into a different outbound pallet or standalone outbound box.

---

## 11. Audit History Table

The history table is the main operational audit trail.

It stores information such as:

- PN,
- expected quantity,
- received quantity,
- pallet label,
- pallet prefix,
- country code,
- pallet number,
- box label,
- box number,
- status,
- operation date,
- operation time,
- operator,
- operation type,
- operation details,
- station,
- source file.

This allows the system to answer:

```text
What changed?
When did it change?
Who changed it?
Which PN was affected?
Which pallet and box were affected?
Which status was recorded?
Which station performed the operation?
What was the source of the operation?
```

Audit history is used across receiving, picking, packing, missing handling and corrections.

---

## 12. Database-Level Audit Trigger

The system uses a database-level trigger to record important stock record changes.

When critical fields in a stock record are updated, the trigger writes a correction entry to history.

Tracked fields include:

- PN,
- quantity,
- box number,
- box prefix,
- pallet number,
- pallet prefix,
- country code,
- status.

This is important because corrections should not rely only on UI-level logging.

If a critical stock field changes, the database can create an audit entry automatically.

This helps protect traceability for administrative stock corrections.

---

## 13. Manual Logistic History Entries

Not every audit event is only a stock correction.

Many process actions are written to history as explicit logistic events.

Examples include:

```text
PICKING
PICKING_MISSING_REASSIGNED
PN_FOUND_OUTSIDE_STOCK
PACKING
PACKING_RETURN_TO_PICKED
PACKING_TRANSFER
PACKING_LABEL_EDIT
```

These events describe operational actions, not only raw database updates.

For example, when an item is reassigned during Missing handling, the history can store:

- original source pallet,
- original source box,
- replacement source pallet,
- replacement source box,
- quantity,
- operator,
- station,
- order number,
- operation type.

This makes the audit useful for real warehouse problem solving.

---

## 14. Stock Recalculation

The current stock summary is recalculated from detailed stock records.

The recalculation uses only active stock statuses:

```text
P
EX
DA
```

It ignores process and exception statuses such as:

```text
PENDING_* = pending stock waiting for release
MISSING   = stock not physically found in expected location
PICKED    = stock already moved into picking area
PACKED    = stock already packed
USUNIĘTY  = removed / zeroed record kept for traceability
```

This prevents inactive or process-stage records from inflating available stock.

The system can rebuild current stock after correction, receiving, picking and packing operations.

---

## 15. Correction Safety

The system supports stock record correction, but correction is treated as an administrative action.

A stock record correction can change:

- PN,
- quantity,
- country,
- status,
- pallet number,
- pallet prefix,
- box number,
- box prefix,
- full pallet label,
- full box label.

If a record is removed from active use, it is not silently deleted from traceability.

It can be marked as:

```text
USUNIĘTY = removed / zeroed stock record kept for traceability
```

and quantity can be set to zero.

This keeps the record visible in audit history while excluding it from active stock.

---

## 16. Picking Audit

Picking creates history entries when stock is moved from active stock into the picking area.

The audit records:

- PN,
- picked quantity,
- source pallet,
- source box,
- destination country,
- operator,
- station,
- source file,
- operation type.

This allows the system to trace which stock was used for an order.

Picking history is important because the same PN can exist in multiple boxes and pallets.

The audit trail helps identify exactly which physical source was used.

---

## 17. Missing Audit

Missing handling has dedicated audit logic.

When a planned source is not physically found, the system records the missing event and the reassignment.

The audit trail can show:

- planned source box,
- missing source,
- replacement source,
- quantity,
- PN,
- order number,
- operator,
- station.

This is important because the warehouse still needs to know that the original location was wrong, even if the order was later fulfilled from another source.

Without audit, the missing event would disappear after reassignment.

---

## 18. Product Found Outside Stock Audit

The system supports controlled handling of products found physically but not available in system stock.

This action is recorded separately from normal picking.

The audit trail shows that the product was added because it matched an active shortage, not because the operator freely created stock.

This protects the process from uncontrolled stock injection.

---

## 19. Packing Audit

Packing writes history when picked stock is assigned to outbound structure.

The audit can include:

- PN,
- packed quantity,
- source pallet,
- source box,
- outbound pallet,
- outbound box,
- destination country,
- operator,
- station,
- operation type.

This connects the picked item to its final outbound structure.

It also supports later investigation if an item was packed into the wrong outbound pallet or box.

---

## 20. Packed Correction Audit

Packed corrections are audit-sensitive operations.

The system records actions such as:

```text
Return PACKED to PICKED
Transfer PACKED between outbound structures
Edit packed outbound labels
```

The audit trail can include:

- source packed record,
- target packed record,
- transferred quantity,
- old outbound pallet,
- old outbound box,
- new outbound pallet,
- new outbound box,
- source country,
- target country,
- operator,
- station.

This allows outbound corrections without losing traceability.

---

## 21. User and Permission Tables

The database also stores user and permission data.

The system supports role-based access control with roles such as:

```text
ADMIN
SUPERVISOR
LEADER
OPERATOR
VIEWER
```

Permissions are stored separately from roles so access can be controlled more precisely.

Permission areas include:

- current stock,
- export,
- worker performance,
- orders,
- deliveries,
- receiving,
- picking,
- packing,
- stock corrections,
- packed corrections,
- user management,
- admin tools.

This is important because correction and administration functions should not be available to every warehouse user.

---

## 22. Authentication and Admin Events

The system records authentication and admin-related events.

Examples include:

- successful login,
- failed login,
- logout,
- user creation,
- role change,
- permission change,
- password reset,
- user activation or deactivation.

These events are separate from warehouse stock history.

This keeps operational audit and admin audit logically separated.

---

## 23. Session and Station Context

The system tracks user and station context.

A station is required for operational actions such as picking and packing.

This matters because warehouse traceability should identify not only who performed the action, but also from which workstation or physical station the action was executed.

The audit model can therefore include:

```text
operator
station
timestamp
operation type
details
```

This makes the history more useful for shift-level and process-level investigation.

---

## 24. Data Integrity Approach

The system uses several mechanisms to protect data integrity:

- validation before inserts,
- status-based process control,
- transaction blocks for critical operations,
- current stock recalculation,
- audit history writes,
- role-based access control,
- station requirement for operational actions,
- separation of active stock from process-stage records.

The database model avoids treating stock as one simple number.

Instead, it treats each stock record as part of a process.

This is the key difference between a simple inventory spreadsheet and an operational warehouse system.

---

## 25. What the Database Design Demonstrates

The database and audit design demonstrate practical warehouse logic:

```text
Stock can be received but not released.
Stock can exist but not be pickable.
Stock can be picked but not packed.
Stock can be packed but still corrected.
Stock can be missing from the expected source.
Stock can be found physically outside system stock.
Stock can be removed from active use without deleting traceability.
```

This model supports real warehouse exceptions instead of assuming perfect data.

---

## 26. Portfolio Note

The public repository does not include real operational data.

The database file, backups, logs and real import files are excluded from version control.

The repository is intended to show:

- application logic,
- database structure,
- process design,
- audit approach,
- correction handling,
- permission model,
- operational thinking.

Real warehouse data should never be committed to the public repository.

---

<div align="center">

**WMS-lite – Warehouse Operations System**  
A practical warehouse operations system focused on database-backed stock flow, audit history, process control and traceable corrections.

</div>
