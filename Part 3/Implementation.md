## Part 3: Low Stock Alerts API (Simple Logic)

**Endpoint:** `GET /api/companies/{company_id}/alerts/low-stock`

### Assumptions
- Recent sales activity = at least 1 sale in the last 30 days
- Low stock threshold depends on product type
- Stock is tracked separately for each warehouse
- Supplier details are fetched using supplier-product mapping (if available)

### Pseudocode
1. Get all warehouses for the given company.
2. For each warehouse, fetch inventory (product + current stock).
3. For each product:
   - Check if it has recent sales activity (last 30 days).
   - Fetch threshold based on product type.
   - If current stock is below threshold → create an alert.
   - Attach supplier details (if available).
4. Return all alerts and total count.
