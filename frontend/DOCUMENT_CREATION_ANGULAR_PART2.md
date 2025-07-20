# Document Creation with Angular Material Design (Part 2)

## Document List Component

Create a document list component in `src/app/features/documents/components/document-list/`:

### document-list.component.ts

```typescript
import { Component, OnInit, ViewChild } from '@angular/core';
import { MatTableDataSource } from '@angular/material/table';
import { MatPaginator } from '@angular/material/paginator';
import { MatSort } from '@angular/material/sort';
import { MatDialog } from '@angular/material/dialog';
import { DocumentService } from '../../services/document.service';
import { Document } from '../../models/document.model';
import { DocumentStatus, DocumentType } from '../../models/document-enums';
import { Router } from '@angular/router';
import { ConfirmDialogComponent } from '../../../../shared/components/confirm-dialog/confirm-dialog.component';

@Component({
  selector: 'app-document-list',
  templateUrl: './document-list.component.html',
  styleUrls: ['./document-list.component.scss']
})
export class DocumentListComponent implements OnInit {
  displayedColumns: string[] = ['name', 'type', 'version', 'status', 'createdDate', 'actions'];
  dataSource = new MatTableDataSource<Document>([]);
  isLoading = true;
  DocumentStatus = DocumentStatus;
  DocumentType = DocumentType;
  
  @ViewChild(MatPaginator) paginator: MatPaginator;
  @ViewChild(MatSort) sort: MatSort;
  
  constructor(
    private documentService: DocumentService,
    private dialog: MatDialog,
    private router: Router
  ) { }

  ngOnInit(): void {
    this.loadDocuments();
  }

  ngAfterViewInit(): void {
    this.dataSource.paginator = this.paginator;
    this.dataSource.sort = this.sort;
  }

  loadDocuments(): void {
    this.isLoading = true;
    this.documentService.getDocuments().subscribe(
      (documents) => {
        this.dataSource.data = documents;
        this.isLoading = false;
      },
      (error) => {
        console.error('Error loading documents:', error);
        this.isLoading = false;
      }
    );
  }

  applyFilter(event: Event): void {
    const filterValue = (event.target as HTMLInputElement).value;
    this.dataSource.filter = filterValue.trim().toLowerCase();

    if (this.dataSource.paginator) {
      this.dataSource.paginator.firstPage();
    }
  }

  getDocumentTypeName(type: DocumentType): string {
    return DocumentType[type];
  }

  getDocumentStatusName(status: DocumentStatus): string {
    return DocumentStatus[status];
  }

  getStatusColor(status: DocumentStatus): string {
    switch (status) {
      case DocumentStatus.Draft: return 'gray';
      case DocumentStatus.InReview: return 'orange';
      case DocumentStatus.ApprovedEffective: return 'green';
      case DocumentStatus.Archived: return 'blue';
      case DocumentStatus.Obsolete: return 'red';
      default: return 'black';
    }
  }

  viewDocument(document: Document): void {
    this.router.navigate(['/documents', document.id]);
  }

  editDocument(document: Document): void {
    this.router.navigate(['/documents', document.id, 'edit']);
  }

  deleteDocument(document: Document): void {
    const dialogRef = this.dialog.open(ConfirmDialogComponent, {
      width: '350px',
      data: { 
        title: 'Confirm Delete', 
        message: `Are you sure you want to delete document "${document.name}"?` 
      }
    });

    dialogRef.afterClosed().subscribe(result => {
      if (result) {
        this.documentService.deleteDocument(document.id!).subscribe(
          () => {
            this.loadDocuments();
          },
          (error) => {
            console.error('Error deleting document:', error);
          }
        );
      }
    });
  }

  submitForReview(document: Document): void {
    const updatedDocument = { ...document, status: DocumentStatus.InReview };
    this.documentService.updateDocument(updatedDocument).subscribe(
      () => {
        this.loadDocuments();
      },
      (error) => {
        console.error('Error submitting document for review:', error);
      }
    );
  }
}
```

### document-list.component.html

```html
<div class="document-list-container">
  <div class="header-actions" fxLayout="row" fxLayoutAlign="space-between center">
    <h1>Documents</h1>
    <button mat-raised-button color="primary" [routerLink]="['/documents/create']">
      <mat-icon>add</mat-icon>
      Create Document
    </button>
  </div>
  
  <mat-card>
    <mat-card-content>
      <div class="filter-container" fxLayout="row" fxLayoutAlign="start center">
        <mat-form-field appearance="outline" fxFlex>
          <mat-label>Filter</mat-label>
          <input matInput (keyup)="applyFilter($event)" placeholder="Search documents...">
          <mat-icon matSuffix>search</mat-icon>
        </mat-form-field>
      </div>
      
      <div class="table-container">
        <table mat-table [dataSource]="dataSource" matSort>
          <!-- Name Column -->
          <ng-container matColumnDef="name">
            <th mat-header-cell *matHeaderCellDef mat-sort-header> Name </th>
            <td mat-cell *matCellDef="let document"> {{document.name}} </td>
          </ng-container>
          
          <!-- Type Column -->
          <ng-container matColumnDef="type">
            <th mat-header-cell *matHeaderCellDef mat-sort-header> Type </th>
            <td mat-cell *matCellDef="let document"> {{getDocumentTypeName(document.type)}} </td>
          </ng-container>
          
          <!-- Version Column -->
          <ng-container matColumnDef="version">
            <th mat-header-cell *matHeaderCellDef mat-sort-header> Version </th>
            <td mat-cell *matCellDef="let document"> {{document.version}} </td>
          </ng-container>
          
          <!-- Status Column -->
          <ng-container matColumnDef="status">
            <th mat-header-cell *matHeaderCellDef mat-sort-header> Status </th>
            <td mat-cell *matCellDef="let document">
              <span class="status-badge" [style.background-color]="getStatusColor(document.status)">
                {{getDocumentStatusName(document.status)}}
              </span>
            </td>
          </ng-container>
          
          <!-- Created Date Column -->
          <ng-container matColumnDef="createdDate">
            <th mat-header-cell *matHeaderCellDef mat-sort-header> Created Date </th>
            <td mat-cell *matCellDef="let document"> {{document.createdDate | date:'medium'}} </td>
          </ng-container>
          
          <!-- Actions Column -->
          <ng-container matColumnDef="actions">
            <th mat-header-cell *matHeaderCellDef> Actions </th>
            <td mat-cell *matCellDef="let document">
              <button mat-icon-button color="primary" (click)="viewDocument(document)" matTooltip="View">
                <mat-icon>visibility</mat-icon>
              </button>
              
              <button mat-icon-button color="accent" (click)="editDocument(document)" 
                      [disabled]="document.status !== DocumentStatus.Draft" matTooltip="Edit">
                <mat-icon>edit</mat-icon>
              </button>
              
              <button mat-icon-button color="warn" (click)="deleteDocument(document)"
                      [disabled]="document.status !== DocumentStatus.Draft" matTooltip="Delete">
                <mat-icon>delete</mat-icon>
              </button>
              
              <button mat-icon-button color="primary" (click)="submitForReview(document)"
                      [disabled]="document.status !== DocumentStatus.Draft" matTooltip="Submit for Review">
                <mat-icon>send</mat-icon>
              </button>
            </td>
          </ng-container>
          
          <tr mat-header-row *matHeaderRowDef="displayedColumns"></tr>
          <tr mat-row *matRowDef="let row; columns: displayedColumns;"></tr>
          
          <!-- Row shown when there is no matching data -->
          <tr class="mat-row" *matNoDataRow>
            <td class="mat-cell" colspan="6">
              <div class="no-data-message" *ngIf="!isLoading">
                No documents found
              </div>
              <div class="loading-message" *ngIf="isLoading">
                Loading documents...
              </div>
            </td>
          </tr>
        </table>
        
        <mat-paginator [pageSizeOptions]="[5, 10, 25, 100]" showFirstLastButtons></mat-paginator>
      </div>
    </mat-card-content>
  </mat-card>
</div>
```

### document-list.component.scss

```scss
.document-list-container {
  padding: 20px;
  
  .header-actions {
    margin-bottom: 20px;
  }
  
  .filter-container {
    margin-bottom: 15px;
  }
  
  .table-container {
    overflow-x: auto;
    
    table {
      width: 100%;
    }
    
    .status-badge {
      padding: 4px 8px;
      border-radius: 12px;
      color: white;
      font-size: 12px;
      font-weight: 500;
    }
    
    .no-data-message, .loading-message {
      padding: 20px;
      text-align: center;
      font-style: italic;
      color: #666;
    }
  }
}
```

## Document Approval Workflow

Create a document approval component in `src/app/features/documents/components/document-approval/`:

### document-approval.component.ts

```typescript
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute, Router } from '@angular/router';
import { FormBuilder, FormGroup, Validators } from '@angular/forms';
import { MatSnackBar } from '@angular/material/snack-bar';
import { DocumentService } from '../../services/document.service';
import { Document, DocumentApproval, DocumentApprovalStatus } from '../../models/document.model';

@Component({
  selector: 'app-document-approval',
  templateUrl: './document-approval.component.html',
  styleUrls: ['./document-approval.component.scss']
})
export class DocumentApprovalComponent implements OnInit {
  document: Document | null = null;
  approvals: DocumentApproval[] = [];
  approvalForm: FormGroup;
  isLoading = true;
  isSubmitting = false;
  documentId: string;
  
  constructor(
    private route: ActivatedRoute,
    private router: Router,
    private fb: FormBuilder,
    private documentService: DocumentService,
    private snackBar: MatSnackBar
  ) { }

  ngOnInit(): void {
    this.documentId = this.route.snapshot.paramMap.get('id') || '';
    this.initForm();
    this.loadDocument();
    this.loadApprovals();
  }

  initForm(): void {
    this.approvalForm = this.fb.group({
      status: [DocumentApprovalStatus.Approved, Validators.required],
      comments: ['', Validators.maxLength(500)]
    });
  }

  loadDocument(): void {
    this.isLoading = true;
    this.documentService.getDocumentById(this.documentId).subscribe(
      (document) => {
        this.document = document;
        this.isLoading = false;
      },
      (error) => {
        console.error('Error loading document:', error);
        this.isLoading = false;
        this.snackBar.open('Error loading document', 'Close', { duration: 3000 });
      }
    );
  }

  loadApprovals(): void {
    this.documentService.getDocumentApprovals(this.documentId).subscribe(
      (approvals) => {
        this.approvals = approvals;
      },
      (error) => {
        console.error('Error loading approvals:', error);
      }
    );
  }

  onSubmit(): void {
    if (this.approvalForm.invalid) {
      return;
    }

    this.isSubmitting = true;
    
    const approval: DocumentApproval = {
      documentId: this.documentId,
      approvedById: localStorage.getItem('userId') || '',
      approvalGroupId: localStorage.getItem('userGroupId') || '',
      status: this.approvalForm.value.status,
      comments: this.approvalForm.value.comments,
      approvalDate: new Date(),
      isLatestApproval: true
    };

    this.documentService.approveDocument(this.documentId, approval).subscribe(
      () => {
        this.isSubmitting = false;
        this.snackBar.open('Document approval submitted successfully', 'Close', { duration: 3000 });
        this.router.navigate(['/documents']);
      },
      (error) => {
        this.isSubmitting = false;
        this.snackBar.open('Error submitting approval', 'Close', { duration: 3000 });
        console.error('Error submitting approval:', error);
      }
    );
  }

  getStatusLabel(status: DocumentApprovalStatus): string {
    switch (status) {
      case DocumentApprovalStatus.Pending: return 'Pending';
      case DocumentApprovalStatus.Approved: return 'Approved';
      case DocumentApprovalStatus.Rejected: return 'Rejected';
      default: return 'Unknown';
    }
  }

  getStatusColor(status: DocumentApprovalStatus): string {
    switch (status) {
      case DocumentApprovalStatus.Pending: return 'orange';
      case DocumentApprovalStatus.Approved: return 'green';
      case DocumentApprovalStatus.Rejected: return 'red';
      default: return 'gray';
    }
  }
}
```

### document-approval.component.html

```html
<div class="document-approval-container">
  <div *ngIf="isLoading" class="loading-spinner">
    <mat-spinner diameter="40"></mat-spinner>
    <p>Loading document...</p>
  </div>
  
  <div *ngIf="!isLoading && document">
    <div class="header-section" fxLayout="row" fxLayoutAlign="space-between center">
      <div>
        <h1>Document Approval</h1>
        <h2>{{ document.name }}</h2>
      </div>
      <button mat-button [routerLink]="['/documents']">
        <mat-icon>arrow_back</mat-icon>
        Back to Documents
      </button>
    </div>
    
    <div fxLayout="row" fxLayout.lt-md="column" fxLayoutGap="20px">
      <!-- Document Details -->
      <mat-card fxFlex="70">
        <mat-card-header>
          <mat-card-title>Document Details</mat-card-title>
        </mat-card-header>
        
        <mat-card-content>
          <div class="document-details">
            <div class="detail-row">
              <span class="label">Name:</span>
              <span class="value">{{ document.name }}</span>
            </div>
            <div class="detail-row">
              <span class="label">Type:</span>
              <span class="value">{{ document.type }}</span>
            </div>
            <div class="detail-row">
              <span class="label">Version:</span>
              <span class="value">{{ document.version }}</span>
            </div>
            <div class="detail-row">
              <span class="label">Status:</span>
              <span class="value">{{ document.status }}</span>
            </div>
            <div class="detail-row">
              <span class="label">Created By:</span>
              <span class="value">{{ document.createdBy?.name || 'Unknown' }}</span>
            </div>
            <div class="detail-row">
              <span class="label">Created Date:</span>
              <span class="value">{{ document.createdDate | date:'medium' }}</span>
            </div>
          </div>
          
          <div class="document-content">
            <h3>Document Content</h3>
            <div class="content-preview" [innerHTML]="document.data"></div>
          </div>
        </mat-card-content>
      </mat-card>
      
      <!-- Approval Form -->
      <div fxFlex="30">
        <mat-card>
          <mat-card-header>
            <mat-card-title>Submit Approval</mat-card-title>
          </mat-card-header>
          
          <mat-card-content>
            <form [formGroup]="approvalForm" (ngSubmit)="onSubmit()">
              <div fxLayout="column" fxLayoutGap="15px">
                <mat-form-field appearance="outline">
                  <mat-label>Approval Status</mat-label>
                  <mat-select formControlName="status">
                    <mat-option [value]="1">Approve</mat-option>
                    <mat-option [value]="2">Reject</mat-option>
                  </mat-select>
                </mat-form-field>
                
                <mat-form-field appearance="outline">
                  <mat-label>Comments</mat-label>
                  <textarea matInput formControlName="comments" rows="4" placeholder="Enter your comments..."></textarea>
                </mat-form-field>
                
                <button mat-raised-button color="primary" type="submit" [disabled]="approvalForm.invalid || isSubmitting">
                  <mat-icon>check_circle</mat-icon>
                  Submit Approval
                </button>
              </div>
            </form>
          </mat-card-content>
        </mat-card>
        
        <!-- Previous Approvals -->
        <mat-card class="approvals-card">
          <mat-card-header>
            <mat-card-title>Approval History</mat-card-title>
          </mat-card-header>
          
          <mat-card-content>
            <div *ngIf="approvals.length === 0" class="no-approvals">
              No approval history available
            </div>
            
            <mat-list *ngIf="approvals.length > 0">
              <mat-list-item *ngFor="let approval of approvals">
                <div mat-line>
                  <span class="approval-status" [style.color]="getStatusColor(approval.status)">
                    {{ getStatusLabel(approval.status) }}
                  </span>
                  by {{ approval.approvedBy?.name || 'Unknown' }}
                </div>
                <div mat-line>{{ approval.approvalDate | date:'medium' }}</div>
                <div mat-line *ngIf="approval.comments">
                  <small>{{ approval.comments }}</small>
                </div>
              </mat-list-item>
            </mat-list>
          </mat-card-content>
        </mat-card>
      </div>
    </div>
  </div>
</div>
```

### document-approval.component.scss

```scss
.document-approval-container {
  padding: 20px;
  
  .loading-spinner {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 300px;
  }
  
  .header-section {
    margin-bottom: 20px;
    
    h1 {
      margin-bottom: 0;
    }
    
    h2 {
      margin-top: 5px;
      color: #666;
    }
  }
  
  .document-details {
    margin-bottom: 20px;
    
    .detail-row {
      display: flex;
      margin-bottom: 10px;
      
      .label {
        font-weight: 500;
        width: 120px;
      }
    }
  }
  
  .document-content {
    border-top: 1px solid #e0e0e0;
    padding-top: 15px;
    
    .content-preview {
      max-height: 300px;
      overflow-y: auto;
      padding: 10px;
      border: 1px solid #e0e0e0;
      border-radius: 4px;
      background-color: #f9f9f9;
    }
  }
  
  .approvals-card {
    margin-top: 20px;
    
    .no-approvals {
      padding: 15px 0;
      font-style: italic;
      color: #666;
    }
    
    .approval-status {
      font-weight: 500;
    }
  }
}
```

## Version Control

To implement version control for documents, add the following functionality to the document service:

### document-version.service.ts

```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { Document } from '../models/document.model';
import { environment } from '../../../../environments/environment';

@Injectable({
  providedIn: 'root'
})
export class DocumentVersionService {
  private apiUrl = `${environment.apiUrl}/Documents`;

  constructor(private http: HttpClient) { }

  createNewVersion(documentId: string, document: Document): Observable<Document> {
    return this.http.post<Document>(`${this.apiUrl}/${documentId}/versions`, document);
  }

  getVersionHistory(documentId: string): Observable<Document[]> {
    return this.http.get<Document[]>(`${this.apiUrl}/${documentId}/versions`);
  }

  compareVersions(documentId: string, versionId1: string, versionId2: string): Observable<any> {
    return this.http.get<any>(`${this.apiUrl}/${documentId}/versions/compare`, {
      params: {
        version1: versionId1,
        version2: versionId2
      }
    });
  }
}
```

## Integration with Backend API

### app.module.ts

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
import { HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http';
import { ReactiveFormsModule } from '@angular/forms';
import { FlexLayoutModule } from '@angular/flex-layout';
import { QuillModule } from 'ngx-quill';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { MaterialModule } from './shared/material.module';
import { AuthInterceptor } from './core/interceptors/auth.interceptor';
import { ErrorInterceptor } from './core/interceptors/error.interceptor';

// Document Components
import { DocumentsModule } from './features/documents/documents.module';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    BrowserAnimationsModule,
    HttpClientModule,
    ReactiveFormsModule,
    FlexLayoutModule,
    QuillModule.forRoot(),
    MaterialModule,
    DocumentsModule,
    AppRoutingModule
  ],
  providers: [
    { provide: HTTP_INTERCEPTORS, useClass: AuthInterceptor, multi: true },
    { provide: HTTP_INTERCEPTORS, useClass: ErrorInterceptor, multi: true }
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

### documents.module.ts

```typescript
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { ReactiveFormsModule } from '@angular/forms';
import { RouterModule } from '@angular/router';
import { FlexLayoutModule } from '@angular/flex-layout';
import { QuillModule } from 'ngx-quill';

import { MaterialModule } from '../../shared/material.module';
import { DocumentCreateComponent } from './components/document-create/document-create.component';
import { DocumentListComponent } from './components/document-list/document-list.component';
import { DocumentViewComponent } from './components/document-view/document-view.component';
import { DocumentEditComponent } from './components/document-edit/document-edit.component';
import { DocumentApprovalComponent } from './components/document-approval/document-approval.component';
import { DocumentVersionHistoryComponent } from './components/document-version-history/document-version-history.component';

@NgModule({
  declarations: [
    DocumentCreateComponent,
    DocumentListComponent,
    DocumentViewComponent,
    DocumentEditComponent,
    DocumentApprovalComponent,
    DocumentVersionHistoryComponent
  ],
  imports: [
    CommonModule,
    ReactiveFormsModule,
    RouterModule,
    FlexLayoutModule,
    QuillModule,
    MaterialModule
  ],
  exports: [
    DocumentCreateComponent,
    DocumentListComponent,
    DocumentViewComponent,
    DocumentEditComponent,
    DocumentApprovalComponent,
    DocumentVersionHistoryComponent
  ]
})
export class DocumentsModule { }
```

## Conclusion

This implementation provides a comprehensive document management system using Angular Material Design that integrates with the Symmex Backend API. The system includes:

1. Document creation with rich text editing
2. File attachments
3. Document listing with filtering and sorting
4. Document approval workflow
5. Version control
6. Integration with backend API

The UI is built using Angular Material components for a consistent and professional look and feel. The system is modular and can be extended with additional features as needed.