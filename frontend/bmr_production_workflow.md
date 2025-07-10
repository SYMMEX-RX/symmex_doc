# BMR Production Process Workflow Guide

## Overview
This document outlines the complete Batch Manufacturing Record (BMR) workflow from start to finish using the Symmex Backend API endpoints. This process ensures full pharmaceutical manufacturing compliance and traceability.

## Workflow Steps

### 1. **Create Production Plan (ProcessGroupGroup)**
**Endpoint:** `POST /ProcessGroupGroups`

```json
{
  "name": "Batch-001-Paracetamol",
  "batchNumber": "B-20241220-001",
  "productId": "product-123",
  "masterFormulaRecordId": "mfr-456",
  "batchSize": 1000,
  "status": "Draft",
  "plannedStartDate": "2024-12-20T08:00:00Z",
  "plannedEndDate": "2024-12-22T16:00:00Z"
}
```

### 2. **Generate BMR from Production Plan**
**Endpoint:** `POST /api/BMR/generate/{productionPlanId}`

This automatically creates a BMR based on the production plan and linked MFR.

### 3. **Get BMR Details**
**Endpoint:** `GET /api/BMR/{bmrId}`

Retrieve the generated BMR with all sections and requirements.

### 4. **Update Production Plan Status to Planned**
**Endpoint:** `PUT /ProcessGroupGroups/{productionPlanId}`

```json
{
  "status": "Planned"
}
```

### 5. **Create Material Requests**
**Endpoint:** `POST /MaterialRequests`

```json
{
  "productionPlanId": "production-plan-123",
  "materialId": "material-456",
  "requestedQuantity": 500,
  "status": "Pending"
}
```

### 6. **Update Production Plan Status to MaterialRequested**
**Endpoint:** `PUT /ProcessGroupGroups/{productionPlanId}`

```json
{
  "status": "MaterialRequested"
}
```

### 7. **Dispense Materials (Create Inventory Transactions)**
**Endpoint:** `POST /Inventories`

```json
{
  "type": "Subtraction",
  "action": "ProcessInput",
  "value": 500,
  "materialId": "material-456",
  "processId": "process-789"
}
```

### 8. **Update Production Plan Status to MaterialDispensed**
**Endpoint:** `PUT /ProcessGroupGroups/{productionPlanId}`

```json
{
  "status": "MaterialDispensed"
}
```

### 9. **Create Production Tasks for Each Process**
**Endpoint:** `POST /ProductionTasks`

```json
{
  "name": "Weigh Raw Materials",
  "description": "Weigh all raw materials according to batch formula",
  "processId": "process-weighing-123",
  "assignedToId": "operator-456",
  "priority": "High",
  "dueDate": "2024-12-20T10:00:00Z",
  "instructions": "Follow SOP-001 for weighing procedure"
}
```

### 10. **Start Production - Update Status**
**Endpoint:** `PUT /ProcessGroupGroups/{productionPlanId}`

```json
{
  "status": "InProgress",
  "actualStartDate": "2024-12-20T08:30:00Z"
}
```

### 11. **Execute Tasks - Start Task**
**Endpoint:** `PUT /ProductionTasks/{taskId}/start`

Operators start their assigned tasks.

### 12. **Record Process Data in BMR**
**Endpoint:** `POST /api/BMR/{bmrId}/sections`

```json
{
  "title": "Weighing Record",
  "content": "Material A: 250.5kg, Material B: 125.2kg",
  "type": "ProcessData"
}
```

### 13. **Complete Task**
**Endpoint:** `PUT /ProductionTasks/{taskId}/complete`

```json
{
  "comments": "Weighing completed successfully. All materials within specification."
}
```

### 14. **Add Electronic Signature to BMR**
**Endpoint:** `POST /api/BMR/{bmrId}/signatures`

```json
{
  "userId": "operator-456",
  "purpose": "Process Completion",
  "signedDate": "2024-12-20T10:30:00Z"
}
```

### 15. **Record Deviations (if any)**
**Endpoint:** `POST /api/BMR/{bmrId}/deviations`

```json
{
  "title": "Temperature Deviation",
  "description": "Mixing temperature exceeded limit by 2°C",
  "severity": "Minor",
  "correctiveAction": "Adjusted cooling system"
}
```

### 16. **Quality Check Phase**
**Endpoint:** `PUT /ProcessGroupGroups/{productionPlanId}`

```json
{
  "status": "QualityCheck"
}
```

### 17. **Create Quality Checks**
**Endpoint:** `POST /QualityChecks`

```json
{
  "processGroupId": "process-group-123",
  "checkType": "InProcess",
  "parameters": [
    {
      "name": "Weight",
      "expectedValue": "250.0",
      "actualValue": "250.5",
      "unit": "kg",
      "status": "Pass"
    }
  ]
}
```

### 18. **Update BMR Status**
**Endpoint:** `PUT /api/BMR/{bmrId}/status`

**Body:** `"Completed"`

### 19. **Final Production Plan Completion**
**Endpoint:** `PUT /ProcessGroupGroups/{productionPlanId}`

```json
{
  "status": "Completed",
  "actualEndDate": "2024-12-22T15:30:00Z"
}
```

### 20. **Get Final BMR with All Data**
**Endpoint:** `GET /api/BMR/{bmrId}`

Retrieve the complete BMR with all recorded data, signatures, and deviations.

## Workflow Status Progression

```
Production Plan Status Flow:
Draft → Planned → MaterialRequested → MaterialDispensed → InProgress → QualityCheck → Completed

BMR Status Flow:
Draft → InProgress → Completed

Task Status Flow:
Pending → Assigned → InProgress → Completed
```

## Key Integration Points

### Production Plan (ProcessGroupGroup)
- **Drives** the overall batch execution
- **Links** to MFR for manufacturing instructions
- **Tracks** batch-level status and timing

### BMR (Batch Manufacturing Record)
- **Records** all manufacturing activities
- **Captures** process data and signatures
- **Documents** deviations and corrective actions

### Production Tasks
- **Manage** individual operations
- **Assign** work to operators
- **Track** task completion

### Inventory Management
- **Tracks** material dispensing and usage
- **Records** material consumption per process
- **Maintains** material traceability

### Quality Management
- **Ensures** in-process compliance
- **Records** quality check results
- **Manages** deviation workflows

## Compliance Features

### 21 CFR Part 11 Compliance
- ✅ **Electronic Signatures** with user authentication
- ✅ **Audit Trails** for all changes
- ✅ **Data Integrity** with timestamps
- ✅ **Access Controls** with role-based permissions

### GMP Compliance
- ✅ **Batch Records** with complete traceability
- ✅ **Material Tracking** from receipt to usage
- ✅ **Process Documentation** with operator signatures
- ✅ **Deviation Management** with CAPA integration

### Data Integrity (ALCOA+)
- ✅ **Attributable** - User signatures and timestamps
- ✅ **Legible** - Clear digital records
- ✅ **Contemporaneous** - Real-time data capture
- ✅ **Original** - Source data preservation
- ✅ **Accurate** - Validation and verification

## Error Handling

### Common Issues and Solutions

**1. Material Shortage**
- Check inventory levels before starting production
- Create material requests early in the process

**2. Task Dependencies**
- Ensure prerequisite tasks are completed
- Use task dependency management

**3. Quality Failures**
- Record deviations immediately
- Follow CAPA procedures for investigation

**4. Equipment Issues**
- Update machine status to maintenance
- Reassign tasks to alternative equipment

## Best Practices

### Before Starting Production
1. ✅ Verify MFR is approved and current
2. ✅ Confirm all materials are available
3. ✅ Check equipment status and calibration
4. ✅ Assign qualified operators to tasks

### During Production
1. ✅ Record data in real-time
2. ✅ Sign off on completed steps immediately
3. ✅ Report deviations as they occur
4. ✅ Maintain continuous quality monitoring

### After Production
1. ✅ Complete all BMR sections
2. ✅ Obtain all required signatures
3. ✅ Review and approve the batch record
4. ✅ Archive completed documentation

## API Authentication

All endpoints require JWT Bearer token authentication:

```http
Authorization: Bearer <your-jwt-token>
```

## Error Responses

Standard HTTP status codes are used:
- `200` - Success
- `400` - Bad Request
- `401` - Unauthorized
- `404` - Not Found
- `500` - Internal Server Error

---

**Document Version:** 1.0  
**Last Updated:** December 2024  
**Maintained By:** Symmex Development Team