# Document Creation with Angular Material Design

![Angular Material Logo](https://material.angular.io/assets/img/angular-material-logo.svg)

## Overview
This guide demonstrates how to implement a document creation system using Angular Material Design that integrates with the Symmex Backend API. The implementation includes document creation, editing, approval workflows, and version control.

## Table of Contents
1. [Project Setup](#project-setup)
2. [Component Structure](#component-structure)
3. [Document Models](#document-models)
4. [Document Service](#document-service)
5. [Document Creation Component](#document-creation-component)
6. [Document List Component](#document-list-component)
7. [Document Approval Workflow](#document-approval-workflow)
8. [Version Control](#version-control)
9. [File Attachments](#file-attachments)
10. [Integration with Backend API](#integration-with-backend-api)

## Project Setup

### Prerequisites
- Node.js (v14+)
- Angular CLI (v12+)
- Angular Material

### Installation

```bash
# Install Angular CLI
npm install -g @angular/cli

# Create new Angular project
ng new symmex-document-management --routing=true --style=scss

# Navigate to project directory
cd symmex-document-management

# Add Angular Material
ng add @angular/material

# Install additional dependencies
npm install @angular/flex-layout @ngx-formly/core @ngx-formly/material ngx-quill
```

### Project Structure

```
src/
├── app/
│   ├── core/                 # Core services, guards, interceptors
│   ├── shared/               # Shared components, directives, pipes
│   ├── features/
│   │   └── documents/        # Document feature module
│   │       ├── components/   # Document components
│   │       ├── models/       # Document models/interfaces
│   │       ├── services/     # Document services
│   │       └── documents.module.ts
│   ├── app-routing.module.ts
│   └── app.module.ts
├── assets/
└── environments/
```

## Document Models

Create the following models in `src/app/features/documents/models/`:

### document.model.ts

```typescript
import { DocumentType, DocumentStatus } from './document-enums';

export interface Document {
  id?: string;
  uniqueId?: string;
  companyId?: string;
  type: DocumentType;
  name: string;
  data?: string;
  version: string;
  status: DocumentStatus;
  previous?: Document;
  expiryDate?: Date;
  effectiveDate?: Date;
  createdById: string;
  createdBy?: any;
  updatedById?: string;
  updatedBy?: any;
  labelValues?: any[];
  users?: any[];
  userGroups?: any[];
  approvals?: DocumentApproval[];
  files?: any[];
}

export interface DocumentApproval {
  id?: string;
  documentId: string;
  approvedById: string;
  approvedBy?: any;
  approvalGroupId: string;
  approvalGroup?: any;
  status: DocumentApprovalStatus;
  comments?: string;
  approvalDate: Date;
  isLatestApproval: boolean;
}

export enum DocumentApprovalStatus {
  Pending = 0,
  Approved = 1,
  Rejected = 2
}
```

### document-enums.ts

```typescript
export enum DocumentType {
  SOP = 0,
  WorkInstruction = 1,
  Policy = 2,
  Form = 3,
  Template = 4,
  Report = 5,
  Manual = 6,
  Other = 7
}

export enum DocumentStatus {
  Draft = 0,
  InReview = 1,
  ApprovedEffective = 2,
  Archived = 3,
  Obsolete = 4
}
```

## Document Service

Create a service to handle API communication in `src/app/features/documents/services/`:

### document.service.ts

```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { Document, DocumentApproval } from '../models/document.model';
import { environment } from '../../../../environments/environment';

@Injectable({
  providedIn: 'root'
})
export class DocumentService {
  private apiUrl = `${environment.apiUrl}/Documents`;

  constructor(private http: HttpClient) { }

  getDocuments(): Observable<Document[]> {
    return this.http.get<Document[]>(this.apiUrl);
  }

  getDocumentById(id: string): Observable<Document> {
    return this.http.get<Document>(`${this.apiUrl}/${id}`);
  }

  createDocument(document: Document): Observable<Document> {
    return this.http.post<Document>(this.apiUrl, document);
  }

  updateDocument(document: Document): Observable<Document> {
    return this.http.put<Document>(`${this.apiUrl}/${document.id}`, document);
  }

  deleteDocument(id: string): Observable<void> {
    return this.http.delete<void>(`${this.apiUrl}/${id}`);
  }

  getDocumentApprovals(documentId: string): Observable<DocumentApproval[]> {
    return this.http.get<DocumentApproval[]>(`${this.apiUrl}/${documentId}/approvals`);
  }

  approveDocument(documentId: string, approval: DocumentApproval): Observable<DocumentApproval> {
    return this.http.post<DocumentApproval>(`${this.apiUrl}/${documentId}/approvals`, approval);
  }

  uploadFile(documentId: string, file: File): Observable<any> {
    const formData = new FormData();
    formData.append('file', file);
    
    return this.http.post<any>(`${this.apiUrl}/${documentId}/files`, formData);
  }
}
```

## Document Creation Component

Create a document creation component in `src/app/features/documents/components/document-create/`:

### document-create.component.ts

```typescript
import { Component, OnInit } from '@angular/core';
import { FormBuilder, FormGroup, Validators } from '@angular/forms';
import { MatSnackBar } from '@angular/material/snack-bar';
import { Router } from '@angular/router';
import { DocumentService } from '../../services/document.service';
import { Document } from '../../models/document.model';
import { DocumentType, DocumentStatus } from '../../models/document-enums';

@Component({
  selector: 'app-document-create',
  templateUrl: './document-create.component.html',
  styleUrls: ['./document-create.component.scss']
})
export class DocumentCreateComponent implements OnInit {
  documentForm: FormGroup;
  documentTypes = Object.keys(DocumentType).filter(key => isNaN(Number(key)));
  isSubmitting = false;
  files: File[] = [];
  editorContent = '';
  
  constructor(
    private fb: FormBuilder,
    private documentService: DocumentService,
    private snackBar: MatSnackBar,
    private router: Router
  ) { }

  ngOnInit(): void {
    this.initForm();
  }

  initForm(): void {
    this.documentForm = this.fb.group({
      name: ['', [Validators.required, Validators.maxLength(100)]],
      type: [DocumentType.SOP, Validators.required],
      version: ['1.0', Validators.required],
      users: [[]],
      userGroups: [[]]
    });
  }

  onSubmit(): void {
    if (this.documentForm.invalid) {
      return;
    }

    this.isSubmitting = true;
    
    const document: Document = {
      ...this.documentForm.value,
      data: this.editorContent,
      status: DocumentStatus.Draft,
      createdById: localStorage.getItem('userId') || '',
      companyId: localStorage.getItem('companyId') || ''
    };

    this.documentService.createDocument(document).subscribe(
      (result) => {
        // Upload files if any
        if (this.files.length > 0 && result.id) {
          this.uploadFiles(result.id);
        } else {
          this.handleSuccess();
        }
      },
      (error) => {
        this.isSubmitting = false;
        this.snackBar.open('Error creating document', 'Close', { duration: 3000 });
        console.error('Error creating document:', error);
      }
    );
  }

  uploadFiles(documentId: string): void {
    let uploadedCount = 0;
    
    this.files.forEach(file => {
      this.documentService.uploadFile(documentId, file).subscribe(
        () => {
          uploadedCount++;
          if (uploadedCount === this.files.length) {
            this.handleSuccess();
          }
        },
        (error) => {
          this.isSubmitting = false;
          this.snackBar.open(`Error uploading file ${file.name}`, 'Close', { duration: 3000 });
          console.error('Error uploading file:', error);
        }
      );
    });
  }

  handleSuccess(): void {
    this.isSubmitting = false;
    this.snackBar.open('Document created successfully', 'Close', { duration: 3000 });
    this.router.navigate(['/documents']);
  }

  onFileSelected(event: any): void {
    const files = event.target.files;
    if (files) {
      for (let i = 0; i < files.length; i++) {
        this.files.push(files[i]);
      }
    }
  }

  removeFile(index: number): void {
    this.files.splice(index, 1);
  }

  onEditorContentChange(content: string): void {
    this.editorContent = content;
  }
}
```

### document-create.component.html

```html
<div class="document-create-container">
  <mat-card>
    <mat-card-header>
      <mat-card-title>Create New Document</mat-card-title>
    </mat-card-header>
    
    <mat-card-content>
      <form [formGroup]="documentForm" (ngSubmit)="onSubmit()">
        <div fxLayout="column" fxLayoutGap="20px">
          <!-- Document Name -->
          <mat-form-field appearance="outline" fxFlex>
            <mat-label>Document Name</mat-label>
            <input matInput formControlName="name" placeholder="Enter document name">
            <mat-error *ngIf="documentForm.get('name')?.hasError('required')">
              Document name is required
            </mat-error>
          </mat-form-field>
          
          <!-- Document Type -->
          <mat-form-field appearance="outline" fxFlex>
            <mat-label>Document Type</mat-label>
            <mat-select formControlName="type">
              <mat-option *ngFor="let type of documentTypes" [value]="DocumentType[type]">
                {{ type }}
              </mat-option>
            </mat-select>
          </mat-form-field>
          
          <!-- Document Version -->
          <mat-form-field appearance="outline" fxFlex>
            <mat-label>Version</mat-label>
            <input matInput formControlName="version" placeholder="1.0">
          </mat-form-field>
          
          <!-- Rich Text Editor -->
          <div class="editor-container">
            <h3>Document Content</h3>
            <quill-editor 
              [styles]="{height: '300px'}"
              (onContentChanged)="onEditorContentChange($event.html)">
            </quill-editor>
          </div>
          
          <!-- File Attachments -->
          <div class="file-upload-section">
            <h3>Attachments</h3>
            <button type="button" mat-raised-button color="primary" (click)="fileInput.click()">
              <mat-icon>attach_file</mat-icon>
              Add Files
            </button>
            <input hidden #fileInput type="file" multiple (change)="onFileSelected($event)">
            
            <div class="file-list" *ngIf="files.length > 0">
              <mat-list>
                <mat-list-item *ngFor="let file of files; let i = index">
                  <mat-icon mat-list-icon>insert_drive_file</mat-icon>
                  <div mat-line>{{ file.name }}</div>
                  <div mat-line>{{ file.size | number }} bytes</div>
                  <button mat-icon-button color="warn" (click)="removeFile(i)">
                    <mat-icon>delete</mat-icon>
                  </button>
                </mat-list-item>
              </mat-list>
            </div>
          </div>
        </div>
        
        <div class="form-actions" fxLayout="row" fxLayoutAlign="end center" fxLayoutGap="10px">
          <button type="button" mat-button [routerLink]="['/documents']">Cancel</button>
          <button type="submit" mat-raised-button color="primary" [disabled]="documentForm.invalid || isSubmitting">
            <mat-icon>save</mat-icon>
            Save Document
          </button>
        </div>
      </form>
    </mat-card-content>
  </mat-card>
</div>
```

### document-create.component.scss

```scss
.document-create-container {
  padding: 20px;
  
  mat-card {
    max-width: 900px;
    margin: 0 auto;
  }
  
  .editor-container {
    margin-bottom: 20px;
  }
  
  .file-upload-section {
    margin: 20px 0;
    
    .file-list {
      margin-top: 15px;
      border: 1px solid #e0e0e0;
      border-radius: 4px;
      padding: 10px;
      max-height: 200px;
      overflow-y: auto;
    }
  }
  
  .form-actions {
    margin-top: 30px;
  }
}
```
```