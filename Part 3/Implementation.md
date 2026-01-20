## Part 3: Low Stock Alerts API (Pseudocode)

**Endpoint:** `GET /api/companies/{company_id}/alerts/low-stock`

### Assumptions
- Recent sales activity = at least 1 sale in the last 30 days
- Threshold depends on product type (stored in DB or decided using simple rules)
- Stock is tracked per warehouse
- Supplier is fetched from supplier-product mapping

### Pseudocode
FUNCTION getLowStockAlerts(company_id):

    alerts = []

    warehouses = GET warehouses WHERE company_id = company_id
    IF no warehouses:
        RETURN { alerts: [], total_alerts: 0 }

    FOR each warehouse:
        inventory_rows = GET inventory rows for this warehouse

        FOR each inventory row:
            product = GET product details

            IF product has no recent sales (last 30 days):
                CONTINUE

            threshold = GET threshold based on product type
            IF threshold missing:
                threshold = default value

            IF current_stock >= threshold:
                CONTINUE

            supplier = GET supplier details for this product (if available)

            ADD to alerts:
                {
                  product_id,
                  product_name,
                  sku,
                  warehouse_id,
                  warehouse_name,
                  current_stock,
                  threshold,
                  supplier
                }

    RETURN { alerts: alerts, total_alerts: count(alerts) }
