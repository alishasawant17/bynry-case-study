###Database Schema (Text Description)

### 1) Company
**Table: companies**
- `id` (PK, integer)  
- `name` (string, not null)  
- `created_at` (timestamp)

Relationship
- One company can have many warehouses.

---

### 2) Warehouse
**Table: warehouses**
- `id` (PK, integer)  
- `company_id` (FK → companies.id, not null)  
- `name` (string, not null)  
- `location` (string, optional)  
- `created_at` (timestamp)

Relationship
- Each warehouse belongs to exactly one company.

---
### 3) Product
**Table: products**
- `id` (PK, integer)
- `company_id` (FK → companies.id, not null)
- `name` (string, not null)
- `sku` (string, not null, unique)
- `price` (decimal, not null)
- `is_bundle` (boolean, default false)
- `created_at` (timestamp)

Relationship
- A company can have many products.
- A product can exist in multiple warehouses through the inventory table.
- A product can be a bundle (if `is_bundle = true`) and contain other products through product_bundles.


### 4) Inventory (Products stored in warehouses)
**Table: inventory**
- `id` (PK, integer)  
- `product_id` (FK → products.id, not null)  
- `warehouse_id` (FK → warehouses.id, not null)  
- `quantity` (integer, not null, default 0)  
- `updated_at` (timestamp)
- 
Relationship
- This table creates a many-to-many relationship between products and warehouses:
  - One product can exist in multiple warehouses.
  - One warehouse can store multiple products.

---

### 5) Supplier
**Table: suppliers**
- `id` (PK, integer)  
- `name` (string, not null)  
- `email` (string, optional)  
- `phone` (string, optional)  
- `created_at` (timestamp)

Relationship
- A supplier can supply many products (possibly to multiple companies).

**Supplier ↔ Product mapping**
**Table: supplier_products**
- `id` (PK, integer)  
- `supplier_id` (FK → suppliers.id, not null)  
- `product_id` (FK → products.id, not null)  
- `company_id` (FK → companies.id, optional depending on requirement)  
- `created_at` (timestamp)

---

### 6) Bundles (Products containing other products)
**Table: product_bundles**
- `id` (PK, integer)  
- `bundle_product_id` (FK → products.id, not null)  
- `child_product_id` (FK → products.id, not null)  
- `child_quantity` (integer, not null, default 1)


Relationship
- A bundle is a product that contains multiple child products with specified quantities.


