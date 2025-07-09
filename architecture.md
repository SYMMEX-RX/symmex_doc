# Symmex Backend Architecture Documentation

## Overview
Symmex Backend is a comprehensive pharmaceutical manufacturing management system built with .NET Core 9.0, providing end-to-end solutions for production planning, quality management, equipment maintenance, and regulatory compliance.

## 1. Application Layer Architecture

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                              PRESENTATION LAYER                                 │
├─────────────────────────────────────────────────────────────────────────────────┤
│  Controllers (20+ REST APIs)                                                   │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐              │
│  │   Users     │ │  Materials  │ │  Machines   │ │  Processes  │              │
│  │ Controller  │ │ Controller  │ │ Controller  │ │ Controller  │              │
│  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘              │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐              │
│  │ Inventories │ │   BMR/MFR   │ │ Deviations  │ │ Maintenance │              │
│  │ Controller  │ │ Controller  │ │ Controller  │ │ Controller  │              │
│  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘              │
├─────────────────────────────────────────────────────────────────────────────────┤
│                              BUSINESS LOGIC LAYER                               │
├─────────────────────────────────────────────────────────────────────────────────┤
│  Services (25+ Business Services)                                              │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐              │
│  │   Users     │ │  Materials  │ │  Machines   │ │  Processes  │              │
│  │  Service    │ │  Service    │ │  Service    │ │  Service    │              │
│  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘              │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐              │
│  │ Inventories │ │Material     │ │ Deviations  │ │Notifications│              │
│  │  Service    │ │Review Svc   │ │  Service    │ │  Service    │              │
│  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘              │
├─────────────────────────────────────────────────────────────────────────────────┤
│                              DATA TRANSFER LAYER                                │
├─────────────────────────────────────────────────────────────────────────────────┤
│  DTOs & AutoMapper                                                             │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐              │
│  │   UserDto   │ │MaterialDto  │ │ MachineDto  │ │ ProcessDto  │              │
│  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘              │
├─────────────────────────────────────────────────────────────────────────────────┤
│                              DATA ACCESS LAYER                                  │
├─────────────────────────────────────────────────────────────────────────────────┤
│  Entity Framework Core + DbContext                                             │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐              │
│  │    User     │ │  Material   │ │   Machine   │ │   Process   │              │
│  │   Entity    │ │   Entity    │ │   Entity    │ │   Entity    │              │
│  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘              │
└─────────────────────────────────────────────────────────────────────────────────┘
```

## 2. System Architecture Overview

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                              CLIENT APPLICATIONS                                │
├─────────────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐              │
│  │   Web App   │ │ Mobile App  │ │  Desktop    │ │ Third Party │              │
│  │  (Angular)    │ │ (Angular)   │ │    App      │ │ Integration │              │
│  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘              │
│                                    │                                           │
│                              HTTP/HTTPS REST API                               │
│                                    │                                           │
├─────────────────────────────────────────────────────────────────────────────────┤
│                              API GATEWAY / LOAD BALANCER                       │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                    │                                           │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │                        SYMMEX BACKEND API                               │   │
│  │                         (.NET Core 8.0)                                │   │
│  │                                                                         │   │
│  │  ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐          │   │
│  │  │  Authentication │ │   Authorization │ │    Middleware   │          │   │
│  │  │   (JWT Bearer)  │ │  (Role-based)   │ │   (CORS, etc)   │          │   │
│  │  └─────────────────┘ └─────────────────┘ └─────────────────┘          │   │
│  │                                                                         │   │
│  │  ┌─────────────────────────────────────────────────────────────────┐   │   │
│  │  │                    CORE MODULES                                 │   │   │
│  │  │                                                                 │   │   │
│  │  │  Manufacturing    Quality         Equipment      Materials      │   │   │
│  │  │  ┌─────────────┐  ┌─────────────┐ ┌─────────────┐ ┌───────────┐ │   │   │
│  │  │  │ Production  │  │ Deviations  │ │  Machines   │ │ Inventory │ │   │   │
│  │  │  │ Planning    │  │ Reviews     │ │ Components  │ │ Tracking  │ │   │   │
│  │  │  │ BMR/MFR     │  │ Audits      │ │ Maintenance │ │ Requests  │ │   │   │
│  │  │  └─────────────┘  └─────────────┘ └─────────────┘ └───────────┘ │   │   │
│  │  │                                                                 │   │   │
│  │  │  Document Mgmt   User Management  Notifications   Compliance    │   │   │
│  │  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌───────────┐ │   │   │
│  │  │  │ Documents   │ │ Users       │ │ Multi-      │ │ CAPA      │ │   │   │
│  │  │  │ Approvals   │ │ Groups      │ │ Channel     │ │ Changes   │ │   │   │
│  │  │  │ Signatures  │ │ Permissions │ │ Delivery    │ │ Training  │ │   │   │
│  │  │  └─────────────┘ └─────────────┘ └─────────────┘ └───────────┘ │   │   │
│  │  └─────────────────────────────────────────────────────────────────┘   │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                                    │                                           │
├─────────────────────────────────────────────────────────────────────────────────┤
│                              EXTERNAL SERVICES                                  │
├─────────────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐              │
│  │   Email     │ │     SMS     │ │  WhatsApp   │ │  Firebase   │              │
│  │  Service    │ │   Service   │ │   Service   │ │   (Push)    │              │
│  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘              │
│                                    │                                           │
├─────────────────────────────────────────────────────────────────────────────────┤
│                              DATA PERSISTENCE                                   │
├─────────────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │                        PostgreSQL Database                              │   │
│  │                                                                         │   │
│  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐      │   │
│  │  │   Users &   │ │ Materials & │ │ Machines &  │ │ Processes & │      │   │
│  │  │ Companies   │ │ Inventory   │ │ Components  │ │ Production  │      │   │
│  │  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘      │   │
│  │                                                                         │   │
│  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐      │   │
│  │  │ Documents & │ │ Quality &   │ │Notifications│ │ Audit &     │      │   │
│  │  │ Approvals   │ │ Deviations  │ │ & Logs      │ │ Compliance  │      │   │
│  │  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘      │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────────────────┘
```

## 3. Data Flow Architecture

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                              REQUEST FLOW                                       │
└─────────────────────────────────────────────────────────────────────────────────┘

Client Request
      │
      ▼
┌─────────────┐    JWT Token     ┌─────────────┐    Authorization    ┌─────────────┐
│   API       │ ──────────────► │ Auth        │ ──────────────────► │ Controller  │
│ Middleware  │                 │ Middleware  │                     │             │
└─────────────┘                 └─────────────┘                     └─────────────┘
                                                                           │
                                                                           ▼
┌─────────────┐    DTO          ┌─────────────┐    Entity           ┌─────────────┐
│   Service   │ ◄────────────── │   AutoMapper│ ◄─────────────────  │ Validation  │
│   Layer     │                 │             │                     │             │
└─────────────┘                 └─────────────┘                     └─────────────┘
      │
      ▼
┌─────────────┐    LINQ Query   ┌─────────────┐    SQL Query        ┌─────────────┐
│ Entity      │ ──────────────► │   EF Core   │ ──────────────────► │ PostgreSQL  │
│ Framework   │                 │   DbContext │                     │ Database    │
└─────────────┘                 └─────────────┘                     └─────────────┘
      │                                                                     │
      ▼                                                                     │
┌─────────────┐    Entity       ┌─────────────┐    DTO Response     ┌─────────────┐
│ Business    │ ◄────────────── │   Data      │ ◄─────────────────  │   Result    │
│ Logic       │                 │ Mapping     │                     │   Set       │
└─────────────┘                 └─────────────┘                     └─────────────┘
      │
      ▼
┌─────────────┐    JSON         ┌─────────────┐
│   HTTP      │ ──────────────► │   Client    │
│ Response    │                 │ Application │
└─────────────┘                 └─────────────┘
```

## 4. Security Architecture

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                              SECURITY LAYERS                                    │
├─────────────────────────────────────────────────────────────────────────────────┤
│  Authentication & Authorization                                                │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐              │
│  │ JWT Bearer  │ │ Identity    │ │ Role-based  │ │ Company     │              │
│  │ Tokens      │ │ Framework   │ │ Access      │ │ Isolation   │              │
│  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘              │
├─────────────────────────────────────────────────────────────────────────────────┤
│  Data Protection                                                               │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐              │
│  │ HTTPS/TLS   │ │ Data        │ │ Audit       │ │ Soft        │              │
│  │ Encryption  │ │ Validation  │ │ Logging     │ │ Delete      │              │
│  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘              │
└─────────────────────────────────────────────────────────────────────────────────┘
```

## 5. Core Modules

### Manufacturing Management
- **Production Planning**: Master and child production plans
- **Process Management**: Hierarchical process groups and templates
- **BMR/MFR**: Batch Manufacturing Records and Master Formula Records
- **Material Management**: Inventory tracking, requests, and reviews

### Quality Management
- **Deviations**: Comprehensive deviation tracking and CAPA
- **Non-Conformance**: Product and process non-conformance management
- **Material Reviews**: Automated review workflows with notifications
- **Audits**: Internal and external audit management

### Equipment Management
- **Machine Management**: Status tracking and maintenance scheduling
- **Component Hierarchy**: Machine sub-components with individual maintenance
- **Preventive Maintenance**: Scheduled maintenance with automatic counting
- **Calibration Management**: Equipment calibration scheduling

### Document Management
- **Version Control**: Document versioning with approval workflows
- **Electronic Signatures**: Digital signature capabilities
- **Training Records**: Personnel training and acknowledgments
- **File Storage**: Centralized document storage system

### User & Security Management
- **Multi-tenant Architecture**: Company-based data isolation
- **Role-based Access Control**: Granular permissions system
- **JWT Authentication**: Secure token-based authentication
- **Audit Trails**: Comprehensive activity logging

### Notifications System
- **Multi-channel Delivery**: Email, SMS, WhatsApp, Push notifications
- **Priority-based Messaging**: Critical, high, medium, low priorities
- **Workflow Integration**: Automated notifications for reviews and approvals

## 6. Technology Stack

### Backend Framework
- **.NET Core 8.0**: Modern, cross-platform framework
- **Entity Framework Core**: ORM with PostgreSQL provider
- **AutoMapper**: Object-to-object mapping
- **ASP.NET Core Identity**: Authentication and authorization

### Database
- **PostgreSQL**: Primary database for all application data
- **Entity Framework Migrations**: Database schema management
- **LINQ**: Type-safe database queries

### Security
- **JWT Bearer Tokens**: Stateless authentication
- **HTTPS/TLS**: Encrypted communication
- **Role-based Authorization**: Fine-grained access control
- **Data Validation**: Input sanitization and validation

### External Integrations
- **Email Services**: SMTP integration for notifications
- **SMS Services**: SMS gateway integration
- **WhatsApp Business API**: WhatsApp messaging
- **Firebase**: Push notifications for mobile apps

## 7. Key Features

### Pharmaceutical Compliance
- **21 CFR Part 11**: Electronic records and signatures compliance
- **GMP Compliance**: Good Manufacturing Practice adherence
- **Audit Trails**: Complete activity logging for regulatory compliance
- **Document Control**: Controlled document management system

### Operational Excellence
- **Real-time Monitoring**: Live status tracking of processes and equipment
- **Automated Workflows**: Streamlined approval and review processes
- **Inventory Management**: Real-time inventory tracking with thresholds
- **Maintenance Scheduling**: Preventive and calibration maintenance

### Scalability & Performance
- **Multi-tenant Architecture**: Supports multiple companies/organizations
- **Efficient Database Design**: Optimized queries and indexing
- **Caching Strategies**: Performance optimization through caching
- **Horizontal Scaling**: Designed for cloud deployment

## 8. API Endpoints Summary

### Core Entities (20+ Controllers)
- Users, Companies, Materials, Machines
- Processes, ProcessGroups, ProcessGroupGroups
- Inventories, Suppliers, Manufacturers
- Documents, Notifications, Audits
- MaterialReviews, MachineComponents
- ProductionLines, Workstations
- Deviations, NonConformance

### Specialized Endpoints
- Batch review and approval workflows
- Machine maintenance scheduling
- Material review automation
- Quality management processes
- Document approval workflows

## 9. Deployment Architecture

### Development Environment
- **Local Development**: Docker Compose setup
- **Database**: Local PostgreSQL instance
- **Configuration**: Environment-based settings

### Production Considerations
- **Container Orchestration**: Kubernetes/Docker Swarm
- **Load Balancing**: Multiple API instances
- **Database Clustering**: PostgreSQL high availability
- **Monitoring**: Application performance monitoring
- **Backup Strategy**: Automated database backups

## 10. Future Enhancements

### Planned Features
- **Real-time Dashboard**: Management reporting and analytics
- **Mobile Applications**: Native iOS/Android apps
- **Advanced Analytics**: Business intelligence and reporting
- **Integration APIs**: Third-party system integrations
- **Workflow Engine**: Advanced business process automation

### Scalability Improvements
- **Microservices Architecture**: Service decomposition
- **Event-driven Architecture**: Asynchronous processing
- **CQRS Implementation**: Command Query Responsibility Segregation
- **Message Queuing**: Background job processing

---

**Document Version**: 1.0  
**Last Updated**: July 2025  
**Maintained By**: Symmex Development Team