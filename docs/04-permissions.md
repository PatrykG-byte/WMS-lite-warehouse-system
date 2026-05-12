# Permissions and User Roles

The system includes role-based access control for warehouse operations.

Permissions are used to separate daily execution from administrative and correction actions.

## Example Roles

| Role | Purpose |
|---|---|
| ADMIN | Full access, user management and technical tools |
| SUPERVISOR | Operational control, corrections and process supervision |
| LEADER | Warehouse process control |
| OPERATOR | Daily warehouse execution |
| VIEWER | Read-only access |

## Example Permission Areas

| Area | Example Permissions |
|---|---|
| Current Stock | view current state, export data |
| Deliveries | view deliveries, create deliveries, receive deliveries |
| Picking | view picking, run picking, add product found outside stock |
| Packing | view packing, run packing |
| Corrections | edit stock records, return packed to picked, edit packing list |
| Admin | manage users, access admin tools, clear cache |

## Why Permissions Matter

Warehouse systems need different access levels because not every user should be able to correct stock, edit packed items or manage users.

For example:

- operators can execute picking and packing,
- leaders can control process flow,
- supervisors can handle corrections,
- admins can manage users and technical settings.

This reduces the risk of accidental or unauthorized changes in stock data.
