# 📘 Chapter 2: Dimensional Modeling Techniques Overview


## Fundamental Concepts

> “Simplicity is the fundamental key that allows users to understand databases easily and software to navigate databases efficiently.” — Kimball & Ross
> 

### ✨ Essential Techniques for Every Dimensional Design

These foundational techniques must be considered in every dimensional modeling effort. They are referenced throughout the book and underpin all advanced modeling strategies.

### 📝 Gather Business Requirements & Data Realities

- **Purpose:** Understand business needs and the realities of source data before modeling.
- **How:**
    - Conduct sessions with business representatives to clarify objectives, KPIs, and analytic needs.
    - Meet with source system experts and perform high-level data profiling to assess data feasibility.
- **Why:** Aligns the data warehouse with real business goals and ensures the data required is available and usable.

> “Requirement gathering is vital to ensure department goals align with the overall project and to achieve stakeholder buy-in.”
> 

### 🤝 Collaborative Dimensional Modeling Workshops

- **Approach:** Design models collaboratively with business subject matter experts and data governance representatives.
- **Process:**
    - The data modeler leads interactive workshops, not isolated design.
    - Workshops help clarify requirements and ensure the model fits business realities.
- **Outcome:** Ensures the dimensional model reflects true business needs and secures business buy-in.

### 🔄 Four-Step Dimensional Design Process

| **Step** | **Description** |
| --- | --- |
| 1️⃣ Select the business process | Identify the operational activity to model (e.g., order taking, claims processing). |
| 2️⃣ Declare the grain | Define exactly what a single fact table row represents (the “grain”). |
| 3️⃣ Identify the dimensions | List the descriptive attributes (who, what, where, when, why, how). |
| 4️⃣ Identify the facts | Determine the numeric measurements (facts) to be stored. |
- **Note:** These steps are iterative and require business and data input at every stage.

### 🏢 Business Processes

- **Definition:** Operational activities that generate measurable events (e.g., sales, registrations).
- **Fact Table Focus:** Each fact table typically models a single business process.
- **Significance:** Selecting the right process sets the design target and guides grain, dimension, and fact choices.

### 🧱 Grain

- **Definition:** The grain specifies what each row in the fact table represents (e.g., one transaction, one daily snapshot).
- **Importance:** Declaring grain is a binding contract for the design—dimensions and facts must be consistent with it.
- **Best Practice:** Start with atomic grain (lowest detail) for flexibility and robust querying; summary grains can be added later for performance.
- **Rule:** Do not mix different grains in the same fact table.

### 🧑‍💼 Dimensions for Descriptive Context

- **Role:** Provide the “who, what, where, when, why, and how” for each business event.
- **Use:** BI tools use dimensions for filtering and grouping facts.
- **Design Tips:**
    - Dimensions should be single-valued per fact row when possible.
    - Heavy focus on data governance and development, as dimensions shape the user’s BI experience.
- **Nickname:** Dimensions are the “soul” of the data warehouse.

### 📊 Facts for Measurements

- **Definition:** Numeric measurements resulting from business process events.
- **Rule:** Each fact must align with the declared grain.
- **Example:** In retail, “quantity sold” and “extended price” are facts; “store manager’s salary” is not.

### ⭐ Star Schemas & OLAP Cubes

| **Structure** | **Description** |
| --- | --- |
| **Star Schema** | Fact tables linked to dimension tables via primary/foreign keys in a relational DBMS. |
| **OLAP Cube** | Multidimensional structure (often derived from star schemas) accessed via analytic languages (e.g., MDX). |
- **Purpose:** Both structures support analytical queries; OLAP cubes often serve as aggregate or final deployment layers.

### 🛠️ Graceful Extensions to Dimensional Models

Dimensional models are designed for resilience and easy extension:

- **Add facts:** New columns for facts consistent with the grain.
- **Add dimensions:** New foreign key columns, if they don’t change the grain.
- **Add attributes:** New columns in dimension tables.
- **Make grain more atomic:** Add attributes to dimensions and restate the fact table at a lower grain, preserving column names.

> “These changes can be implemented without altering existing BI queries or applications, and without changing query results.”
> 

### 🚦 Key points

- **Business-driven:** Always start with business requirements and realities.
- **Collaborative:** Involve business and technical experts throughout.
- **Structured:** Follow the four-step design process.
- **Consistent:** Declare and respect the grain.
- **Flexible:** Design for easy extension and robust analysis.

## Basic Fact Table Techniques

### 🏗️ Fact Table Structure

- **Definition:**
    - Stores numeric measures from real-world operational events.
    - Each row = one measurement event (at the declared grain).
- **Components:**
    - Numeric facts (measures)
    - Foreign keys to all associated dimensions
    - Optional: degenerate dimension keys, date/time stamps
- **Purpose:**
    - Central target for computations and aggregations in BI queries.
- **Design Principle:**
    - Based on real events, not on report layouts.

### ➕ Additive, Semi-Additive, Non-Additive Facts

| **Type** | **Description** | **Example** | **Summable Across...** |
| --- | --- | --- | --- |
| **Additive** | Can be summed across all dimensions | Sales amount, Quantity | All dimensions |
| **Semi-Additive** | Can be summed across some, but not all, dimensions | Account balance | Some (e.g., not time) |
| **Non-Additive** | Cannot be summed across any dimension; often ratios or percentages | Profit margin, Rates | None |

> 💡 Tip: For non-additive facts, store additive components and calculate the final value in the BI layer or OLAP cube.
> 

### 🚫 Nulls in Fact Tables

- **Facts:**
    - Nulls are handled gracefully by aggregate functions (SUM, COUNT, etc.).
- **Foreign Keys:**
    - Nulls are **not allowed** (would break referential integrity).
    - Instead, use a default/“unknown” row in the dimension table with a surrogate key.

### 🔗 Conformed Facts

- **Definition:**
    - Same measurement appears in multiple fact tables.
- **Rule:**
    - If definitions are identical, use the same name.
    - If definitions differ, use different names to avoid confusion.
- **Goal:**
    - Ensure consistency and clarity for cross-table analysis.

### 🧾 Transaction Fact Tables

- **Grain:**
    - One row per event at a point in space and time (atomic).
- **Characteristics:**
    - Most “dimensional” (many dimensions possible)
    - Rows exist only for actual events (can be dense or sparse)
    - Always includes foreign keys for all dimensions, optional time stamps, degenerate keys
- **Use Case:**
    - Maximum flexibility for slicing/dicing transaction data.

### 📅 Periodic Snapshot Fact Tables

- **Grain:**
    - One row per period (day, week, month, etc.), summarizing all events in that period.
- **Characteristics:**
    - Rows inserted for every period, even if no activity (zero/null facts)
    - Typically contains many facts
    - Foreign keys are uniformly dense
- **Use Case:**
    - Tracking balances, inventories, or other periodic summaries.

### 🏁 Accumulating Snapshot Fact Tables

- **Grain:**
    - One row per process instance, updated as the process moves through milestones.
- **Characteristics:**
    - Suitable for processes with defined start, steps, and end (e.g., order fulfillment)
    - Includes date foreign keys for each milestone
    - Rows are updated as process progresses (unlike other fact tables)
    - May include lag measurements, milestone counters
- **Use Case:**
    - Workflow/pipeline tracking (orders, claims, etc.)

### 🗂️ Factless Fact Tables

- **Definition:**
    - No numeric facts, just the intersection of dimension keys.
- **Use Cases:**
    - Recording events without measures (e.g., student attendance)
    - Analyzing “what didn’t happen” by comparing possible vs. actual events
- **Structure Example:**
    
    
    | **Date Key** | **Student Key** | **Class Key** | **...** |
    | --- | --- | --- | --- |
    | 20240522 | 123 | 456 | ... |

### 📈 Aggregate Fact Tables & OLAP Cubes

- **Aggregate Fact Tables:**
    - Rollups of atomic fact data for faster queries
    - Available alongside atomic tables for BI tools to choose the best level (aggregate navigation)
    - Contain foreign keys to shrunken (aggregated) dimensions
- **OLAP Cubes:**
    - Multidimensional aggregates, accessed directly by users
    - Built similarly to aggregate tables, but optimized for user interaction

> 🏎️ Aggregates = Indexes for your data warehouse: they boost performance but are not directly queried by users.
> 

### 🧩 Consolidated Fact Tables

- **Purpose:**
    - Combine facts from multiple processes at the same grain (e.g., sales actuals + forecasts).
- **Benefits:**
    - Simplifies cross-process analysis (e.g., actual vs. forecast)
    - Reduces complexity for BI tools
- **Trade-off:**
    - Increases ETL complexity, but eases analytic burden.

### 📝 Summary Table

| **Fact Table Type** | **Grain** | **Update Pattern** | **Typical Use Case** |
| --- | --- | --- | --- |
| Transaction | Event | Insert only | Sales, Orders, Payments |
| Periodic Snapshot | Period (e.g., day) | Insert only | Inventory, Account Balances |
| Accumulating Snapshot | Process instance | Update as process | Order Fulfillment, Claims |
| Factless | Event (no measures) | Insert only | Attendance, Event Participation |
| Aggregate | Summarized | Insert/refresh | Performance Optimization |
| Consolidated | Multiple processes | Insert/update | Actuals vs. Forecasts |

### 🚦 Key Points

- Fact tables are the **core** of dimensional models, holding numeric facts and foreign keys.
- Choose the right fact table type for your business process and analytic needs.
- Design for performance (aggregates), flexibility (atomic grain), and clarity (conformed facts).
- Avoid nulls in foreign keys; use default dimension rows instead.
- Factless tables and consolidated tables are powerful tools for special analytic scenarios.

## Basic Dimension Table Techniques

### 🏗️ Dimension Table Structure

- **Single Primary Key:**
    Each dimension table has a unique primary key column, used as a foreign key in related fact tables.
- **Wide, Flat, Denormalized:**
    Designed with many descriptive, low-cardinality text attributes (e.g., names, categories), not just operational codes.
- **Purpose:**
    Dimension attributes are the main targets for filtering, grouping, and labeling in BI queries and reports.

### 🔑 Dimension Surrogate Keys

- **Surrogate Key:**    
    An anonymous, sequential integer primary key (e.g., **`Customer_SK`**) generated by the data warehouse, not by source systems.
- **Why Not Natural Keys?:**
    - Natural keys can change over time or be inconsistent across systems.
    - Surrogate keys enable tracking changes (slowly changing dimensions) and support integration from multiple sources.
- **Exception:**
    Date dimension keys can be meaningful (e.g., **`YYYYMMDD`**).

### 🏷️ Natural, Durable, and Supernatural Keys

- **Natural Key:**
    Comes from operational systems (e.g., employee number), but can change due to business rules.
- **Durable/Supernatural Key:**
    Persistent, never-changing surrogate key assigned by the DW/BI system, independent of business processes.    
- **Benefit:**
    Ensures stable references for entities, even if natural keys change.

### 🔍 Drilling Down

- **Definition:**
    Adding a dimension attribute to a query for deeper analysis (e.g., from sales by region to sales by city).
- **No Predefined Hierarchies Needed:**
    Any dimension attribute can be used for drill-down, supporting flexible exploration.

### 🧾 Degenerate Dimensions
- **Definition:**
    A dimension with only a primary key and no separate dimension table (e.g., invoice number in a sales transaction).
- **Use Case:**
    Common in transaction and accumulating snapshot fact tables for identifiers that don’t need additional descriptive attributes.

### 🌟 Denormalized Flattened Dimensions
- **Best Practice:**
    Flatten hierarchies into separate attributes in the dimension row (denormalized), rather than normalizing into multiple tables.
- **Advantage:**
    Simplifies queries and improves performance.

### 🗂️ Multiple Hierarchies in Dimensions
- **Support for Multiple Hierarchies:**
    A single dimension can contain several natural hierarchies (e.g., date dimension with day-to-week and day-to-month paths).
- **Implementation:**
    All hierarchies coexist as separate attributes within the same dimension table.

### 🚩 Flags and Indicators as Textual Attributes
- **Recommendation:**
    Replace cryptic codes, flags, and indicators with full, meaningful text descriptions for clarity in BI tools and reports.

### 🚫 Null Attributes in Dimensions
- **Avoid Nulls:**
    Substitute with descriptive values like “Unknown” or “Not Applicable” to prevent inconsistent query results across databases.

### 📅 Calendar Date Dimensions
- **Role:**
    Enables navigation and grouping by dates, months, fiscal periods, holidays, etc.
- **Primary Key:**
    Can be a meaningful integer (e.g., **`20250525`** for May 25, 2025).
- **Special Row:**
    Include a row for unknown or to-be-determined dates.
- **Time-of-Day:**
    For more precision, add a separate time-of-day dimension foreign key to the fact table.

### 🧑‍🤝‍🧑 Role-Playing Dimensions
- **Definition:**
    Single physical dimension referenced multiple times in a fact table for different roles (e.g., order date, ship date, delivery date all referencing the date dimension).
    
- **Implementation:**
    Use separate views or aliases for each role.

### 🧩 Junk Dimensions
- **Definition:**
    Combines miscellaneous, low-cardinality flags and indicators into a single dimension.
- **Purpose:**
    Reduces schema complexity and avoids proliferation of tiny dimensions.

### ❄️ Snowflaked Dimensions

| **Star Schema (Recommended)** | **Snowflake Schema (Not Recommended)** |
| --- | --- |
| Denormalized, flat | Normalized, multi-table |
| Fewer joins, easier for users | More joins, harder for users |
| Better query performance | Worse query performance |

- **Kimball’s Advice:**
    Avoid snowflaking unless absolutely necessary; denormalized star schemas are easier to use and faster to query.

### 🔗 Outrigger Dimensions

- **Definition:**    
    A dimension table references another dimension table (e.g., account dimension referencing an account opening date dimension).
- **Guideline:**
    Use sparingly; most inter-dimension relationships should be handled in the fact table, not via outriggers.

### 📝 Summary Table
| **Technique** | **Key Point** |
| --- | --- |
| Structure | Wide, flat, denormalized, single surrogate key |
| Surrogate Keys | Use anonymous integers, not natural keys (except for date dimension) |
| Durable Keys | Persistent, never change, independent of source business rules |
| Drilling Down | Flexible, no need for predefined hierarchies |
| Degenerate Dimensions | Only key, no separate table, for identifiers |
| Denormalized Dimensions | Avoid normalization; flatten hierarchies into attributes |
| Multiple Hierarchies | Support multiple hierarchies within one dimension |
| Flags & Indicators | Use full text, not codes |
| Nulls | Replace with “Unknown”/“Not Applicable” |
| Calendar Date Dimension | Rich attributes, special row for unknown, meaningful key |
| Role-Playing Dimensions | Same dimension, multiple roles via aliases/views |
| Junk Dimensions | Combine low-cardinality flags/indicators into one dimension |
| Snowflaked Dimensions | Avoid—prefer star schema |
| Outrigger Dimensions | Use rarely; prefer handling relationships in fact tables |


## Integration via Conformed Dimensions

### 🌐 Conformed Dimensions: The Key to Integration

- **Definition:**
    Dimension tables are “conformed” when their attributes have the same column names and domain values across different tables.
- **Purpose:**
    - Enables data from different business processes (fact tables) to be combined in a single, aligned report.
    - Supports consistent analysis and reporting across the enterprise.
- **How:**
    - Conformed attributes are used as row headers (grouping columns) in queries, aligning results from multiple fact tables.
- **Benefits:**
    - Ensures analytic consistency
    - Reduces future development costs
    - Avoids redundant work (“not repeatedly re-creating the wheel”)
- **Governance:**
    - Defined collaboratively with business data governance representatives.
    - Reused across fact tables and processes.

### 📏 Shrunken Dimensions
- **Definition:**
    - Subsets of conformed dimensions, containing only a subset of rows and/or columns.
- **Use Cases:**
    - Required for aggregate fact tables (e.g., monthly, brand-level aggregates).
    - Needed when business processes operate at higher granularity (e.g., forecasts by month/brand vs. sales by date/product).
    - Also used when one dimension is a subset of another at the same level of detail.

### 🔄 Drilling Across
- **Definition:**
    - Making separate queries against two or more fact tables using identical conformed dimension attributes as row headers.
- **How it Works:**
    - Results from different fact tables are aligned via a sort-merge on common dimension attributes.
- **In BI Tools:**
    - Often called “stitch” or “multipass query.”
- **Benefit:**
    - Allows seamless cross-process analysis without complex joins.

### 🔗 Value Chain
- **Definition:**
    - The natural flow of an organization’s primary business processes (e.g., purchasing → warehousing → sales).
- **Fact Table Design:**
    - Each value chain step typically produces its own atomic fact table, as each has unique metrics, time intervals, and granularity.

### 🚌 Enterprise Data Warehouse Bus Architecture
- **Purpose:**
    - Provides an incremental, manageable approach to building an enterprise DW/BI system.
- **How it Works:**
    - Focuses on business processes as building blocks.
    - Delivers integration through standardized, reusable conformed dimensions.
    - Supports both relational and OLAP structures.
- **Benefits:**
    - Encourages agile, manageable implementations.
    - Platform and technology independent.

### 🗂️ Enterprise Data Warehouse Bus Matrix

|  | **Dim 1** | **Dim 2** | **Dim 3** | **...** |
| --- | --- | --- | --- | --- |
| **Process 1** | X |  | X |  |
| **Process 2** | X | X |  |  |
| **Process 3** |  | X | X |  |
- **Rows:** Business processes
- **Columns:** Dimensions
- **Shaded Cells:** Indicate association between process and dimension
- **Usage:**
    - Tests whether dimensions are well-defined for each process.
    - Identifies where dimensions should be conformed across processes.
    - Helps prioritize DW/BI projects (implement one row/process at a time).
- **Communication Tool:**
    - Facilitates discussion between technical teams and business stakeholders.

### 🗂️ Detailed Implementation Bus Matrix

- **More Granular:**
    - Expands each business process row to show specific fact tables or OLAP cubes.
- **Documentation:**
    - Documents precise grain and list of facts for each table/cube.
- **Purpose:**
    - Supports detailed design and implementation planning.

### 👥 Opportunity/Stakeholder Matrix

|  | **Marketing** | **Sales** | **Finance** | **...** |
| --- | --- | --- | --- | --- |
| **Process 1** | X | X |  |  |
| **Process 2** |  | X | X |  |
| **Process 3** | X |  | X |  |
- **Rows:** Business processes
- **Columns:** Business functions (e.g., marketing, sales, finance)
- **Shaded Cells:** Indicate which business functions are interested in which processes
- **Usage:**
    - Identifies stakeholders for collaborative design sessions.
    - Ensures the right business groups are involved in each project.
      
### 🚦 Key Points

- **Conformed dimensions** are the foundation of cross-process integration in dimensional modeling.
- **Shrunken dimensions** enable aggregation and higher-level analysis.
- **Drilling across** allows seamless, multi-fact-table analysis using conformed attributes.
- **Bus architecture and matrices** provide structure, clarity, and agility for enterprise DW/BI development.
- **Stakeholder mapping** ensures business alignment and buy-in for each process-centric project.

## Dealing with Slowly Changing Dimension Attributes

### 🕰️ What Are Slowly Changing Dimensions (SCDs)?

- **Definition:**
    
    Dimension attributes that change over time, often unpredictably, but less frequently than fact table entries.
    
- **Challenge:**
    
    Preserve historical accuracy and data integrity while supporting current and historical analysis.

### 🧩 SCD Types Explained

#### 🟢 Type 0: Retain Original

- **Approach:** Attribute value never changes. Facts are always grouped by the original value.
- **Use Case:** “Original” fields (e.g., original credit score, durable identifier), most date dimension attributes.

#### 🟡 Type 1: Overwrite

- **Approach:** Overwrite old value with the new one; no history is kept.
- **Effect:** Always shows the most recent value; destroys historical context.
- **Use Case:** Correcting errors, attributes where only the current value matters (e.g., correcting a misspelled name).

> ⚠️ Recompute aggregates and cubes if Type 1 changes affect them, since history is lost.
> 

#### 🟠 Type 2: Add New Row

- **Approach:** Insert a new row in the dimension table for each change, with a new surrogate key.
- **Implementation:**
    - Fact tables reference the current surrogate key at the time of the event.
    - Add columns: effective date, expiration date, and current row indicator.
- **Effect:** Preserves full history of changes; supports “as-was” reporting.
- **Use Case:** Tracking customer address changes, product reclassifications.

#### 🟣 Type 3: Add New Attribute

- **Approach:** Add a new column to store the previous value; main attribute is overwritten.
- **Effect:** Supports limited history (usually just one previous value).
- **Use Case:** When only the current and one previous value are needed (e.g., current and previous department).

#### 🔵 Type 4: Add Mini-Dimension

- **Approach:** Move rapidly changing or frequently used attributes to a separate mini-dimension table.
- **Implementation:**
    - Both the base dimension and mini-dimension keys are stored in the fact table.
- **Effect:** Efficiently handles volatile attributes; reduces size and update frequency of the main dimension.
- **Use Case:** Customer demographics, frequent status changes.

#### 🟤 Type 5: Mini-Dimension + Type 1 Outrigger

- **Approach:** Combine Type 4 mini-dimension with a Type 1 reference in the base dimension.
- **Implementation:**
    - The base dimension includes a current profile reference (Type 1) to the mini-dimension.
    - ETL must update this reference on change.
- **Effect:** Allows querying both historical and current mini-dimension values directly.
- **Use Case:** Need to report facts by both current and historical attribute values.

#### 🟠 Type 6: Type 1 Attributes on Type 2 Dimension

- **Approach:** Hybrid of Types 1, 2, and 3.
- **Implementation:**
    - Type 2 row versioning for full history.
    - Type 1 attributes for current values, updated across all rows for a durable key.
- **Effect:** Supports filtering/grouping by either current or historical attribute values.
- **Use Case:** When both “as-was” and “as-is” reporting are required.

#### ⚫ Type 7: Dual Type 1 and Type 2 Dimensions

- **Approach:** Fact table contains both the surrogate key (Type 2) and durable key (Type 1).
- **Implementation:**
    - Join via surrogate key for historical view; join via durable key for current view.
    - Separate BI views for each perspective.
- **Effect:** Supports both “as-was” and “as-is” reporting in a flexible way.
- **Use Case:** Advanced analytics needing both perspectives.


## 📝 SCD Types Cheat Sheet
| **Type** | **History Preserved** | **How?** | **When to Use** |
| --- | --- | --- | --- |
| 0 | Yes | Never update | Immutable/original attributes |
| 1 | No | Overwrite | Only current value matters |
| 2 | Yes | New row per change | Full change history needed |
| 3 | Partial | Add new attribute (side-by-side) | Only one prior value needed |
| 4 | Yes | Mini-dimension for volatile attributes | Rapidly changing or frequently used attrs |
| 5 | Yes + Current | Mini-dimension + Type 1 outrigger | Need both current and historical context |
| 6 | Yes + Current | Type 1 attrs on Type 2 dimension | Hybrid: historical & current analysis |
| 7 | Yes + Current | Dual keys for Type 1 & 2 | Both “as-was” and “as-is” reporting |

## Dealing with Dimension Hierarchies

### 🏛️ What Are Dimension Hierarchies?

- **Definition:**
    
    Hierarchies in dimensions represent structured relationships among attributes (e.g., Product → Brand → Category → Department).
    
- **Purpose:**
    
    Enable users to drill down or roll up data for analysis at various levels of detail.

### 🟩 Fixed Depth Positional Hierarchies

- **Description:**
    - Series of many-to-one relationships with a set number of levels and agreed-upon names.
    - Example: Product → Brand → Category → Department.
- **Modeling Approach:**
    - Each level appears as a separate column (attribute) in the dimension table.
- **Benefits:**
    - Easiest to understand and navigate.
    - Delivers predictable, fast query performance.
- **When to Use:**
    - When hierarchy levels are consistent and well-defined.
 
| Product_Key | Product_Name | Brand     | Category   | Department |
|-------------|-------------|-----------|------------|------------|
| 1           | Widget A    | Acme      | Gadgets    | Hardware   |
| 2           | Widget B    | Acme      | Gadgets    | Hardware   |
| 3           | Gizmo X     | BetaCorp  | Tools      | Hardware   |
| 4           | App Z       | SoftInc   | Software   | Digital    |

  
### 🟨 Slightly Ragged/Variable Depth Hierarchies

- **Description:**
    - Hierarchies with a small, variable number of levels (e.g., geographic hierarchies with 3–6 levels).
- **Modeling Approach:**
    - Use columns for the maximum number of levels, leave unused levels blank or with a default value.
    - Populate columns based on business rules (leave unused levels blank or with a default value).
- **Benefits:**
    - Avoids complexity of more advanced hierarchical solutions.
- **When to Use:**
    - When depth varies slightly, but not unpredictably.
     
| Location_Key | Country   | State     | City        | Neighborhood  |
|--------------|-----------|-----------|-------------|---------------|
| 1            | USA       | Texas     | Houston     | Midtown       |
| 2            | USA       | Texas     | Houston     |               |
| 3            | USA       |           | Washington  | Capitol Hill  |
| 4            | Canada    | Ontario   | Toronto     |               |
| 5            | France    |           | Paris       |               |


### 🟧 Ragged/Variable Depth Hierarchies with Hierarchy Bridge Tables

- **Description:**
    - Hierarchies where the number of levels is highly variable or unknown, and relationships may be recursive (parent/child).
- **Modeling Approach:**
    - Use a **bridge table**: a separate table that stores all possible paths in the hierarchy.
    - Each row in the bridge table represents a relationship or path between nodes.
- **Benefits:**
    - Supports alternative hierarchies, shared ownership, and time-varying structures.
    - Enables all forms of hierarchy traversal with standard SQL—no special extensions required.
- **When to Use:**
    - For complex, deeply nested, or recursive hierarchies.
 
**Employee Dimension**

| Employee_Key | Employee_Name | Title       |
|--------------|--------------|--------------|
| 1            | Alice        | CEO          |
| 2            | Bob          | VP           |
| 3            | Carol        | Manager      |
| 4            | Dave         | Analyst      |

**Employee Bridge Table**

| Ancestor_Employee_Key | Descendant_Employee_Key | Levels_From_Ancestor |
|----------------------|-------------------------|---------------------|
| 1                    | 2                       | 1                   |
| 1                    | 3                       | 2                   |
| 1                    | 4                       | 3                   |
| 2                    | 3                       | 1                   |
| 2                    | 4                       | 2                   |
| 3                    | 4                       | 1                   |


## 🟦 Ragged/Variable Depth Hierarchies with Pathstring Attributes

- **Description:**
    - Encode the full hierarchical path as a text string in a single column for each dimension row.
- **Modeling Approach:**
    - Each row’s pathstring shows the full lineage from the top node to the current node (e.g., “Dept>Category>Brand>Product”).
    - Add a Pathstring column to the dimension table, encoding the full path from the root to each node.
- **Benefits:**
    - Simplifies many hierarchy analysis queries using standard SQL.
- **Limitations:**
    - Not suitable for alternative or shared hierarchies.
    - Vulnerable to changes in hierarchy structure (may require relabeling all paths).
- **When to Use:**
    - For simple, single-path ragged hierarchies where performance and flexibility are less critical.
      
| OrgUnit_Key | OrgUnit_Name      | Pathstring                      |
|-------------|------------------|----------------------------------|
| 1           | Company          | Company                          |
| 2           | Division A       | Company>Division A               |
| 3           | Dept X           | Company>Division A>Dept X        |
| 4           | Team 1           | Company>Division A>Dept X>Team 1 |
| 5           | Division B       | Company>Division B               |
| 6           | Dept Y           | Company>Division B>Dept Y        |

## 📝 Summary Table

| Hierarchy Type | Modeling Approach | Pros | Cons / Limitations | When to Use |
| --- | --- | --- | --- | --- |
| Fixed Depth Positional | Separate columns for each level | Simple, fast, user-friendly | Inflexible for variable-depth hierarchies | Consistent, agreed-upon hierarchies |
| Slightly Ragged/Variable Depth | Max columns, fill as needed | Avoids complexity | Some empty columns, not scalable | Small range of levels |
| Ragged/Variable Depth (Bridge) | Hierarchy bridge table | Flexible, supports all traversals | More complex design and queries | Deep/recursive/complex hierarchies |
| Ragged/Variable Depth (Pathstring) | Encoded pathstring attribute | Simple SQL queries | No alt/shared hierarchies, relabeling | Simple, single-path ragged hierarchies |
