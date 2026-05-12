<h1 align="center">WMS-lite – Warehouse Operations System</h1>

<p align="center">
Local warehouse operations system for inbound, stock control, picking, packing, problem solving and audit history.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.x-blue" />
  <img src="https://img.shields.io/badge/Streamlit-App-red" />
  <img src="https://img.shields.io/badge/SQLite-Local%20Database-lightgrey" />
  <img src="https://img.shields.io/badge/Status-Portfolio%20Demo-orange" />
</p>

---

## Overview

**WMS-lite** is a local warehouse operations system built with **Python**, **Streamlit** and **SQLite**.

The project was created to support real warehouse workflows such as inbound receiving, current stock control, picking, packing, outbound preparation, problem solving, stock record correction, missing/extra/damage handling and operational audit history.

This repository is a **portfolio/demo version**. Production data, real warehouse records, user credentials, database backups and confidential identifiers are not included.

---

## Problem

Warehouse stock and operational statuses are difficult to control using only spreadsheets.

Manual tracking can lead to mistakes during receiving, picking, packing and correction handling, especially when the same product can move through multiple process states such as:

- pending,
- available stock,
- picked,
- packed,
- missing,
- extra,
- damaged,
- corrected or removed.

In real warehouse work, the physical situation often does not match the planned process. A product may be missing from the expected box, found in another location, received as extra, damaged, packed incorrectly or recorded with the wrong pallet, box, quantity, country or status.

The goal of this project was to replace manual spreadsheet-based tracking with a structured operational tool for daily warehouse work.

---

## Solution

WMS-lite centralizes warehouse operations in one local application.

The system stores warehouse data in a local SQLite database and provides workflows for:

- receiving deliveries,
- controlling current stock,
- creating picking plans,
- executing picking,
- packing items,
- preparing outbound pallets and boxes,
- handling warehouse exceptions,
- correcting stock records,
- reviewing audit history.

The application separates physical stock from operational workflow statuses, making it easier to understand where each item is, what state it is in and what action was performed.

---

## Main Features

| Area | Features |
|---|---|
| Inbound | Delivery plan import, receiving flow, pending stock handling |
| Stock | Current stock overview, status model, stock recalculation |
| Picking | Picking plan creation, picking execution, shortages |
| Packing | Packing flow, outbound pallet/box assignment, packing list support |
| Problem Solving | Missing, Extra, Damage, product found outside stock |
| Corrections | Pallet, box, quantity, country, status and PN correction |
| Audit | History of operational corrections and status changes |
| Access Control | User roles and permissions |
| Database | Local SQLite database |

---

## Stock Status Model

The system uses warehouse statuses to separate physical stock from workflow state.

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
| `USUNIĘTY` | Removed/corrected record |

---

## Core Workflows

### 1. Inbound Receiving

Delivery plans can be imported and used as the basis for receiving goods into the system.

The receiving flow supports planned quantities, received quantities, delivery numbers, product numbers, pallets, boxes and target stock status.

---

### 2. Stock Control

The system tracks:

- product numbers,
- quantities,
- countries,
- pallet labels,
- box labels,
- warehouse statuses.

Current stock is calculated from database records and filtered by active stock statuses.

---

### 3. Picking

The application supports picking plan creation based on available stock.

Picking logic uses available stock records and creates operational picking records for warehouse execution.

---

### 4. Packing

Picked items can be packed into outbound pallets and boxes.

The system supports assigning packed items to outbound labels and keeping packing data connected with the original picking flow.

---

### 5. Problem Solving and Exception Handling

Problem solving is used when the standard warehouse process does not match the physical stock situation.

The system supports exception scenarios such as:

- missing item during picking,
- reassignment from an alternative stock source,
- product found outside expected stock,
- extra stock,
- damaged stock,
- packed item correction,
- return from `PACKED` to `PICKED`,
- transfer of packed quantity between outbound pallets or boxes.

Problem solving actions update operational statuses and quantities instead of being handled as comments or manual notes.

---

### 6. Stock Record Correction

The system supports controlled correction of warehouse stock records.

Authorized users can correct operational data when the recorded stock does not match the physical warehouse situation.

This includes correcting:

- product number,
- quantity,
- country,
- status,
- pallet number,
- pallet prefix,
- box number,
- box prefix.

Corrections are not handled as silent manual edits. The system updates the stock record, recalculates current stock where required and writes old/new values to audit history.

This makes operational corrections traceable and helps prevent hidden changes in warehouse data.

---

### 7. Audit History

Operational changes are logged to provide traceability for stock corrections and status changes.

The audit history helps track:

- what was changed,
- when it was changed,
- which operator performed the action,
- which stock record was affected,
- what the previous and new values were.

---

## Problem Solving Examples

### Missing During Picking

If an item is missing from the expected pallet or box during picking, the original source can be marked as `MISSING`.

The required quantity can then be reassigned from another available stock source.

This keeps both facts visible:

- where the item was supposed to be,
- where it was actually picked from.

---

### Product Found Outside Stock

If a product is physically found outside the expected stock location, it can be added to the picking flow only when it matches an existing shortage in the active picking plan.

This prevents random products from being added to an order without a valid operational reason.

---

### Packed Item Correction

If an item was packed incorrectly, the system supports controlled corrections such as:

- returning packed quantity back to `PICKED`,
- moving packed quantity to another outbound pallet,
- moving packed quantity to another outbound box,
- splitting packed quantity when only part of the record needs correction.

---

### Stock Data Correction

If the warehouse record contains wrong data, authorized users can correct the stock record.

Examples:

- wrong pallet number,
- wrong box number,
- wrong quantity,
- wrong country,
- wrong status,
- wrong product number.

These corrections are written to audit history and are not hidden manual changes.

---

## User Roles and Permissions

The system includes role-based access control.

Example roles:

| Role | Purpose |
|---|---|
| `ADMIN` | Full system access and user management |
| `SUPERVISOR` | Operational control and corrections |
| `LEADER` | Warehouse process control |
| `OPERATOR` | Daily warehouse execution |
| `VIEWER` | Read-only access |

Example permissions:

- view current stock,
- export data,
- view deliveries,
- receive deliveries,
- view picking,
- run picking,
- view packing,
- run packing,
- edit stock records,
- manage users,
- access admin tools.

---

## Tech Stack

| Technology | Use |
|---|---|
| Python | Application logic |
| Streamlit | User interface |
| SQLite | Local database |
| pandas | Data processing |
| SQL | Queries, stock calculations and reporting |

---

## Project Structure

Recommended portfolio structure:

```text
wms-lite-warehouse-system/
│
├── app.py
├── README.md
├── requirements.txt
├── .gitignore
│
├── docs/
│   ├── system-overview.md
│   ├── database-model.md
│   ├── workflows.md
│   └── limitations-and-roadmap.md
│
├── screenshots/
│   ├── current-stock.png
│   ├── delivery-flow.png
│   ├── picking-flow.png
│   ├── packing-flow.png
│   └── audit-history.png
│
└── sample_data/
    ├── sample_delivery_plan.csv
    └── sample_order.csv
