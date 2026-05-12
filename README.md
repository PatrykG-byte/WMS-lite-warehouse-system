<h1 align="center">WMS-lite – Warehouse Operations System</h1>

WMS-lite is a local warehouse operations system built with **Python**, **Streamlit** and **SQLite**.

The project was created to support real warehouse workflows such as inbound receiving, current stock control, picking, packing, outbound preparation, missing/extra/damage handling and operational audit history.

This repository is a portfolio/demo version. Production data, real warehouse records, user credentials, database backups and confidential identifiers are not included.

---

## Problem

Warehouse stock and operational statuses are difficult to control using only spreadsheets.

Manual tracking can lead to mistakes during receiving, picking, packing and correction handling, especially when the same product can move through multiple process states such as pending, available stock, picked, packed, missing, extra or damaged.

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
- handling operational exceptions,
- reviewing audit history.

The application separates physical stock from operational workflow statuses, making it easier to understand where each item is and what action was performed.

---

## Main Features

- Inbound delivery receiving
- Delivery plan import
- Current stock overview
- Stock status management
- Picking plan creation
- Picking execution
- Packing and outbound preparation
- Missing item handling
- Extra stock handling
- Damage stock handling
- Packing list support
- User roles and permissions
- Audit history for stock corrections
- Local SQLite database
- Streamlit user interface

---

## Stock Status Model

The system uses warehouse statuses to separate physical stock from workflow state.

Examples of statuses used in the application:

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

### 2. Stock Control

The system tracks product numbers, quantities, countries, pallet labels, box labels and warehouse statuses.

Current stock is calculated from database records and filtered by active stock statuses.

### 3. Picking

The application supports picking plan creation based on available stock.

Picking logic uses available stock records and creates operational picking records for warehouse execution.

### 4. Packing

Picked items can be packed into outbound pallets and boxes.

The system supports assigning packed items to outbound labels and keeping packing data connected with the original picking flow.

### 5. Problem Solving and Exception Handling

The system supports warehouse problem solving workflows for cases where the standard process does not match the physical stock situation.

Examples of supported exception scenarios:

- missing stock during picking,
- extra stock received outside the delivery plan,
- damaged stock handling,
- product found outside the expected stock location,
- correction of product records,
- correction of packed outbound labels,
- returning packed items back to picked status,
- splitting picked or packed quantities between outbound boxes or pallets.

Problem solving actions are connected with audit history, so operational corrections are traceable and not hidden as silent data changes.

### 6. Audit History

Operational changes are logged to provide traceability for stock corrections and status changes.

The audit history helps track what was changed, when it was changed and which operator performed the action.

---

## User Roles and Permissions

The system includes role-based access control.

Example roles:

- `ADMIN`
- `SUPERVISOR`
- `LEADER`
- `OPERATOR`
- `VIEWER`

Example permissions:

- view current stock,
- export data,
- view deliveries,
- receive deliveries,
- view picking,
- run picking,
- view packing,
- run packing,
- edit product records,
- manage users,
- access admin tools.

---

## Tech Stack

- Python
- Streamlit
- SQLite
- pandas
- SQL
- Local database architecture

---

## Project Structure

Planned portfolio structure:

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
