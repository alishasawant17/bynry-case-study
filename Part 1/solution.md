1. SKU uniqueness not checked:
   Issue- sku = data['sku']----> doesn't check if sku already exists
   Impact- Two products can get get same SKU which may result in inventory mismatch, billing confusion
   Fix- check DB if SKU exists

2. Product incorrectly tied to a warehouse:
   Issue- warehouse_id = data['warehouse_id']----> inside product model
   Impact- You can't store same product in multiple warehouses.
   Fix- Remove warehouse_id from product model

3. Two commits
   Issue- product model is commited first then inventory
   Impact- If inventory insert fails after product commit, then product gets created without inventory.
   Fix- Commit once or rollback if anything fails.

4. No validation for missing/optional field
   Issue- They directly access: data['name'], data['initial_quantity']
   Impact- If field missing → KeyError → API crashes.
   Fix- Use .get() and validate required fields properly.

5. No error handling
   Issue- No try/except, no rollback.
   Impact- Any DB error = crash + half saved data.
   Fix- Wrap with try/except and rollback on failure.


✅ Corrected Flow (Improved Logic)

1. **Validate request JSON + required fields**
   - Use `data.get()` to avoid crash if fields are missing.

2. **Check SKU uniqueness**
   - If SKU already exists in DB → return error (duplicate SKU).

3. **Create Product**
   - Create product using `name`, `sku`, `price`.
   - Do NOT store `warehouse_id` inside `Product`.

4. **Create Inventory entry (separately from Product)**
   - Use `product_id` + `warehouse_id` + `initial_quantity`.
   - `initial_quantity` should be optional → default to `0`.

5. **Commit once (single transaction)**
   - If anything fails → rollback (no half-saved data).

6. **Return success response**
   - Return `201 Created` with `product_id`.

