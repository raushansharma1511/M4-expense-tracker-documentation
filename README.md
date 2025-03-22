# Flask Expense Tracker API Documentation

## Introduction

The Flask Expense Tracker API is a robust financial management system designed to help users track their income and expenses. It provides a complete backend solution with secure authentication, comprehensive transaction management, and advanced reporting features.

---

## 1. Authentication and User Management

The Authentication and User Management module provides comprehensive functionality for user registration, authentication, profile management, and hierarchical user relationships. The system supports role-based access control with three distinct user roles: Admin, Regular User, and Child User.

### Key Features

#### 1.1 User Roles
- **Admin**: Full access to manage all users, their financial data, and system resources. Can create additional admin accounts.
- **User**: Manage their own financial data (e.g., wallets, transactions, categories). View-only access to their child user’s assets (e.g., via `child_id` parameter in API requests).
- **Child User**: Restricted users created by parent users, with limited permissions to manage only their own data.

#### 1.2 JWT Authentication
- Secure token-based authentication using JWT (JSON Web Tokens)
- Includes access tokens (short-lived) and refresh tokens (longer-lived), with a role field encoded in the token claims
- Tokens must be included in the Authorization header as `Bearer <token>` for protected endpoints.

#### 1.3 User Registration and Verification
*Signup*
- Users register by providing their details (email, username, password, name), and an account verification link will be sent to their email for account verification.
- Once verification is done, the user account will be created, and a default wallet is created upon successful registration.

*Login*
- Users can log in using username or email and password.
- Returns access token (JWT) and refresh token upon successful login.

#### 1.4 Profile Management
- User profile updates (username, name, gender, date of birth)
- Users can change their own email with verification through OTP received on both current and new email.
- If an admin changes an email, a verification link will be sent to the new email, and after verification, the email will be updated.
- Password updates with current password verification.

#### 1.5 Parent-Child Relationships
- Parents can create and monitor child user accounts and their financial data.
- Children must verify their account for successful creation using the link sent to their email.
- Child accounts are linked through a dedicated parent-child relationship model.
- Each parent can have only one child user.

#### 1.6 Account Security
- Email verification for new accounts and email changes
- Password reset functionality with secure tokens
- Account deletion with password validation (password not required if an admin is deleting any user account).

---

## 2. Categories

Categories provide a classification system for organizing financial transactions. The module supports both system-defined default categories and user-created custom categories to enable meaningful organization and reporting.

### Features and Constraints
- **Predefined System Categories**: Default categories (Food, Transport, Utilities, etc.) available to all users.
- **Custom User Categories**: Users can create personalized categories to better organize their financial data.
- **Naming Rules**: Category names must be unique per user (case-insensitive) and cannot duplicate predefined category names.
- **Access Control**: Users can view own and predefined categories but can modify their own categories, while parents can view their child's categories, and admins can see all categories.
- **Deletion Restrictions**: Categories with associated transactions, recurring transactions, or budgets cannot be deleted.
- **Soft Deletion**: Categories are soft-deleted to maintain data integrity in existing transactions.

---

## 3. Wallets

Users can manage their wallets, which store financial information such as balances, allowing users to organize their finances across multiple accounts or purposes.

### Features and Constraints
- **Wallet Management**: Users can create, retrieve, update, and delete their wallets. Admins can manage wallets for any user.
- **Balance Control**: Wallets maintain balance integrity through transaction validation. Balance is read-only and only modified through transactions. Initial balance of the wallet will always be zero.
- **Naming Rules**: Wallet names must be unique per user (case-insensitive).
- **Deletion Restrictions**: Wallets can only be deleted if they have zero balance and no associated transactions.
- **Access Control**: Users can access only their own wallets, parents can view their child's wallets, and admins can manage all wallets.
- **Inter-wallet Transfers**: Users can transfer funds between their own wallets through specialized transactions.

---

## 4. Transactions

Transactions represent financial activities (income and expenses) within the user's wallets. All transactions are soft-deleted to maintain financial records and support accurate reporting.

### Features and Constraints
- **Transaction Types**: Supports two transaction types - CREDIT (income) and DEBIT (expense).
- **Wallet Integration**: Each transaction is linked to a specific wallet and updates the wallet's balance automatically.
- **Category Classification**: Every transaction must be associated with a category for better organization and reporting.
- **Date Tracking**: Transactions include timestamps for chronological tracking and time-based reporting.
- **Access Control**: Users can only access their own transactions, while parents can view their child's transactions, and admins can access any user's transactions.
- **Budget Impact**: Expense transactions automatically affect any matching budget's spent amount.
- **Validation Rules**: The amount must be between 0 and 99999999.99. The category used in the transaction must belong to the user or be global and not be deleted. The wallet used in the transaction must belong to the user and not be deleted.

---

## 5. Budgets

Budgets provide a monthly financial planning tool for tracking and controlling expenses by category. The system supports budget creation, monitoring, and automated notifications when spending approaches or exceeds defined limits.

### Features and Constraints
- **Category-Based Budgeting**: Each budget is tied to a specific expense category for targeted monitoring.
- **Monthly Planning**: Budgets are created for specific month and year combinations, enabling time-based financial planning.
- **Spending Tracking**: The system automatically tracks spending against budgets when transactions are created, updated, or deleted.
- **Threshold Notifications**: Users receive email notifications when spending reaches warning (customizable percentage) or exceeding thresholds.
- **Access Control**: Users can manage their own budgets, while parents can view their child's budgets.
- **Budget Analytics**: Provides percentage calculations, remaining amount tracking, and spending trends.
- **Validation Rules**: System prevents duplicate budgets for the same user-category-month-year combination and validates reasonable budget amounts.

---

## 6. Recurring Transactions

Recurring Transactions automate regular financial activities by scheduling transactions to occur at defined intervals. They allow users to set up recurring payments or income sources that are processed automatically on scheduled dates.

### Features and Constraints
- **Automated Processing**: System automatically creates regular transactions based on user-defined schedules.
- **Frequency Options**: Supports daily, weekly, monthly, and yearly recurring transactions.
- **Time Range Control**: Users can set both start and optional end dates for recurring transactions.
- **Wallet Integration**: All recurring transactions are linked to specific wallets and maintain balance integrity.
- **Category Classification**: Each recurring transaction must be associated with a category for better organization.
- **Email Notifications**: Users receive email notifications when recurring transactions are processed.
- **Validation Rules**: The system prevents creating recurring transactions with invalid dates, insufficient wallet balance, or invalid category/wallet combinations. The wallet used in the transaction must belong to the user and not be deleted. The category used in the transaction must belong to the user or be global and not be deleted.
- **Access Control**: Users can only access their own recurring transactions, while parents can view their child's transactions.

---

## 7. Inter-wallet Transactions

Inter-wallet Transactions enable users to transfer funds between their own wallets, providing flexibility in financial management and organization. This module maintains the integrity of wallet balances while creating a clear record of fund movements.

### Features and Constraints
- **Wallet-to-Wallet Transfers**: Users can move funds between any of their personal wallets.
- **Balance Validation**: System prevents transfers when the source wallet has insufficient funds.
- **Distinct Wallet Requirement**: Source and destination must be different wallets.
- **Ownership Validation**: Both wallets must belong to the same user.
- **Transaction History**: All transfers are tracked with timestamps and descriptions.
- **Hierarchical Access**: Parents can view their child user's transfers, and admins can access all transfers.
- **Deletion Rules**: Soft deletion automatically reverses the transfer effect on wallet balances.

---

## 8. Reports and Analytics

The Reports and Analytics module provides comprehensive financial insights by aggregating transaction data into meaningful summaries, trends, and exportable formats. It helps users track their spending patterns and make informed financial decisions.

### Features and Constraints
- **Transaction Summary Reports**: Detailed overview of financial activities including totals by category and wallet.
- **Spending Trends Analysis**: Category-based spending breakdowns with percentage calculations and visualizations.
- **Data Export Options**: Export transaction history in both PDF and CSV formats for offline analysis.
- **Flexible Date Filtering**: All reports can be filtered by custom date ranges for precise time period analysis.
- **Hierarchical Access**: Parents can view their child's reports, while admins can access reports for any user.
- **Email Delivery**: Exported reports are automatically sent to the user's email address as attachments.
- **Asynchronous Processing**: Large report generation is handled by background tasks to optimize user experience.

---

## 9. Background Tasks (Cron Jobs)
- Permanently removes soft-deleted categories, wallets, and users after a certain period via a scheduled task.

## 10. Utility Scripts
- Terminal script which creates an admin user if none exists.
- Terminal script which creates missing global categories (e.g., Salary, Food) if not present.

## API Endpoints

**Base URL:** `localhost:5000/api/`

### 1. Authentication and User Management
| Method | Endpoint                          | Description                                                                 |
|--------|-----------------------------------|-----------------------------------------------------------------------------|
| POST   | `/auth/sign-up`                  | Registers a new user account and sends verification email                   |
| GET    | `/auth/verify-user/{token}`      | Verifies user's email using the token sent via email                        |
| POST   | `/auth/login`                    | Authenticates user and generates access and refresh tokens                  |
| POST   | `/auth/refresh-token`            | Generates a new access token using a valid refresh token                    |
| POST   | `/auth/logout`                   | Invalidates the current access token                                        |
| GET    | `/users                          | Retrieves all users  information(accessible only by admins)                 |
| GET    | `/users/{id}`                    | Retrieves user profile information                                          |
| PATCH  | `/users/{id}`                    | Updates user profile information                                            |
| POST   | `/users/{id}/update-password`    | Updates user's password                                                     |
| POST   | `/users/{id}/update-email`       | Initiates email change process by sending verification OTPs                 |
| POST   | `/users/{id}/update-email/confirm` | Confirms email change by verifying OTPs                                   |
| DELETE | `/users/{id}`                    | Soft deletes user account and related resources                             |
| POST   | `/auth/reset-password`           | Requests a password reset link for a registered email                       |
| POST   | `/auth/reset-password-confirm/{token}` | Resets password using token received via email                        |
| POST   | `/users/{id}/child`              | Creates a new child user linked to parent account                           |
| GET    | `/users/{id}/child`              | Retrieves child user linked to parent account                               |
| POST   | `/admin/create`                  | Creates a new admin user (accessible only by admins)                        |

### 2. Categories
| Method | Endpoint                          | Description                                                                 |
|--------|-----------------------------------|-----------------------------------------------------------------------------|
| GET    | `/categories`                    | Retrieves paginated list of available categories                            |
| POST   | `/categories`                    | Creates a new custom category                                               |
| GET    | `/categories/{id}`               | Retrieves a specific category's details                                     |
| PATCH  | `/categories/{id}`               | Updates a category's name                                                   |
| DELETE | `/categories/{id}`               | Deletes a category if it has no associated transactions                     |

### 3. Wallets
| Method | Endpoint                          | Description                                                                 |
|--------|-----------------------------------|-----------------------------------------------------------------------------|
| GET    | `/wallets`                       | Retrieves paginated list of available wallets                               |
| POST   | `/wallets`                       | Creates a new wallet                                                        |
| GET    | `/wallets/{id}`                  | Retrieves a specific wallet's details                                       |
| PATCH  | `/wallets/{id}`                  | Updates a wallet's name                                                     |
| DELETE | `/wallets/{id}`                  | Deletes a wallet (requires zero balance and no associated transactions)     |

### 4. Transactions
| Method | Endpoint                          | Description                                                                 |
|--------|-----------------------------------|-----------------------------------------------------------------------------|
| GET    | `/transactions`                  | Retrieves paginated list of transactions with filtering options             |
| POST   | `/transactions`                  | Creates a new transaction                                                   |
| GET    | `/transactions/{id}`             | Retrieves details of a specific transaction                                 |
| PATCH  | `/transactions/{id}`             | Updates a transaction                                                       |
| DELETE | `/transactions/{id}`             | Soft deletes a transaction                                                  |

### 5. Budgets
| Method | Endpoint                          | Description                                                                 |
|--------|-----------------------------------|-----------------------------------------------------------------------------|
| GET    | `/budgets`                       | Retrieves paginated list of budgets with filtering options                  |
| POST   | `/budgets`                       | Creates a new budget for a specific category, month, and year               |
| GET    | `/budgets/{id}`                  | Retrieves details of a specific budget                                      |
| PATCH  | `/budgets/{id}`                  | Updates a budget's amount or category                                       |
| DELETE | `/budgets/{id}`                  | Soft deletes a budget                                                       |

### 6. Recurring Transactions
| Method | Endpoint                          | Description                                                                 |
|--------|-----------------------------------|-----------------------------------------------------------------------------|
| GET    | `/recurring-transactions`        | Retrieves paginated list of recurring transactions with filtering options   |
| POST   | `/recurring-transactions`        | Creates a new recurring transaction                                         |
| GET    | `/recurring-transactions/{id}`   | Retrieves details of a specific recurring transaction                       |
| PATCH  | `/recurring-transactions/{id}`   | Updates a recurring transaction                                             |
| DELETE | `/recurring-transactions/{id}`   | Soft deletes a recurring transaction                                        |

### 7. Inter-wallet Transactions
| Method | Endpoint                          | Description                                                                 |
|--------|-----------------------------------|-----------------------------------------------------------------------------|
| GET    | `/interwallet-transactions`      | Retrieves paginated list of transfers between wallets                       |
| POST   | `/interwallet-transactions`      | Creates a transfer between wallets                                          |
| GET    | `/interwallet-transactions/{id}` | Retrieves details of a specific transfer                                    |
| PATCH  | `/interwallet-transactions/{id}` | Updates a transfer between wallets                                          |
| DELETE | `/interwallet-transactions/{id}` | Soft deletes a transfer between wallets                                     |

### 8. Reports and Analytics
| Method | Endpoint                          | Description                                                                 |
|--------|-----------------------------------|-----------------------------------------------------------------------------|
| GET    | `/transactions/summary-report`   | Generates a comprehensive summary report of transactions with date range filtering |
| GET    | `/transactions/spending-trends`  | Generates category-based spending trends with percentage breakdowns         |
| GET    | `/transactions/history/export`   | Generates and emails a detailed transaction history report in PDF or CSV format |

---



