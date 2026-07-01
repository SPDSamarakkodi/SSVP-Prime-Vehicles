# SSVP Prime Vehicles

A desktop vehicle sales and customization application built with **WPF** and the **MVVM** pattern. SSVP Prime Vehicles lets users register, sign in, browse vehicle models, configure default or fully customized builds, complete purchases, view billing details, request returns, and contact customer support.

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Technology Stack](#technology-stack)
- [System Requirements](#system-requirements)
- [Getting Started](#getting-started)
- [Database Setup](#database-setup)
- [Configuration](#configuration)
- [Application Workflow](#application-workflow)
- [Vehicle Models & Customization](#vehicle-models--customization)
- [Project Structure](#project-structure)
- [Architecture](#architecture)
- [NuGet Dependencies](#nuget-dependencies)
- [Known Limitations](#known-limitations)
- [License](#license)

---

## Overview

**SSVP Prime Vehicles** is a Windows desktop application for a fictional automotive brand (SSVP). It provides an end-to-end experience from user onboarding through vehicle selection, customization, payment capture, billing, and post-purchase support.

The app launches at `MainWindow.xaml`, which hosts public-facing views (Home, Login, Sign Up, About). After successful authentication, users enter the `DashBoard` window with a sidebar navigation system for the main application features.

---

## Features

### Public Area (Main Window)
- **Home** — Welcome screen with brand messaging and call-to-action
- **Login** — User authentication against a SQL Server LocalDB database (max 3 failed attempts)
- **Sign Up** — New user registration with username, password, name, and date of birth
- **About** — Company information and branding

### Dashboard (Authenticated Area)
- **Dashboard Home** — Landing view after login
- **Models** — Browse three vehicle lines: **Lexus**, **Prius**, and **Aqua**, with detailed spec windows
- **Customize** — Choose default or full customization for each model
- **Purchase Details** — Review purchase-related information
- **Return** — Search vehicle records, view order details, and initiate return/contact via email
- **Support** — Submit customer service inquiries via email
- **Logout** — Return to the main window

### Vehicle Configuration
- **Default configuration** — Quick purchase with minimal options (e.g., Black/White color, quantity)
- **Full customization** — Configure color, quantity, seat material, dashboard type, steering wheel, rim size, and door style
- **Dynamic pricing** — Base price plus option surcharges, multiplied by quantity
- **Delivery estimate** — Calculated based on order quantity

### Purchase & Billing
- **Customer Payment Details** — Collect NIC, name, address, country, telephone, and email
- **SSVP Bill** — Display vehicle and customer billing data; OTP email verification; purchase cancellation support

---

## Technology Stack

| Category | Technology |
|----------|------------|
| Language | C# |
| Framework | .NET Framework 4.7.2 |
| UI | WPF (Windows Presentation Foundation) |
| Pattern | MVVM (Model-View-ViewModel) |
| Database | SQL Server LocalDB (`MSSQLLocalDB`) |
| Data Access | ADO.NET (`System.Data.SqlClient`) |
| Email | `System.Net.Mail` (SMTP via Gmail) |
| UI Libraries | Material Design, MahApps.Metro, FontAwesome.Sharp, Syncfusion WPF, WpfAnimatedGif |

---

## System Requirements

- **OS:** Windows 10 or later
- **IDE:** Visual Studio 2019 or later (recommended: Visual Studio 2022)
- **.NET:** .NET Framework 4.7.2 Developer Pack
- **Database:** SQL Server Express LocalDB (included with Visual Studio)
- **Workload:** .NET desktop development

---

## Getting Started

### 1. Clone or Download

```bash
git clone <repository-url>
cd SSVP_Prime_Vehicles-master
```

### 2. Open the Solution

Open `SSVP.sln` in Visual Studio.

### 3. Restore NuGet Packages

In Visual Studio:

1. Right-click the solution in **Solution Explorer**
2. Select **Restore NuGet Packages**

Or from the Package Manager Console:

```powershell
Update-Package -reinstall
```

### 4. Set Up the Database

Create the LocalDB database and tables (see [Database Setup](#database-setup)).

### 5. Update Connection Strings

Update hardcoded database connection strings in the source files to point to your local `.mdf` file path. See [Configuration](#configuration).

### 6. Build and Run

1. Set **SSVP** (`CarApp2`) as the startup project
2. Select **Debug** or **Release** configuration
3. Press **F5** or click **Start**

The application entry point is `MainWindow.xaml` (configured in `App.xaml`).

---

## Database Setup

The application uses **SQL Server LocalDB** with an `.mdf` database file. Connection strings in the codebase reference paths such as:

- `C:\Users\user\Documents\ssvpCarAppDB.mdf`
- `D:\SSVPCar\ssvpCarAppDB.mdf`

You must create the database and align all connection strings to a single consistent path on your machine.

### Required Tables

#### `SignUp`

| Column | Description |
|--------|-------------|
| Username | Unique login username |
| Password | User password |
| (Confirm Password) | Password confirmation field |
| Name | Full name |
| DOB | Date of birth |

#### `Customer`

| Column | Description |
|--------|-------------|
| NIC | National Identity Card / customer ID |
| Fname | First / full name |
| Address | Street address |
| Country | Country of residence |
| Tele | Telephone number |
| Email | Email address |

#### `Vehicle`

| Column | Description |
|--------|-------------|
| Vehicle_Id | Vehicle / order identifier (uses NIC in inserts) |
| Name | Customer or vehicle name |
| Colour | Selected body color |
| QTY | Order quantity |
| Seat_Material | Leather or Nylon |
| DashBoard | Analog or Digital |
| StearingWheel | Circle or Square |
| Rim | Rim size (S, M, L) |
| Door | Standard (S) or Butterfly (B) |
| Generation | Vehicle generation (e.g., 5.1) |
| Engine | Engine type (e.g., Hybrid) |
| FuelType | Fuel type (e.g., Petrol) |
| Price | Calculated total price |
| DelivaryDate | Estimated delivery time (days) |

### Example SQL (create tables)

```sql
CREATE TABLE SignUp (
    Username NVARCHAR(50) PRIMARY KEY,
    Password NVARCHAR(100),
    ConfirmPassword NVARCHAR(100),
    Name NVARCHAR(100),
    DOB NVARCHAR(20)
);

CREATE TABLE Customer (
    NIC NVARCHAR(20) PRIMARY KEY,
    Fname NVARCHAR(100),
    Address NVARCHAR(200),
    Country NVARCHAR(50),
    Tele NVARCHAR(20),
    Email NVARCHAR(100)
);

CREATE TABLE Vehicle (
    Vehicle_Id NVARCHAR(20),
    Name NVARCHAR(100),
    Colour NVARCHAR(20),
    QTY INT,
    Seat_Material NVARCHAR(20),
    DashBoard NVARCHAR(20),
    StearingWheel NVARCHAR(20),
    Rim NVARCHAR(10),
    Door NVARCHAR(10),
    Generation NVARCHAR(10),
    Engine NVARCHAR(20),
    FuelType NVARCHAR(20),
    Price FLOAT,
    DelivaryDate INT
);
```

> **Note:** Table and column names match the application's SQL queries. Adjust types and constraints as needed for your environment.

---

## Configuration

### Database Connection Strings

Multiple files contain hardcoded connection strings. Before running the app, update them to your database path. Key files include:

| File | Purpose |
|------|---------|
| `MVVM/View/loginView.xaml.cs` | Login authentication |
| `MVVM/View/SignUp.xaml.cs` | User registration |
| `CustomerPaymentDetails.xaml.cs` | Customer record insert |
| `SSVPBill.xaml.cs` | Billing and cancellation |
| `PriusDefault.xaml.cs`, `PriusCustomize.xaml.cs` | Prius orders |
| `LexusDefault.xaml.cs`, `LexusCustomize.xaml.cs` | Lexus orders |
| `AquaDefault.xaml.cs`, `AquaCustomize.xaml.cs` | Aqua orders |
| `MVVM/View/ReturnView.xaml.cs` | Return / search |

**Example connection string:**

```
Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=C:\YourPath\ssvpCarAppDB.mdf;Integrated Security=True;Connect Timeout=30;
```

### Email (SMTP) Configuration

Email features (OTP verification, support, returns) use Gmail SMTP. Configure credentials in:

- `SSVPBill.xaml.cs`
- `MVVM/View/supportView.xaml.cs`
- `MVVM/View/ReturnView.xaml.cs`

Replace the sender email and app password with your own SMTP credentials. For Gmail, use an [App Password](https://support.google.com/accounts/answer/185833) with 2FA enabled.

> **Security warning:** Do not commit real credentials to source control. Move SMTP and database settings to `App.config` or user secrets for production use.

---

## Application Workflow

```
MainWindow (Public)
├── Home
├── Login ──────────────► DashBoard (Authenticated)
├── Sign Up                    ├── Dashboard Home
└── About                      ├── Models
                               │     ├── Lexus → lexusMoreData
                               │     ├── Prius → PriusMoreData
                               │     └── Aqua  → AquaMoreData
                               ├── Customize
                               │     ├── Default: LexusDefault / PriusDefault / AquaDefault
                               │     └── Custom:  LexusCustomize / PriusCustomize / AquaCustomize
                               │           └── CustomerPaymentDetails
                               │                 └── SSVPBill
                               ├── Purchase Details
                               ├── Return
                               ├── Support
                               └── Logout → MainWindow
```

### Typical Purchase Flow

1. **Register** via Sign Up (or use an existing account)
2. **Log in** with username and password
3. Navigate to **Customize** and select a model
4. Choose **Default** or **Customize** configuration
5. Enter NIC, name, quantity, and options
6. Click to **calculate price** and save the vehicle record
7. Proceed to **Customer Payment Details**
8. Submit customer information
9. View and confirm details on the **SSVP Bill** screen

---

## Vehicle Models & Customization

### Available Models

| Model | Description |
|-------|-------------|
| **Lexus** | Premium vehicle line |
| **Prius** | Hybrid-focused vehicle line |
| **Aqua** | Compact / efficient vehicle line |

Each model supports both **default** and **full customization** workflows.

### Base Pricing

| Item | Value |
|------|-------|
| Base vehicle price | **6,500,000** |
| Final price | `(Base + option surcharges) × quantity` |

### Customization Options & Surcharges

| Option | Choices | Surcharge |
|--------|---------|-----------|
| **Color** | Black | — |
| | White | +200,000 |
| | Red | +185,000 |
| | Blue / Grey | +150,000 |
| **Seat Material** | Leather | — |
| | Nylon | +100,000 |
| **Dashboard** | Analog | — |
| | Digital | +350,000 |
| **Steering Wheel** | Circle | — |
| | Square | +250,000 |
| **Rim Size** | S | — |
| | M | +100,000 |
| | L | +185,000 |
| **Doors** | Standard (S) | — |
| | Butterfly (B) | +550,000 |

### Delivery Estimates

| Quantity | Delivery (days) |
|----------|-----------------|
| Less than 3 | 3 |
| 3 to 5 | 10 |
| More than 5 | 30 |

### Default Configuration

Default builds use a simplified option set (typically Black or White color and quantity only). Unselected customization fields are stored as `"-"`.

---

## Project Structure

```
SSVP_Prime_Vehicles-master/
├── SSVP.sln                    # Visual Studio solution file
├── LICENSE.txt                 # MIT License
├── README.md                   # This file
└── CarApp2/                    # Main WPF project (assembly: CarApp2)
    ├── App.xaml / App.xaml.cs  # Application entry, DataTemplates, themes
    ├── MainWindow.xaml         # Public landing window
    ├── DashBoard.xaml          # Authenticated dashboard shell
    ├── Models.xaml             # Standalone models window
    ├── CustomerPaymentDetails.xaml
    ├── SSVPBill.xaml           # Billing and OTP verification
    │
    ├── PriusDefault.xaml       # Prius default configuration
    ├── PriusCustomize.xaml     # Prius full customization
    ├── PriusMoreData.xaml      # Prius specifications
    ├── LexusDefault.xaml         # Lexus default configuration
    ├── LexusCustomize.xaml       # Lexus full customization
    ├── lexusMoreData.xaml        # Lexus specifications
    ├── AquaDefault.xaml          # Aqua default configuration
    ├── AquaCustomize.xaml        # Aqua full customization
    ├── AquaMoreData.xaml         # Aqua specifications
    │
    ├── Core/
    │   ├── ObservableObject.cs   # INotifyPropertyChanged base class
    │   └── RelayCommand.cs       # ICommand implementation
    │
    ├── MVVM/
    │   ├── View/                 # XAML views (UserControls)
    │   │   ├── HomeView.xaml
    │   │   ├── loginView.xaml
    │   │   ├── SignUp.xaml
    │   │   ├── aboutView.xaml
    │   │   ├── DashboardHomeView.xaml
    │   │   ├── modelView.xaml
    │   │   ├── customizeView.xaml
    │   │   ├── PurchaseDetailsView.xaml
    │   │   ├── ReturnView.xaml
    │   │   └── supportView.xaml
    │   │
    │   └── ViewModel/            # ViewModels for MVVM binding
    │       ├── mainViewModel.cs
    │       ├── MainDashboardViewModel.cs
    │       ├── loginViewModel.cs
    │       ├── signupviewModel.cs
    │       ├── HomeViewModel.cs
    │       ├── aboutViewModel.cs
    │       ├── DashboardHomeViewModel.cs
    │       ├── modelViewModel.cs
    │       ├── customizeViewModel.cs
    │       ├── PurchaseDetailsViewModel.cs
    │       ├── ReturnViewModel.cs
    │       ├── supportViewModel.cs
    │       └── user.cs
    │
    ├── Theme/                    # Shared UI styles and colors
    │   ├── UIcolors.xaml
    │   ├── ButtonStyles.xaml
    │   ├── ComboBoxStyle.xaml
    │   ├── loginbtnTheme.xaml
    │   └── Normalbuttons.xaml
    │
    ├── Images/                   # Logos, icons, vehicle assets
    ├── Properties/               # Assembly info, resources, settings
    ├── packages.config           # NuGet package references
    ├── App.config                # .NET Framework configuration
    └── SSVP.csproj               # Project file
```

---

## Architecture

The application follows the **MVVM (Model-View-ViewModel)** pattern:

- **Views** — XAML `UserControl` and `Window` files in `MVVM/View/` and the project root
- **ViewModels** — C# classes in `MVVM/ViewModel/` handling navigation and state
- **Core** — Reusable `ObservableObject` and `RelayCommand` helpers

### Navigation

- `MainViewModel` manages public views (Home, Login, About, Sign Up) via `CurrentView` and `RelayCommand` bindings
- `MainDashboardViewModel` manages authenticated dashboard views the same way
- `App.xaml` defines `DataTemplate` mappings that automatically render the correct view for each ViewModel type

### Data Binding

ViewModels inherit from `ObservableObject` for `INotifyPropertyChanged` support. Commands use `RelayCommand` for button and navigation actions.

---

## NuGet Dependencies

| Package | Version | Purpose |
|---------|---------|---------|
| ControlzEx | 4.3.0 | Support library for MahApps.Metro |
| FontAwesome.Sharp | 6.3.0 | Icon fonts in the UI |
| GuerrillaNtp | 3.1.0 | NTP time utilities |
| MahApps.Metro | 2.0.0 | Modern Metro-style WPF controls |
| MaterialDesignColors | 3.0.0 | Material Design color palette |
| MaterialDesignThemes | 5.0.0 | Material Design WPF components |
| MaterialDesignThemes.MahApps | 1.0.0 | Material Design + MahApps integration |
| Microsoft.Xaml.Behaviors.Wpf | 1.1.39 | XAML behaviors |
| Syncfusion.Licensing | 25.2.4 | Syncfusion license management |
| Syncfusion.Shared.WPF | 25.2.4 | Syncfusion WPF shared components |
| WpfAnimatedGif | 2.0.2 | Animated GIF support in WPF |

---

## Known Limitations

- **Hardcoded connection strings** — Database paths differ across files and must be updated manually for each environment
- **Hardcoded SMTP credentials** — Email sender passwords are embedded in source code
- **No centralized configuration** — Settings are not managed through `App.config`
- **Login attempt logic** — Failed login handling may not behave consistently in all edge cases
- **Sign Up validation bug** — `SignUp.xaml.cs` reads `.Name` instead of `.Text` for some fields; verify registration works after setup
- **Mixed database paths** — Login uses a different default path than other modules
- **Syncfusion licensing** — Syncfusion components may require a valid license for commercial use

---

## License

This project is licensed under the **MIT License**. See [LICENSE.txt](LICENSE.txt) for details.

---

## Contact & Support

**SSVP Prime Vehicles**  
Location: Colombo 7  
Contact: +94 76 0193 056

---

*© SSVP Prime Vehicles — Where innovation fuels every journey.*
