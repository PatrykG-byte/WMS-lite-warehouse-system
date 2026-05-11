# Database Overview

The system uses a local SQLite database.

The database stores operational records for:

- stock items
- pallets
- boxes
- delivery plans
- picking plans
- picking shortages
- packing records
- outbound pallets and boxes
- users and permissions
- audit history

The database structure separates physical stock from operational workflow statuses.

This helps track whether an item is available, pending, picked, packed, missing, damaged or extra.
