# Problem Solving and Stock Corrections

Problem solving is used when the physical warehouse situation does not match the planned process or the recorded system data.

## Supported Scenarios

- Missing item during picking
- Reassignment from an alternative stock source
- Product found outside expected stock
- Extra stock
- Damaged stock
- Full stock record correction
- Pallet number correction
- Box number correction
- Quantity correction
- Country/status correction
- Return from PACKED to PICKED
- Transfer between outbound pallets or boxes

## Missing During Picking

If an item is missing from the expected pallet or box, the system can mark the original source as MISSING and allow reassignment from another available source.

This keeps both facts visible:

- where the item was supposed to be,
- where it was actually picked from.

## Stock Record Correction

Authorized users can correct stock records when the system data does not match the physical warehouse situation.

Correction may include:

- PN,
- quantity,
- country,
- status,
- pallet number,
- pallet prefix,
- box number,
- box prefix.

Corrections are written to audit history and are not hidden manual edits.

## Packed Item Correction

The system supports controlled corrections after packing:

- return PACKED quantity back to PICKED,
- transfer packed quantity to another outbound pallet,
- transfer packed quantity to another outbound box,
- split quantity when only part of the packed record needs correction.

## Audit

Problem solving actions are connected with audit history.

The system records:

- operator,
- station,
- quantity,
- source,
- target,
- operation type,
- details of the correction.
