# QA (Quality Assurance) Logs API Endpoints

This document outlines the API endpoints available for accessing and managing Quality Assurance logs in the Symmex system.

## Audit Logs

### Get Audit Logs
- **Method**: GET
- **Endpoint**: `/audits/company/{companyId}`
- **Description**: Retrieve all audit records for a company
- **Query Parameters**: `auditType` (optional)
- **Example**: `GET /audits/company/company-123?auditType=Internal`

### Get Audit Details
- **Method**: GET
- **Endpoint**: `/audits/{id}`
- **Description**: Get detailed information about a specific audit
- **Example**: `GET /audits/audit-123`

### Get Audit Findings
- **Method**: GET
- **Endpoint**: `/audits/{auditId}/findings`
- **Description**: Get all findings associated with an audit
- **Example**: `GET /audits/audit-123/findings`

## CAPA Logs (Corrective and Preventive Actions)

### Get CAPA Records
- **Method**: GET
- **Endpoint**: `/capas/company/{companyId}`
- **Description**: Retrieve all CAPA records for a company
- **Example**: `GET /capas/company/company-123`

### Get CAPA Details
- **Method**: GET
- **Endpoint**: `/capas/{id}`
- **Description**: Get detailed information about a specific CAPA
- **Example**: `GET /capas/capa-123`

### Get CAPA Effectiveness Checks
- **Method**: GET
- **Endpoint**: `/capas/{capaId}/effectivenesschecks`
- **Description**: Get all effectiveness checks for a CAPA
- **Example**: `GET /capas/capa-123/effectivenesschecks`

## Training Records

### Get All Training Records
- **Method**: GET
- **Endpoint**: `/trainingrecords/company/{companyId}`
- **Description**: Retrieve all training records for a company
- **Example**: `GET /trainingrecords/company/company-123`

### Get User Training Records
- **Method**: GET
- **Endpoint**: `/trainingrecords/user/{userId}/company/{companyId}`
- **Description**: Get training records for a specific user
- **Example**: `GET /trainingrecords/user/user-123/company/company-123`

### Get Item Training Records
- **Method**: GET
- **Endpoint**: `/trainingrecords/item/{trainableItemId}/type/{trainableItemType}/company/{companyId}`
- **Description**: Get training records for a specific item (document, process, etc.)
- **Example**: `GET /trainingrecords/item/doc-123/type/Document/company/company-123`

## Material Review Logs

### Get All Material Reviews
- **Method**: GET
- **Endpoint**: `/materialreviews`
- **Description**: Get all material review records for the company
- **Example**: `GET /materialreviews`

### Get Pending Material Reviews
- **Method**: GET
- **Endpoint**: `/materialreviews/pending`
- **Description**: Get material reviews that require action
- **Example**: `GET /materialreviews/pending`

### Get Material Reviews by Material
- **Method**: GET
- **Endpoint**: `/materialreviews/byMaterial/{materialId}`
- **Description**: Get reviews for a specific material
- **Example**: `GET /materialreviews/byMaterial/material-123`

## Change Request Logs

### Get All Change Requests
- **Method**: GET
- **Endpoint**: `/changerequests/company/{companyId}`
- **Description**: Get all change requests for a company
- **Query Parameters**: `status` (optional)
- **Example**: `GET /changerequests/company/company-123?status=Pending`

### Get Change Request Details
- **Method**: GET
- **Endpoint**: `/changerequests/{id}`
- **Description**: Get detailed information about a specific change request
- **Example**: `GET /changerequests/cr-123`

## Deviation Logs

### Get All Deviations
- **Method**: GET
- **Endpoint**: `/deviations`
- **Description**: Get all deviation records for the company
- **Example**: `GET /deviations`

### Get Deviation Details
- **Method**: GET
- **Endpoint**: `/deviations/{id}`
- **Description**: Get detailed information about a specific deviation
- **Example**: `GET /deviations/dev-123`

## Non-Conformance Logs

### Get All Non-Conformances
- **Method**: GET
- **Endpoint**: `/nonconformance`
- **Description**: Get all non-conformance records for the company
- **Example**: `GET /nonconformance`

### Get Non-Conformance Details
- **Method**: GET
- **Endpoint**: `/nonconformance/{id}`
- **Description**: Get detailed information about a specific non-conformance
- **Example**: `GET /nonconformance/nc-123`

## Document Acknowledgement Logs

### Get Document Acknowledgements
- **Method**: GET
- **Endpoint**: `/documents/{documentId}/acknowledgements`
- **Description**: Get all user acknowledgements for a document
- **Example**: `GET /documents/doc-123/acknowledgements`

### Get User Document Acknowledgements
- **Method**: GET
- **Endpoint**: `/users/{userId}/documentacknowledgements`
- **Description**: Get all documents acknowledged by a specific user
- **Example**: `GET /users/user-123/documentacknowledgements`

## Authentication

All endpoints require JWT Bearer token authentication:

```http
Authorization: Bearer <your-jwt-token>
```

## Response Format

All endpoints return JSON with appropriate data structures containing the requested log information.

## Error Responses

- `401 Unauthorized`: Invalid or missing authentication token
- `403 Forbidden`: User does not have access to the requested logs
- `404 Not Found`: Requested log not found
- `500 Internal Server Error`: Server-side error occurred

---

**Document Version:** 1.0  
**Last Updated:** December 2024  
**Maintained By:** Symmex Development Team