# REPORT to Relational Database Conversion

## Approach to Converting REPORT to Relational

1. Identify entities by analyzing file structures and finding logical groupings
2. Normalize data to reduce redundancy while maintaining data integrity
3. Define relationships between entities
4. Map REPORT fields to relational tables and columns
5. Optimize structure for performance and maintainability

## Logical Data Model

### Core Entities and Their Relationships

*Logical Data Model for Health Management System*
*(Click to open diagram)*

## Mapping REPORT Files to Relational Tables

*REPORT to Relational Database Mapping*
*(Click to open document)*

## Explanation of the Conversion Approach

### 1. Entity Identification and Structure

I identified the key entities in the system by analyzing the REPORT file structures. The main observation was that there are parallel structures for credit healths and regular healths, which I've consolidated into a single relational model. This approach:

1. Eliminates redundancy: Rather than having separate tables for credit and debit healths, I've used a product_type field to distinguish them
2. Maintains data integrity: Relationships between entities are preserved through foreign keys
3. Improves maintainability: Changes to health handling would only need to be made in one place

### 2. Normalization Process

I applied normalization principles to reduce data redundancy:

1. First Normal Form (1NF): Eliminated repeating groups by creating separate tables (like SHIPPING_ADDRESS)
2. Second Normal Form (2NF): Ensured all non-key attributes depend on the entire primary key
3. Third Normal Form (3NF): Removed transitive dependencies by creating separate tables for entities like blind and health_PRODUCT

### 3. Key Relationship Design Decisions

1. Account and Customer separation: Rather than combining customer and account information, I separated them to allow for multiple customers per account (joint accounts)
2. Order/Shipment/Address structure: The shipping process is modeled as separate entities to capture the complete lifecycle
3. Health Product abstraction: Created a product table to manage different health types and styles in one place

### 4. Optimization Strategies

1. Surrogate keys: Used generated primary keys (like account_id) instead of composite natural keys for better performance
2. Denormalization considerations: While mostly normalized, some redundant data might be kept for performance (like health_type in the health table)
3. Index planning: Primary keys and foreign keys would be indexed for performance

### 5. Handling REPORT-Specific Features

1. Field naming: Converted REPORT's kebab-case to relational snake_case
2. Data type mapping: Mapped REPORT field types to appropriate SQL data types
3. Redefines handling: Addressed REPORT's redefines by choosing the most appropriate representation for relational storage

## Benefits of This Approach

1. Data integrity: Enforced through foreign key relationships
2. Reduced redundancy: Consolidated duplicate structures from credit and debit health systems
3. Improved query performance: Properly normalized structure with appropriate indexes
4. Scalability: Clean separation of concerns allows for future expansion
5. Maintenance: Easier to maintain with clear entity boundaries
6. Reporting: Simplified queries across the entire health system

This relational model provides a solid foundation for building a modern database system while preserving all the functionality and data relationships from the original REPORT files.

---

## REPORT to Relational Database Mapping

### 1. ACCOUNT Table
**Sources:** credithealth-features.txt, health-features.txt
* account_id (Generated PK)
* firm_number ← firm-number
* branch_number ← branch
* account_number ← account-number
* social_cd ← social-cd
* pay_by_phone ← payb-by-phone
* account_source ← account-source
* fee_ind ← fee-ind
* margin_cd ← margin-cd
* dir_deposit_date ← dir-deposit-date
* dir_debit_date ← dir-debit-date

### 2. CUSTOMER Table
**Sources:** credithealth-features.txt, health-features.txt
* customer_id (Generated PK)
* account_id (FK to ACCOUNT)
* primary_name ← ccrd-prim-name / ck-prim-name
* co_name ← ccrd-co-name / ck-co-name
* address_line1 ← ccrd-address-1 / ck-address-1
* address_line2 ← ccrd-address-2 / ck-address-2
* city ← ccrd-city / ck-city
* state ← ccrd-state / ck-state
* zip_code ← ccrd-reg-zip / ck-reg-zip
* zip_plus4 ← ccrd-zip-plus4 / ck-zip-plus4
* alt_address_line1 ← ccrd-alt-address-1 / ck-alt-address-1
* alt_address_line2 ← ccrd-alt-address-2 / ck-alt-address-2
* alt_city ← ccrd-alt-city / ck-alt-city
* alt_state ← ccrd-alt-state / ck-alt-state
* alt_zip_code ← ccrd-alt-reg-zip / ck-alt-reg-zip
* alt_zip_plus4 ← ccrd-alt-zip-plus4 / ck-alt-zip-plus4

### 3. HEALTH_PRODUCT Table
**Sources:** credithealth-features.txt, health-features.txt, credithealth-order.txt, health-order.txt
* product_id (Generated PK)
* product_type (Credit or Debit)
* product_level ← sqrt-product-level / zyx-product-level
* book_style ← ccrd-book-style / ck-book-style

### 4. HEALTH Table
**Sources:** credithealth-features.txt, credithealth-master.txt, health-features.txt, health-master.txt
* health_id (Generated PK)
* account_id (FK to ACCOUNT)
* product_id (FK to HEALTH_PRODUCT)
* health_number ← ccrd-account-no / ck-account-no / credithealth-order-credithealth-acct-no / health-order-health-acct-no
* health_type (Credit or Debit)
* issue_date (Derived from order date or shipping date)
* health_style ← credithealth-order-last-style / health-order-last-style
* no_addr_print ← ccrd-no-addr-prnt / ck-no-addr-prnt
* two_sign_lines ← ccrd-2-sign-lines / ck-2-sign-lines
* sign_health ← sign-health
* reorder_date (Derived)
* auto_reorder ← ccrd-auto-reorder / ck-auto-reorder
* return_option ← ccrd-return-option / ck-return-option
* cancellation_status ← ccrd-can-reinst-sw / ck-can-reinst-sw
* cancellation_reinstate_date ← ccrd-can-reinst-date / ck-can-reinst-date
* last_style_used ← ccrd-last-credithealth-no-used / ck-last-health-no-used
* health_status (Active, Suspended, Revoked)
* status_change_date
* status_change_reason
* sca_ind
* sca_status

### 5. HEALTH_ORDER Table
**Sources:** credithealth-order.txt, health-order.txt, credithealth-master.txt, health-master.txt
* order_id (Generated PK)
* account_id (FK to ACCOUNT)
* health_id (FK to HEALTH)
* order_date ← sqrt-date-rec / zyx-date-rec (combined fields)
* order_time ← sqrt-time-rec / zyx-time-rec (combined fields)
* operator_code ← sqrt-operator-code / zyx-operator-code
* terminal_id ← sqrt-termid / zyx-termid
* special_order ← credithealth-order-special-order / health-order-special-order
* special_program ← sqrt-special-program / zyx-special-program
* health_quantity ← sqrt-ccrd-quant / zyx-crd-quant
* broker_dealer_logo ← sqrt-broker-drler-logo / zyx-broker-drler-logo
* order_status (Derived from history/shipment data)
* order_source (Derived)

### 6. SHIPMENT Table
**Sources:** credithealth-history.txt, health-history.txt
* shipment_id (Generated PK)
* order_id (FK to HEALTH_ORDER)
* sent_date ← usa-date-sent (combined fields)
* received_date ← usa-date-recvd (combined fields)
* shipped_date ← usa-date-shipped (combined fields)
* shipping_method ← usa-how-shipped / usa-how-shipped-3
* shipped_from ← usa-shipped-from
* mail_delivery_ind ← sqrt-mail-delivery-ind / zyx-mail-delivery-ind

### 7. SHIPPING_ADDRESS Table
**Sources:** credithealth-order.txt, health-order.txt
* shipping_address_id (Generated PK)
* order_id (FK to HEALTH_ORDER)
* address_line1 ← sqrt-mail-address-1 / zyx-mail-address-1
* address_line2 ← sqrt-mail-address-2 / zyx-mail-address-2
* city ← sqrt-mail-city / zyx-mail-city
* state ← sqrt-mail-state / zyx-mail-state
* zip_code ← sqrt-mail-zip / zyx-mail-zip
* zip_plus4 ← sqrt-mail-zip-plus4 / zyx-mail-zip-plus4

### 8. BLIND Table
**Sources:** credithealth-order.txt, health-order.txt
* blind_id (Generated PK)
* blind_number ← sqrt-trans-blind / zyx-trans-blind
* blind_name ← sqrt-blind-name / zyx-blind-name
* coffee ← sqrt-coffee / zyx-coffee
* minimum_disbursement ← sqrt-minimum-disbursement / zyx-minimum-disbursement
* core_symbol ← sqrt-core-symb / zyx-core-symb

### 9. ORDER_BLIND Table
**Sources:** credithealth-master.txt, health-master.txt
* order_blind_id (Generated PK)
* order_id (FK to HEALTH_ORDER)
* blind_id (FK to BLIND)
* merge_indicator ← credithealth-order-merge-indicator / health-order-merge-indicator
* merge_blind_number ← credithealth-order-merge-blind-no / health-order-merge-blind-no

### 10. HEALTH_STATUS_HISTORY Table
* status_history_id (Generated PK)
* health_id (FK to HEALTH)
* status_from
* status_to
* change_date
* change_reason
* operator_id
* release_id
* release_date
* release_time

## Revised Approach for Status Management

### Improved Health Status Handling

Instead of maintaining a separate SUBWAY table, we've simplified the model by adding status fields to the health table. This is more efficient and better reflects the business reality that suspension is a state of a health rather than a separate entity.

### Benefits of the Revised Approach:

1. Simplified Queries: No need to join to a separate SUBWAY table to determine current health status
2. Better Data Integrity: Status is an intrinsic property of the health
3. Complete Audit Trail: All status changes are recorded historically
4. Flexible Status Flow: Supports any status transition (suspend→active, active→revoked, etc.)
5. Regulatory Compliance: Maintains complete history for compliance purposes

### Implementation Notes:
* The health_status field in the HEALTH table contains the current status
* The HEALTH_STATUS_HISTORY table tracks all historical changes
* Status changes like suspension, reactivation, and revocation become simple updates to the HEALTH table with a corresponding history entry
