# Employee Attendance Tracker

A Windows Forms application for tracking employee attendance with secure authentication and role-based access control.

---

## Features

- **Dual-Role Authentication** — Separate interfaces for employees and administrators
- **Secure Login System** — Employee ID and password-based authentication
- **Flexible Status Tracking** — Employees can mark daily attendance as Present or Absent
- **Admin Dashboard** — Comprehensive view of all employee attendance records with date filtering
- **Real-Time Database Operations** — Modern `Microsoft.Data.SqlClient` for efficient connectivity
- **User-Friendly Interface** — Intuitive Windows Forms design for ease of use

---

## Tech Stack

| Layer | Technology |
|---|---|
| Backend | C# / .NET Framework |
| Frontend | Windows Forms |
| Database | Microsoft SQL Server |
| Data Access | Microsoft.Data.SqlClient |
| Configuration | App.config |

---

## Prerequisites

- .NET Framework 4.6.1 or later
- SQL Server (LocalDB or full version)
- Visual Studio 2019 or later

---

## Setup

### 1. Database

Run the following SQL script to create and seed the database:

```sql
CREATE DATABASE EmployeeDB;
GO
USE EmployeeDB;
GO

CREATE TABLE Employees (
    EmployeeID INT PRIMARY KEY IDENTITY(1,1),
    FirstName  NVARCHAR(50)  NOT NULL,
    LastName   NVARCHAR(50)  NOT NULL,
    Password   NVARCHAR(100) NOT NULL,
    IsAdmin    BIT DEFAULT 0
);

CREATE TABLE Attendance (
    AttendanceID   INT PRIMARY KEY IDENTITY(1,1),
    EmployeeID     INT FOREIGN KEY REFERENCES Employees(EmployeeID),
    AttendanceDate DATE        NOT NULL,
    Status         NVARCHAR(20) NOT NULL,
    CONSTRAINT UC_EmployeeDate UNIQUE(EmployeeID, AttendanceDate)
);

-- Sample data
INSERT INTO Employees (FirstName, LastName, Password, IsAdmin) VALUES
('Admin', 'User',  'admin123', 1),
('Ahmet', 'Yılmaz', '12345',  0),
('Ayşe',  'Kaya',   '12345',  0);
```

### 2. Application

1. Clone or download the project
2. Open the solution in Visual Studio
3. Install the required NuGet packages:
   - `Microsoft.Data.SqlClient`
   - `System.Configuration.ConfigurationManager`
4. Update the connection string in `App.config`:

```xml
<configuration>
  <connectionStrings>
    <add name="EmployeeDBConnection"
         connectionString="Server=(localdb)\MSSQLLocalDB;Database=EmployeeDB;Integrated Security=true;TrustServerCertificate=true;"
         providerName="Microsoft.Data.SqlClient" />
  </connectionStrings>
</configuration>
```

5. Build and run the application

---

## Usage

### Login

Employees authenticate using their **Employee ID** and **password**. Based on the `IsAdmin` flag, they are redirected to either the admin dashboard or the employee form.

### Employee View

- Mark daily attendance as **Present** or **Absent**
- View current attendance status for the day
- Secure logout

### Admin View

- View attendance records for all employees
- Filter records by specific date
- Monitor attendance patterns across the organization

---

## Database Schema

### `Employees`

| Column | Type | Description |
|---|---|---|
| EmployeeID | INT (PK, Identity) | Unique employee identifier |
| FirstName | NVARCHAR(50) | First name |
| LastName | NVARCHAR(50) | Last name |
| Password | NVARCHAR(100) | Login password |
| IsAdmin | BIT | Admin flag (0 = employee, 1 = admin) |

### `Attendance`

| Column | Type | Description |
|---|---|---|
| AttendanceID | INT (PK, Identity) | Unique record identifier |
| EmployeeID | INT (FK) | References `Employees.EmployeeID` |
| AttendanceDate | DATE | Date of the attendance record |
| Status | NVARCHAR(20) | `Present` or `Absent` |

> A unique constraint on `(EmployeeID, AttendanceDate)` prevents duplicate entries per day.

---

## Project Structure

```
├── LoginForm.cs        # User authentication
├── EmployeeForm.cs     # Employee attendance interface
├── AdminForm.cs        # Admin dashboard
├── DatabaseHelper.cs   # Database connection and query logic
└── App.config          # Connection string configuration
```
