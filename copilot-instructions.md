
# Copilot Ruleset for ETL SQL Development (SSMS)

## Purpose
These rules guide Copilot to generate clean, secure, maintainable, and production‑grade SQL for ETL pipelines in SQL Server. 
All SQL generated should follow these conventions unless explicitly overridden.

---

## 1. General SQL Standards
- Use **lowercase** for SQL keywords (SELECT, JOIN, WHERE).
- Use **uppercase** for identifiers (table names, column names, aliases).
- Avoid `SELECT *`; always specify explicit columns.
- Use **meaningful, consistent aliases** (src, stg, xform, tgt, dim, fact).
- Prefer **CTEs** or **temp tables** for multi-step transformations.
- Write **set-based** logic; avoid RBAR, loops, and cursors unless required.
- Add concise comments explaining non-obvious logic or business rules.

---

## 2. ETL Pipeline Structure
Copilot should structure ETL SQL in this order:

1. **Extract**  
   - Pull data from source into staging (#stg)  
   - Filter early  
   - Use explicit column lists  

2. **Validate**  
   - Enforce data types  
   - Check required fields  
   - Split valid vs invalid rows (#valid, #rejects)  
   - Add `RejectReason` when applicable  

3. **Transform**  
   - Cleanse and normalize data  
   - Join to lookup tables  
   - Apply business rules  
   - Deduplicate using ROW_NUMBER  
   - Use HASHBYTES for change detection when needed  

4. **Load**  
   - Use MERGE for inserts/updates  
   - Ensure idempotency  
   - Track row counts  

5. **Audit & Logging**  
   - Log start/end timestamps  
   - Log row counts (extracted, inserted, updated, rejected)  
   - Log BatchID  

6. **Error Handling**  
   - Use TRY/CATCH in stored procedures  
   - Log errors to ETL_ErrorLog  

---

## 3. Performance Expectations
- Favor **SARGable** predicates; avoid wrapping columns in functions.
- Avoid unnecessary DISTINCT, ORDER BY, or scalar UDFs.
- Use temp tables for large transformations.
- Filter early to reduce data movement.
- Use appropriate indexes for staging and target tables.
- Avoid table hints unless explicitly required.

---

## 4. Security Requirements
- Never concatenate user input into dynamic SQL.
- Always use `sp_executesql` with parameters.
- Avoid NOLOCK unless explicitly documented.
- Do not expose sensitive data in logs or error messages.

---

## 5. MERGE Conventions
When generating MERGE statements:
- Match on business key(s).
- Update only when data has changed (use HASH or column comparison).
- Insert when new.
- Avoid DELETE unless explicitly required.
- Capture row counts after MERGE.

---

## 6. Naming Conventions
- Temp tables: `#stg_*`, `#valid_*`, `#rejects_*`, `#xform_*`
- CTEs: PascalCase (e.g., CleanData, DedupedRows)
- Stored procedures: `etl.<ProcessName>`
- Audit tables: `ETL_AuditLog`, `ETL_ErrorLog`

---

## 7. Stored Procedure Structure
Copilot should generate stored procedures with:

- Parameter defaults  
- Initialization section  
- Extract → Validate → Transform → Load sequence  
- TRY/CATCH block  
- Audit logging  
- Idempotent behavior  

---

## 8. Data Quality Rules
Copilot should automatically consider:

- Required fields must not be NULL  
- Dates must be valid and within expected ranges  
- Numeric fields must be convertible  
- Deduplication using ROW_NUMBER  
- Reject rows with clear `RejectReason`  

---

## 9. Idempotency
All ETL SQL should be safe to rerun without duplicating or corrupting data.

- Use MERGE instead of INSERT when appropriate  
- Use BatchID or watermarking  
- Avoid destructive operations unless explicitly required  

---

## 10. Formatting Rules
- One JOIN per line  
- One WHERE filter per line  
- Align keywords and indentation consistently  
- Use section headers for each ETL phase  

---

## 11. Anti‑Patterns to Avoid
Copilot should **not** generate:

- SELECT *  
- Cursors (unless explicitly requested)  
- Scalar functions in SELECT or WHERE  
- Non-SARGable filters  
- Dynamic SQL without parameters  
- Undocumented NOLOCK hints  
- Nested subqueries when a CTE is clearer  

---

## 12. Output Expectations
All generated SQL should be:

- Clean  
- Readable  
- Maintainable  
- Production‑ready  
- ETL‑friendly  
- Safe to execute in batch pipelines  


