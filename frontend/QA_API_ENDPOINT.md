# QA (Quality Assurance) API Endpoints Documentation

## **Audit Management Endpoints**

### **1. Create Audit**
- **Method**: POST
- **Endpoint**: `/audits`
- **Description**: Create a new quality audit
- **Body**: AuditDto with audit details

### **2. Get Audit by ID**
- **Method**: GET
- **Endpoint**: `/audits/{id}`
- **Description**: Retrieve specific audit details

### **3. Get All Audits**
- **Method**: GET
- **Endpoint**: `/audits/company/{companyId}`
- **Query Parameters**: `auditType` (optional)
- **Description**: Get all audits for a company

### **4. Update Audit**
- **Method**: PUT
- **Endpoint**: `/audits/{id}`
- **Description**: Update audit information

### **5. Delete Audit**
- **Method**: DELETE
- **Endpoint**: `/audits/{id}`
- **Description**: Delete an audit

### **6. Add Audit Finding**
- **Method**: POST
- **Endpoint**: `/audits/{auditId}/findings`
- **Description**: Add finding to an audit

### **7. Update Audit Finding**
- **Method**: PUT
- **Endpoint**: `/audits/findings/{findingId}`
- **Description**: Update audit finding details

### **8. Delete Audit Finding**
- **Method**: DELETE
- **Endpoint**: `/audits/findings/{findingId}`
- **Description**: Remove audit finding

### **9. Link Finding to CAPA**
- **Method**: PATCH
- **Endpoint**: `/audits/findings/{findingId}/link-capa/{capaId}`
- **Description**: Link audit finding to CAPA

### **10. Update Audit Status**
- **Method**: PATCH
- **Endpoint**: `/audits/{auditId}/status`
- **Description**: Update audit status

## **CAPA (Corrective and Preventive Action) Endpoints**

### **11. Create CAPA**
- **Method**: POST
- **Endpoint**: `/capas`
- **Description**: Create new CAPA record

### **12. Get CAPA by ID**
- **Method**: GET
- **Endpoint**: `/capas/{id}`
- **Description**: Retrieve specific CAPA details

### **13. Get All CAPAs**
- **Method**: GET
- **Endpoint**: `/capas/company/{companyId}`
- **Description**: Get all CAPAs for a company

### **14. Update CAPA**
- **Method**: PUT
- **Endpoint**: `/capas/{id}`
- **Description**: Update CAPA information

### **15. Delete CAPA**
- **Method**: DELETE
- **Endpoint**: `/capas/{id}`
- **Description**: Delete a CAPA

### **16. Update CAPA Status**
- **Method**: PATCH
- **Endpoint**: `/capas/{capaId}/status`
- **Description**: Update CAPA status

### **17. Log Effectiveness Check**
- **Method**: POST
- **Endpoint**: `/capas/{capaId}/effectivenesscheck`
- **Description**: Log CAPA effectiveness check

## **Training Records Endpoints**

### **18. Create Training Record**
- **Method**: POST
- **Endpoint**: `/trainingrecords`
- **Description**: Create new training record

### **19. Get Training Record by ID**
- **Method**: GET
- **Endpoint**: `/trainingrecords/{id}`
- **Description**: Retrieve specific training record

### **20. Get Training Records for User**
- **Method**: GET
- **Endpoint**: `/trainingrecords/user/{userId}/company/{companyId}`
- **Description**: Get all training records for a specific user

### **21. Get Training Records for Item**
- **Method**: GET
- **Endpoint**: `/trainingrecords/item/{trainableItemId}/type/{trainableItemType}/company/{companyId}`
- **Description**: Get training records for specific item/document

### **22. Update Training Record**
- **Method**: PUT
- **Endpoint**: `/trainingrecords/{id}`
- **Description**: Update training record information

### **23. Delete Training Record**
- **Method**: DELETE
- **Endpoint**: `/trainingrecords/{id}`
- **Description**: Delete training record

### **24. Mark Training Completed**
- **Method**: PATCH
- **Endpoint**: `/trainingrecords/{id}/complete`
- **Description**: Mark training as completed with assessment

## **Material Review Endpoints**

### **25. Create Material Review**
- **Method**: POST
- **Endpoint**: `/materialreviews`
- **Description**: Create new material review

### **26. Get All Material Reviews**
- **Method**: GET
- **Endpoint**: `/materialreviews`
- **Description**: Get all material reviews for company

### **27. Get Pending Material Reviews**
- **Method**: GET
- **Endpoint**: `/materialreviews/pending`
- **Description**: Get pending material reviews requiring action

### **28. Get Material Reviews by Material**
- **Method**: GET
- **Endpoint**: `/materialreviews/byMaterial/{materialId}`
- **Description**: Get reviews for specific material

### **29. Get Material Review by ID**
- **Method**: GET
- **Endpoint**: `/materialreviews/{id}`
- **Description**: Retrieve specific material review

### **30. Update Material Review**
- **Method**: PUT
- **Endpoint**: `/materialreviews/{id}`
- **Description**: Update material review information

### **31. Approve Material Review**
- **Method**: POST
- **Endpoint**: `/materialreviews/{id}/approve`
- **Description**: Approve material review

### **32. Reject Material Review**
- **Method**: POST
- **Endpoint**: `/materialreviews/{id}/reject`
- **Description**: Reject material review

### **33. Delete Material Review**
- **Method**: DELETE
- **Endpoint**: `/materialreviews/{id}`
- **Description**: Delete material review

## **Change Request Endpoints**

### **34. Create Change Request**
- **Method**: POST
- **Endpoint**: `/changerequests`
- **Description**: Create new change request

### **35. Get Change Request by ID**
- **Method**: GET
- **Endpoint**: `/changerequests/{id}`
- **Description**: Retrieve specific change request

### **36. Get All Change Requests**
- **Method**: GET
- **Endpoint**: `/changerequests/company/{companyId}`
- **Query Parameters**: `status` (optional)
- **Description**: Get all change requests for company

### **37. Update Change Request**
- **Method**: PUT
- **Endpoint**: `/changerequests/{id}`
- **Description**: Update change request information

### **38. Delete Change Request**
- **Method**: DELETE
- **Endpoint**: `/changerequests/{id}`
- **Description**: Delete change request

### **39. Update Change Request Status**
- **Method**: PATCH
- **Endpoint**: `/changerequests/{id}/status`
- **Description**: Update change request status

## **Deviation Management Endpoints**

### **40. Create Deviation**
- **Method**: POST
- **Endpoint**: `/deviations`
- **Description**: Create new deviation record

### **41. Get Deviation by ID**
- **Method**: GET
- **Endpoint**: `/deviations/{id}`
- **Description**: Retrieve specific deviation

### **42. Get All Deviations**
- **Method**: GET
- **Endpoint**: `/deviations`
- **Description**: Get all deviations for company

### **43. Update Deviation**
- **Method**: PUT
- **Endpoint**: `/deviations/{id}`
- **Description**: Update deviation information

### **44. Delete Deviation**
- **Method**: DELETE
- **Endpoint**: `/deviations/{id}`
- **Description**: Delete deviation

## **Non-Conformance Endpoints**

### **45. Create Non-Conformance**
- **Method**: POST
- **Endpoint**: `/nonconformance`
- **Description**: Create new non-conformance record

### **46. Get Non-Conformance by ID**
- **Method**: GET
- **Endpoint**: `/nonconformance/{id}`
- **Description**: Retrieve specific non-conformance

### **47. Get All Non-Conformances**
- **Method**: GET
- **Endpoint**: `/nonconformance`
- **Description**: Get all non-conformances for company

### **48. Update Non-Conformance**
- **Method**: PUT
- **Endpoint**: `/nonconformance/{id}`
- **Description**: Update non-conformance information

### **49. Delete Non-Conformance**
- **Method**: DELETE
- **Endpoint**: `/nonconformance/{id}`
- **Description**: Delete non-conformance

## **Authentication & Authorization**
- All endpoints require JWT Bearer token authentication
- Header: `Authorization: Bearer <token>`
- All endpoints are company-scoped and return data only for the authenticated user's company

## **Key Differences: QA vs QC**

### **QA (Quality Assurance) - Focus Areas:**
- **Process Improvement**: Audits, CAPAs, Change Requests
- **Training & Competency**: Training records, certifications
- **System Management**: Material reviews, deviations, non-conformances
- **Preventive Actions**: Proactive quality management

### **QC (Quality Control) - Focus Areas:**
- **Testing & Analysis**: Sample testing, laboratory results
- **Product Inspection**: Sampling, testing dashboards
- **Compliance Verification**: Test results, specifications
- **Reactive Actions**: Product-specific quality checks

## **Response Format**
All endpoints return JSON with appropriate DTOs containing the requested data fields.

## **Error Responses**
- `400 Bad Request`: Invalid request data
- `401 Unauthorized`: Invalid or missing authentication token
- `403 Forbidden`: User does not have access to the requested data
- `404 Not Found`: Requested resource not found
- `500 Internal Server Error`: Server-side error occurred