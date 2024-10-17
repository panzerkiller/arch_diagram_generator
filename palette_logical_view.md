## Logical View:

### Logical View Purpose:
Describes the key components, services, and classes.
Helps developers understand how the business logic is structured.
Focuses on relationships and dependencies between components.

* Scheduler Module
Triggers the entire workflow every Sunday at 1 AM.

* Product Fetcher Module
Fetches product data from customers' websites.
Stores product information in the ProductDB.
* Sales Data Loader Module
Receives and loads the latest sales data into the SalesDB.
* Core Process Manager Module
Reads from ProductDB and SalesDB.
Generates price adjustment suggestions in Excel format.
* Notification Service
Sends notifications to the Manager about product and sales data changes.
Notifies the Customer when the new suggestion is ready.
* Review & Approval Service
Handles Manager’s review and approval process.
Ensures only approved suggestions are released.
* User Management Module
Manages authentication (login) and authorization.
Controls user access to product availability and report downloads.


```mermaid
graph TD
    Scheduler --> ProductFetcher
    Scheduler --> SalesDataLoader
    ProductFetcher --> ProductDB
    SalesDataLoader --> SalesDB
    ProductDB --> CoreProcessManager
    SalesDB --> CoreProcessManager
    CoreProcessManager --> ExcelFile[Excel File]
    CoreProcessManager --> NotificationService

    NotificationService --> Manager
    Manager -->|Approve/Reject| ReviewApprovalService
    ReviewApprovalService --> CoreProcessManager

    UserManagement --> Customer
    Customer -->|Login| System
    System -->|Download| ExcelFile



```

```mermaid
classDiagram
    class Scheduler {
        +triggerWorkflow()
    }

    class ProductFetcher {
        +fetchProductData(url: String): List<Product>
        +storeInDB(products: List<Product>)
    }

    class SalesDataLoader {
        +loadSalesData(data: List<Sale>)
        +storeInDB(sales: List<Sale>)
    }

    class CoreProcessManager {
        +generatePriceSuggestions(): ExcelFile
    }

    class NotificationService {
        +notifyManager(message: String)
        +notifyCustomer(message: String)
    }

    class ReviewApprovalService {
        +reviewExcelFile(file: ExcelFile): bool
    }

    class UserManagement {
        +login(user: String, password: String): bool
        +authorize(user: String): bool
    }

    ProductFetcher --> ProductDB : stores data
    SalesDataLoader --> SalesDB : stores data
    CoreProcessManager --> ProductFetcher : reads data
    CoreProcessManager --> SalesDataLoader : reads data
    CoreProcessManager --> ExcelFile
    CoreProcessManager --> NotificationService : notify on completion

    NotificationService --> Manager : notify to review
    Manager --> ReviewApprovalService : reviews Excel file
    ReviewApprovalService --> CoreProcessManager : reprocess if needed

    Customer --> UserManagement : login
    UserManagement --> ExcelFile : allow download

```