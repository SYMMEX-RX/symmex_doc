# Product Production Process.
 Considering their dependencies, this order ensures that foundational elements are in place before dependent items are defined.
 Think of it like building with blocks – you need the base before you can add layers on top.

- __Phase 1: Foundational & Master Data Setup__

    This phase involves setting up the core entities that other items will depend on.
    - __Company:__ The root entity. All other data is typically scoped to a company.
    - __User / UserGroup:__ Define who will interact with the system and their roles/permissions.
    - __Unit:__ Define units of measure (e.g., kg, L, pieces, hours) that will be used for materials, batch sizes, process times, etc.
    - __Room / Location / Premise:__ Define physical locations or work centers where production activities occur.
    - __Machine / Equipment:__ Define the machinery or equipment used in production.
    - __Document (Types/Categories):__ Define categories or types for documents like SOPs, Specifications, Batch Records, etc. (The actual documents come later).

- __Phase 2: Material & Product Definition__

    Once the foundational data is set, you define what you're working with and what you're making.

    - __MaterialGroup:__ Categorize raw materials (e.g., Active Ingredients, Excipients, Packaging Components, Raw Metals).
    - __Material:__ Define specific raw materials or components, linking them to MaterialGroups and Units.
    - __MaterialSpecification:__ Define the quality standards and characteristics for each Material.
    - __Supplier:__ Define suppliers from whom materials are procured. (Can also be done earlier, but often tied to specific materials).
    - __Product:__ Define the finished good you intend to produce. This includes basic information like name, code, etc.
    - __ProductSpecification:__ Define the quality standards, characteristics, and acceptance criteria for the Product.
    - __BatchFormula / BillOfMaterials (BOM):__
        - This is crucial. It defines the "recipe" for a Product.
        - It lists all Materials (and their quantities per Unit of product or per batch size) required to make one batch of the Product.
        - Depends on Product and Material.

- __Phase 3: Process Definition (Templates)__

    Now, define how the product is made. These are templates that will be instantiated for actual production runs.

    - __ProcessTemplate:__
        - Define individual, discrete manufacturing steps or operations (e.g., "Weighing API," "Blending Step 1," "Tablet Compression," "Visual Inspection").

        -   These can specify:
            - Instructions.
            - Materials to be consumed (often as placeholders to be filled by the BatchFormula later).
            -  Machines required.
            - UserGroups authorized to perform/verify.
            - Parameters to be monitored (ProcessParameter definitions).
            - Required in-process QualityCheck types.
            - Links to SOP Documents.
    - __ProcessGroupTemplate:__
        - Define a logical phase or stage of production (e.g., "Granulation Phase," "Assembly Line Stage 1," "Coating Process").
        - A ProcessGroupTemplate is typically a sequence of one or more ProcessTemplates.
        - It can also define overall phase requirements, like specific environmental conditions or phase-level documentation.

    - __Document (SOPs, Work Instructions):__ Create the actual Standard Operating Procedures, work instructions, and other guiding documents. These are then linked to the relevant ProcessTemplates or ProcessGroupTemplates.

- __Phase 4: Production Planning & Order Creation__
    
    With products and processes defined, you can plan actual production.
    - __ProcessGroupGroup (Production Order / Batch / Lot):__
        - This represents a specific instruction to produce a certain quantity of a Product.
        - It links to:
            - A Product.
            - A BatchFormula (to know what materials and quantities are needed for this specific order).
            - Often, it will reference the top-level ProcessGroupTemplate that outlines the overall manufacturing flow for that product.
            - Specifies batch size, planned dates, batch number.

- __Phase 5:__ Production Execution (Instantiated Processes)

    When a ProcessGroupGroup (Production Order) is initiated:

    - __MaterialRequest:__ Generated based on the ProcessGroupGroup and its BatchFormula to request materials from Inventory.
    - __Inventory Transactions:__ Materials are dispensed, and inventory levels are updated.
    - __ProcessGroup:__ An instance of a ProcessGroupTemplate is created for the specific ProcessGroupGroup.
    - __Process:__ Instances of ProcessTemplates are created within the active ProcessGroup.
        During the execution of a Process, the following are created/recorded:
        - __MaterialUsage:__ Actual materials consumed.
        - __ProcessParameterValue:__ Actual values for monitored parameters.
        - __QualityCheck (In-Process):__ Results of in-process checks.
        - __EnvironmentalMonitoring Records:__ If applicable.
        - Links to Users who performed and verified steps.

- __Phase 6: Finishing & Release__

    - __QualityCheck (Final Product):__ Results of testing the finished Product from the ProcessGroupGroup.
    - __Label / LabelValue:__ If final product labels are generated and tracked.
    - __Inventory Transactions:__ Finished goods are added to inventory.
    - __ProcessGroupGroup Status Update:__ The production order is marked as completed, reviewed, and potentially released.

Ongoing/Supporting Items (Created as needed throughout the lifecycle):
    - __TrainingRecord:__ Created when users complete training on SOPs, equipment (Machine), or processes (ProcessTemplate).
    - __ChangeRequest:__ Initiated whenever a controlled change is needed for Products, Materials, BatchFormulas, ProcessTemplates, Documents, etc.
    - __Audit / AuditFinding:__ Created as part of quality assurance activities.
    - __CAPA (Corrective and Preventive Action):__ Created in response to deviations, audit findings, complaints, etc.
    - __Notification:__ Generated by the system to inform users of events, tasks, or alerts.

This sequence provides a structured approach, ensuring that dependencies are met before subsequent items are created. In a real-world system, some of these steps might be iterative or occur in parallel where dependencies allow.