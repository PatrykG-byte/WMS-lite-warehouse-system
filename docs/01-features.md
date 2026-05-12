# Features

This document describes the main functional areas of the WMS-lite system.

The application was designed as a local warehouse operations tool focused on inbound receiving, stock control, picking, packing, problem solving, audit history and user permissions.

---

## 1. Inbound and Delivery Receiving

The inbound module supports receiving goods based on delivery plans.

Main features:

- delivery plan import,
- delivery number tracking,
- planned quantity tracking,
- received quantity tracking,
- pallet and box assignment,
- pending stock handling,
- target stock status handling,
- extra and damaged stock classification,
- GAP comparison between planned and received quantities.

The goal of the inbound flow is to make receiving more controlled than manual spreadsheet updates.

Instead of entering loose rows into a file, the system connects received stock with a delivery number, product number, quantity, pallet, box and status.

---

## 2. Current Stock Control

The stock module gives visibility over warehouse inventory.

Main features:

- current stock overview,
- product number tracking,
- quantity tracking,
- country / destination tracking,
- pallet tracking,
- box tracking,
- status filtering,
- stock recalculation,
- separation between physical stock and operational statuses.

The system uses status-based logic to determine what is available as stock and what is already in another process stage, such as picking or packing.

This helps prevent treating picked, packed, missing or pending items as normal available stock.

---

## 3. Stock Status Management

The system uses warehouse statuses to describe the operational state of each stock record.

Example statuses:

| Status | Meaning |
|---|---|
| `P` | Normal stock |
| `EX` | Extra stock |
| `DA` | Damaged stock |
| `PENDING` | Pending stock |
| `PENDING_P` | Pending normal stock |
| `PENDING_DA` | Pending damaged stock |
| `PENDING_EX` | Pending extra stock |
| `MISSING` | Missing item |
| `PICKED` | Picked item |
| `PACKED` | Packed item |
| `USUNIĘTY` | Removed / corrected record |

This status model is one of the core parts of the system.

It allows the application to separate stock that is physically available from stock that is already assigned to a workflow or marked as an exception.

---

## 4. Picking

The picking module supports preparing and executing warehouse picking.

Main features:

- picking plan creation,
- picking execution,
- source pallet tracking,
- source box tracking,
- quantity validation,
- shortage detection,
- alternative source reassignment,
- picking area records,
- operator tracking,
- station tracking.

The system can create picking records based on available stock and then move selected quantities into the picking workflow.

When picking is executed, the system updates stock statuses and keeps a record of which pallet and box were used as the source.

---

## 5. Packing

The packing module supports packing picked items into outbound pallets and boxes.

Main features:

- packing picked items,
- outbound pallet assignment,
- outbound box assignment,
- standalone box shipping,
- partial packing,
- split quantity handling,
- packing list support,
- packed item correction,
- operator tracking,
- station tracking.

The packing flow keeps the connection between the original picked item and the outbound pallet or box.

This is important because packing is not treated as a separate manual note. It is part of the operational stock flow.

---

## 6. Problem Solving

Problem solving is used when the physical warehouse situation does not match the planned process.

Main features:

- missing item handling,
- reassignment from an alternative stock source,
- product found outside expected stock,
- extra stock handling,
- damaged stock handling,
- packed item correction,
- return from `PACKED` to `PICKED`,
- transfer between outbound pallets or boxes.

Problem solving actions update operational statuses and quantities.

They are not handled as comments or informal notes, because the system needs to keep the stock state consistent and traceable.

---

## 7. Stock Record Correction

The system supports controlled correction of warehouse stock records.

Main features:

- product number correction,
- quantity correction,
- country correction,
- status correction,
- pallet number correction,
- pallet prefix correction,
- box number correction,
- box prefix correction,
- removed / corrected record handling,
- stock recalculation after correction,
- audit history for old and new values.

This module is used when the recorded system data does not match the physical warehouse situation.

Examples:

- wrong pallet number,
- wrong box number,
- wrong quantity,
- wrong country,
- wrong status,
- wrong product number,
- record that should no longer count as active stock.

Corrections are controlled and traceable instead of being silent manual edits.

---

## 8. Audit History

The audit module records operational changes and corrections.

Main features:

- stock correction history,
- status change history,
- operator tracking,
- station tracking,
- operation type tracking,
- old/new value tracking,
- correction details.

Audit history is important because warehouse corrections should not be hidden.

The system helps answer questions such as:

- what was changed,
- when it was changed,
- who changed it,
- which pallet was affected,
- which box was affected,
- what the previous value was,
- what the new value is.

---

## 9. User Roles and Permissions

The system includes role-based access control.

Main features:

- user login,
- user roles,
- permission management,
- admin panel,
- restricted correction actions,
- restricted technical actions.

Example roles:

| Role | Purpose |
|---|---|
| `ADMIN` | Full access, user management and technical tools |
| `SUPERVISOR` | Operational control and correction handling |
| `LEADER` | Warehouse process control |
| `OPERATOR` | Daily warehouse execution |
| `VIEWER` | Read-only access |

Permissions help separate normal warehouse execution from sensitive actions such as stock correction, user management or technical maintenance.

---

## 10. Reporting and Operational Visibility

The system includes reporting and visibility features for warehouse operations.

Main features:

- current stock view,
- delivery GAP view,
- picking status visibility,
- packing status visibility,
- order progress visibility,
- audit history view,
- worker performance / operational reporting.

The goal is to give users better visibility over the warehouse process than spreadsheet-based tracking.

---

## 11. Local Database

The system uses a local SQLite database.

Main features:

- structured warehouse records,
- stock tables,
- delivery records,
- picking records,
- packing records,
- user records,
- permission records,
- audit history records.

SQLite was used because the project was designed as a local operational tool rather than a cloud SaaS application.

---

## 12. Streamlit Interface

The user interface is built with Streamlit.

Main features:

- browser-based local interface,
- operational tabs,
- forms for receiving, picking and packing,
- admin views,
- stock views,
- history views,
- role-based visibility.

Streamlit allows the application to be used as a practical internal tool without building a separate frontend framework.

---

## Summary

WMS-lite includes the following core feature areas:

| Area | Description |
|---|---|
| Inbound | Delivery receiving and pending stock handling |
| Stock | Current stock visibility and status management |
| Picking | Picking plan creation and execution |
| Packing | Packing into outbound pallets and boxes |
| Problem Solving | Missing, extra, damage and correction workflows |
| Stock Correction | Controlled correction of pallet, box, quantity, country, status and product data |
| Audit | Traceability of operational changes |
| Permissions | Role-based access control |
| Reporting | Operational visibility and warehouse process monitoring |
| Database | Local SQLite-backed data structure |
