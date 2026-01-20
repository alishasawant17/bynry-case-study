## Part 1: Identify Issues (API Debugging)

1. **SKU uniqueness not checked**
   - **Issue:** `sku = data['sku']` is used directly without checking if the SKU already exists.
   - **Impact:** Two products can get the same SKU, causing inventory mismatch, wrong product mapping, and billing confusion.
   - **Fix:** Check the database for existing SKU before inserting a new product. Return an error if SKU already exists.

2. **Product incorrectly tied to a warehouse**
   - **Issue:** `warehouse_id = data['warehouse_id']` is stored inside the Product model.
   - **Impact:** The same product cannot be stored in multiple warehouses (violates business requirement).
   - **Fix:** Remove `warehouse_id` from the Product table/model. Store warehouse mapping only in the Inventory table.

3. **Two commits (not atomic)**
   - **Issue:** Product is committed first, then inventory is committed separately.
   - **Impact:** If inventory insert fails after product commit, the product gets created without inventory (inconsistent data).
   - **Fix:** Commit once as a single transaction, or rollback if anything fails.

4. **No validation for missing/optional fields**
   - **Issue:** Code directly accesses `data['name']`, `data['initial_quantity']`, etc.
   - **Impact:** If a field is missing, it raises `KeyError` and the API crashes.
   - **Fix:** Use `data.get()` and validate required fields properly. Use defaults for optional fields.

5. **No error handling**
   - **Issue:** No `try/except` block and no rollback on failure.
   - **Impact:** Any database error can crash the API and leave half-saved data.
   - **Fix:** Wrap logic in `try/except` and rollback the DB session if an error occurs.


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

