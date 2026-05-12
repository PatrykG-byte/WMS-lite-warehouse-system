<div align="center">

# Workflows

### Operational flow of WMS-lite – Warehouse Operations System

</div>

---

## Overview

This document describes the main warehouse workflows supported by **WMS-lite – Warehouse Operations System**.

The system is designed around real warehouse operations, not only around inventory visibility. It supports the flow of stock from inbound receiving, through stock control, picking, packing, outbound preparation, problem solving and audit history.

The current implementation is a functional . Most business logic, database operations and Streamlit UI are handled inside `app.py`. This is a known limitation of the portfolio version, but it also shows the full operational scope of the system in one working application.

The goal of this project is to demonstrate practical warehouse process control:

- receiving stock against a delivery plan,
- keeping pending stock separated from active stock,
- controlling available stock by status,
- generating picking plans,
- executing picking by pallet and box,
- handling missing items,
- handling products found outside stock,
- packing picked items,
- assigning packed items to outbound pallet / box structures,
- correcting packed records,
- correcting stock records,
- keeping audit history.

---

## 1. End-to-End Warehouse Flow

The main process flow is:

```text
Delivery plan
→ Inbound receiving
→ Pending stock
→ Delivery discrepancy review
→ Stock release
→ Current stock
→ Outbound order
→ Picking plan
→ Picking execution
→ Missing / shortage handling
→ PICKED
→ Packing
→ PACKED
→ Outbound pallet / box assignment
→ Packing list review
→ Corrections if required
→ Audit history
```

This flow is important because warehouse operations are not clean by default.

Products can be missing. Extra stock can appear. Damaged items must be separated. Operators may assign items to the wrong outbound box. Packed quantities may need to be split or moved. Stock records may require correction.

WMS-lite was built to support those cases instead of hiding them.

---

## 2. Inbound Delivery Workflow

Inbound starts with a delivery plan.

The delivery plan defines what should arrive in the warehouse:

- delivery number,
- product number,
- description,
- expected quantity,
- supplier pallet reference,
- supplier box reference,
- source country / inbound country configuration,,
- pallet prefix,
- box prefix.

The operator receives items against that plan. This prevents uncontrolled manual stock creation.

During receiving, the system validates:

- whether the delivery exists,
- whether the product number belongs to the delivery,
- whether quantity is valid,
- whether pallet and box labels match the configured prefixes,
- whether stock is normal, damaged or extra.

Received stock is not immediately released into active stock.

Instead, it is saved as pending stock:

```text
PENDING_P  = pending normal stock
PENDING_DA = pending damaged stock
PENDING_EX = pending extra stock
```

This creates a safety buffer between physical receiving and available warehouse stock.

Pending records allow the team to review discrepancies before the stock becomes available for picking.

---

## 3. Delivery Discrepancy / GAP Workflow

After receiving, the system can compare the delivery plan against received records.

The purpose is to answer a simple operational question:

```text
Did we receive what we expected?
```

The system compares:

- planned quantity,
- received OK quantity,
- received damage quantity,
- received extra quantity,
- missing quantity,
- over-received quantity,
- products received outside the original list.

The GAP workflow can identify:

```text
OK
SHORTAGE
OVERAGE
QTY OK / DAMAGE
EXTRA OUTSIDE LIST
```

Items received outside the delivery plan are separated as:

```text
EXTRA OUTSIDE LIST
```

This is important because extra products should not silently modify the original plan.

The discrepancy workflow gives the warehouse team a control point before stock is released into active inventory.

---

## 4. Stock Release Workflow

Stock release promotes pending records into active warehouse stock.

Pending statuses are converted into operational stock statuses:

```text
PENDING_P  → P
PENDING_DA → DA
PENDING_EX → EX
```

Only after this step can the stock be used by downstream workflows.

This design prevents unfinished receiving work from polluting the available stock view.

It also creates a clear separation between:

```text
physically received
```

and:

```text
released for operational use
```

That separation is important in real warehouse environments, where receiving and stock availability are not always the same moment.

---

## 5. Current Stock Workflow

The current stock view is calculated from database records.

The system counts only active stock statuses:

```text
P
EX
DA
```

The following statuses are not treated as active available stock:

```text
PENDING    = received or created record waiting for final classification
PENDING_P  = received stock waiting for release as normal stock
PENDING_DA = received stock waiting for release as damaged stock
PENDING_EX = received stock waiting for release as extra stock
MISSING    = stock expected in a source location but not physically found
PICKED     = stock already moved from warehouse stock into picking area
PACKED     = stock already packed into outbound shipment structure
REMOVERD   = removed / zeroed stock record kept for traceability
```

This means the current stock view shows stock that is still physically available for warehouse operations.

The system tracks stock by:

- PN,
- country,
- status,
- quantity,
- pallet label,
- pallet prefix,
- pallet number,
- box label,
- box prefix,
- box number,
- source file / source record.

The stock model is based on pallet and box records, not only product totals.

This allows the system to answer:

```text
How many pieces do we have?
```

but also:

```text
Where are they?
On which pallet?
In which box?
Under which status?
```

That structure is required for picking, packing, problem solving and audit history.

---

## 6. Outbound Order Workflow

Outbound starts with an order file.

The order defines:

- destination country,
- PN,
- description,
- required quantity.

The system validates the order file before using it.

Validation includes:

- missing country,
- missing PN,
- missing quantity,
- invalid quantity,
- non-positive quantity.

After validation, the order can be used to generate a picking plan.

The order workflow is not only a data import step. It is the starting point for controlled outbound execution.

---

## 7. Picking Plan Workflow

The picking plan decides which physical stock records should be used for an outbound order.

The system searches available stock by PN and selects boxes that can satisfy the required quantity.

Pickable statuses are:

```text
P
EX
```

The system does not pick from:

```text
PENDING_*
DA
PICKED
PACKED
MISSING
USUNIĘTY
```

This prevents operators from picking stock that is not operationally available.

The picking plan includes:

- order number,
- PN,
- destination country,
- source pallet,
- source box,
- source box ID,
- quantity to pick,
- remaining quantity,
- item status.

If there is not enough stock, the system records a shortage instead of hiding the problem.

This makes shortages visible before and during picking.

---

## 8. Picking Execution Workflow

Picking execution moves stock from active warehouse stock into the picked area.

The system does not only mark a PN as picked. It connects the picked quantity to a real source box.

During picking, the system records:

- order number,
- destination country,
- PN,
- description,
- picked quantity,
- source box ID,
- picked box ID,
- source pallet label,
- source box label,
- operator,
- station,
- timestamp.

If the full source box quantity is picked, the original stock record changes to:

```text
PICKED
```

If only part of the source box is picked, the system keeps the remaining quantity in stock and creates a separate picked record for the picked quantity.

Example:

```text
Source box quantity: 5
Picked quantity: 2
Remaining stock quantity: 3
New PICKED record: 2
```

This preserves quantity integrity.

The system knows exactly what stayed in stock and what moved into the picked area.

---

## 9. Missing During Picking Workflow

Missing during picking means the system expected stock in a specific source box, but the operator could not physically find it there.

This is handled as a controlled exception.

The workflow is:

```text
Operator selects planned picking row
→ operator marks item as Missing
→ system shows alternative stock sources
→ operator selects replacement source
→ original source is marked as MISSING
→ replacement source is picked
→ picking area receives the reassigned item
→ audit history records the reassignment
```

This preserves two important facts:

1. The original planned source was unavailable.
2. The order was fulfilled from another valid stock source.

The operation is stored as a dedicated event:

```text
PICKING_MISSING_REASSIGNED
```

This is stronger than a simple note, because the system keeps traceability of both the failed source and the replacement source.

---

## 10. Product Found Outside Stock Workflow

Sometimes the warehouse physically finds a product that is not currently available in system stock.

The system supports this case, but with control.

A found product can be added only when it matches a shortage in an active order.

This prevents operators from adding random stock into picking.

The workflow is:

```text
Order has shortage
→ operator finds matching PN physically
→ operator enters PN and quantity
→ system checks active shortage
→ quantity is added to picking area
→ shortage quantity is reduced
→ audit history records the event
```

This supports real problem solving without breaking traceability.

The operation is tracked separately from normal picking as a product found outside stock.

---

## 11. Packing Workflow

Packing starts from picked records.

The system does not pack directly from active stock.

The process stage is:

```text
Stock
→ PICKED
→ PACKED
```

During packing, the operator assigns picked items to outbound handling units:

- outbound pallet,
- outbound box,
- standalone outbound box.

Standard packing requires:

- selected picked item,
- outbound box,
- outbound pallet unless standalone box mode is used,
- operator,
- station.

Outbound pallets are separated from warehouse storage pallets.

This is important because internal storage structure and outbound shipment structure are not the same thing.

---

## 12. Partial Packing Workflow

The system supports splitting picked quantity during packing.

This is needed when a picked record contains multiple pieces, but only part of that quantity should be packed now.

Example:

```text
Picked quantity: 3
Packed now: 1
Remaining PICKED quantity: 2
```

The system keeps the remaining quantity as PICKED and creates a PACKED record for the packed quantity.

This prevents forcing the operator to pack the whole picked quantity at once.

Partial packing is important because outbound shipment structure often changes after picking.

---

## 13. Outbound Pallet / Box Assignment Workflow

Packed records are assigned to outbound pallet and box structures.

The system supports:

- normal outbound pallet + box assignment,
- standalone outbound box assignment,
- review by shipment,
- review by destination country,
- review by outbound pallet,
- review by outbound box.

This workflow answers practical outbound questions:

```text
What is packed?
For which country?
On which outbound pallet?
In which outbound box?
What quantity is assigned?
```

Outbound assignment is part of the operational record, not only a visual label.

That means it can also be audited and corrected.

---

## 14. Packing List Review Workflow

The packing list is generated from PACKED records.

It can include:

- PN,
- description,
- quantity,
- destination country,
- outbound box,
- outbound pallet,
- pallet dimensions,
- pallet weight.

This makes the packing list useful for outbound control and shipment preparation.

The review workflow supports shipments that contain multiple countries, pallets and boxes.

This is important because outbound preparation is often more complex than a single final pallet.

---

## 15. Packed Item Correction Workflow

Packed items sometimes need correction.

Examples:

- item packed to the wrong outbound box,
- item packed to the wrong outbound pallet,
- quantity packed incorrectly,
- part of the quantity must be moved,
- item must be returned from PACKED to PICKED,
- outbound structure changed after packing.

The system supports correction workflows instead of direct manual database edits.

Correction options include:

```text
Return PACKED to PICKED
Transfer PACKED between outbound pallet / box
Edit packed outbound labels
```

These operations require dedicated permissions.

They are also written to audit history.

---

## 16. Return PACKED to PICKED Workflow

If an item was packed incorrectly, the system can return quantity from PACKED back to PICKED.

The workflow is:

```text
Select PACKED record
→ enter quantity to return
→ system validates available packed quantity
→ returned quantity becomes PICKED
→ outbound pallet / box assignment is removed for returned quantity
→ audit history records the correction
```

This is useful when the item should be repacked later.

The record is not silently deleted. The system keeps the process trail.

---

## 17. Transfer PACKED Workflow

Packed items can be transferred between outbound pallet / box structures.

The workflow is:

```text
Select PACKED record
→ choose quantity to transfer
→ choose target country if allowed
→ choose target outbound pallet
→ choose target outbound box
→ system updates or splits the PACKED record
→ audit history records source and target
```

If only part of the quantity is transferred, the source record is reduced and a new PACKED record is created for the transferred quantity.

This preserves quantity traceability.

Cross-country transfer is restricted and should be treated as an administrative action.

---

## 18. Stock Record Correction Workflow

The system includes full stock record correction.

This is broader than simple PN editing.

A stock record can be corrected by:

- PN,
- quantity,
- country,
- status,
- pallet number,
- pallet prefix,
- box number,
- box prefix,
- full pallet label,
- full box label,
- record removed / zeroed.

This workflow is used when the stock record itself is wrong or when a warehouse exception requires cleanup.

When a record should no longer count as active stock, it can be marked as:

```text
USUNIĘTY
```

and its quantity is zeroed.

The record remains traceable instead of being silently deleted.

After correction, the system recalculates current stock so that the stock view stays consistent with the database.

---

## 19. Permissions Workflow

The system uses role-based access control.

Example roles:

```text
ADMIN
SUPERVISOR
LEADER
OPERATOR
VIEWER
```

Permissions cover areas such as:

- current stock view,
- data export,
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

This is important because not every warehouse user should be able to modify stock records or perform correction actions.

Correction workflows are separated from normal operator workflows.

This makes the system closer to a real internal warehouse tool.

---

## 20. Audit History Workflow

Operational actions are recorded in history.

The system stores:

- PN,
- quantity,
- pallet,
- box,
- country,
- status,
- operator,
- operation type,
- operation details,
- station,
- source file,
- date,
- time.

Audit history is used for:

- receiving traceability,
- picking traceability,
- missing reassignment,
- product found outside stock,
- packing confirmation,
- packed corrections,
- stock record corrections,
- login and admin events.

For stock record corrections, the system uses database-level audit logic.

This means important changes to stock records are written to history automatically.

The audit trail is not only a report. It is part of the safety model of the system.

---

## 21. Why These Workflows Matter

The project is not a simple inventory tracker.

A simple inventory tracker usually answers:

```text
How many items are in stock?
```

WMS-lite answers more operational questions:

```text
Where is the stock?
Is it pending, active, picked, packed, missing or removed?
Which pallet and box contain it?
Who moved it?
Which order used it?
Was it reassigned?
Was it packed?
Was it corrected?
Is there audit history?
```

That is the main purpose of this project.

It demonstrates a practical warehouse operations system built around process control, exception handling and traceability.

---

<div align="center">

**WMS-lite – Warehouse Operations System**  
Functional monolithic prototype focused on warehouse process control, auditability and operational problem solving.

</div>
