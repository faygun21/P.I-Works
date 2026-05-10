# User Management Screen — UI Specification

## Table of Contents

1. [Overview](#overview)
2. [Page Layout](#page-layout)
3. [Toolbar](#toolbar)
4. [User Table (Left Panel)](#user-table-left-panel)
5. [User Form (Right Panel)](#user-form-right-panel)
6. [User Interactions & Behavior Flow](#user-interactions--behavior-flow)
7. [Validation Rules](#validation-rules)
8. [Initial State](#initial-state)
9. [Edge Cases & Error Handling](#edge-cases--error-handling)

---

## Overview

The **User Management Screen** allows administrators to view, create, edit, and manage system users. It is accessible only to users with **Admin** or **SuperAdmin** roles.

The screen is divided into two main sections:
- **Left Panel:** A sortable, filterable data table listing all existing users.
- **Right Panel:** A form for creating a new user or editing an existing one.

---

## Page Layout

```
+-------------------------------------------------------+
|  [+ New User]  [✓ Hide Disabled User]    [Save User]  |  ← Toolbar
+---------------------------+---------------------------+
|                           |                           |
|     User Table            |     New User Form         |
|   (Left Panel)            |   (Right Panel)           |
|                           |                           |
+---------------------------+---------------------------+
```

- The layout is a **two-column split view**.
- The toolbar spans the full width at the top.
- The left panel takes approximately **55%** of the screen width.
- The right panel takes approximately **45%** of the screen width.
- Both panels are visible simultaneously at all times.

---

## Toolbar

Located at the top of the page, spanning full width. Contains three controls:

### `+ New User` Button
| Property | Value |
|----------|-------|
| Position | Top-left |
| Style | Primary outlined button, blue border, blue text |
| Icon | `+` prefix |
| Action | Clears the right panel form and resets it to "New User" mode |

**Behavior:**
- Clicking this button resets all form fields on the right panel to empty.
- The form header changes to **"New User"**.
- Any unsaved changes in the form are discarded without a confirmation prompt.

---

### `Hide Disabled User` Checkbox
| Property | Value |
|----------|-------|
| Position | Top-left, next to "+ New User" button |
| Style | Standard checkbox with label |
| Default State | Unchecked |

**Behavior:**
- When **unchecked (default):** All users (enabled and disabled) are shown in the table.
- When **checked:** Only users where `Enabled = true` are displayed in the table.
- The filter is applied instantly without a page reload.
- The checkbox state persists during the session.

---

### `Save User` Button
| Property | Value |
|----------|-------|
| Position | Top-right |
| Style | Filled button, blue background, white text |
| Action | Saves the current form data (create or update) |

**Behavior:**
- Triggers **form validation** before saving.
- If validation passes:
  - **New User mode:** Creates a new user record and adds it to the table.
  - **Edit mode:** Updates the selected user's record in the table.
- If validation fails: Highlights invalid fields with error messages; does not save.
- On success: The form resets to "New User" mode.

---

## User Table (Left Panel)

A sortable data table displaying all users in the system.

### Columns

| Column | Type | Sortable | Description |
|--------|------|----------|-------------|
| ID | Integer | Yes | Auto-generated unique identifier |
| User Name | String | Yes | The login username of the user |
| Email | String | Yes | The email address of the user |
| Enabled | Boolean | Yes | Indicates if the user account is active (`true` / `false`) |

### Column Headers
- Each column header contains **sort arrows (⇅)** indicating it is sortable.
- Clicking a header sorts the table by that column in **ascending** order.
- Clicking again toggles to **descending** order.
- Only one column can be sorted at a time.
- The active sort column is visually highlighted.

### Row Behavior
| Interaction | Behavior |
|-------------|----------|
| Click a row | Loads that user's data into the right panel form in **Edit mode** |
| Selected row | Highlighted with a light blue background |
| Hover | Subtle background color change to indicate interactivity |

### Sample Data (Initial Load)
| ID | User Name | Email | Enabled |
|----|-----------|-------|---------|
| 1 | AdminUser | admin@piworks.net | true |
| 2 | Test User | testuser@piworks.net | true |

---

## User Form (Right Panel)

Displays either a **New User** form or an **Edit User** form depending on context.

### Form Header
- Displays **"New User"** when creating a new user.
- Displays **"Edit User"** (or the selected username) when editing an existing user.

### Fields

#### Username
| Property | Value |
|----------|-------|
| Type | Text input |
| Required | Yes |
| Placeholder | *(empty)* |
| Max length | 50 characters |
| Validation | Cannot be empty; must be unique; no special characters except `_` and `-` |

#### Display Name
| Property | Value |
|----------|-------|
| Type | Text input |
| Required | No |
| Placeholder | *(empty)* |
| Max length | 100 characters |
| Validation | No special validation |

#### Phone
| Property | Value |
|----------|-------|
| Type | Text input |
| Required | No |
| Placeholder | *(empty)* |
| Format | Numeric, allows `+`, `-`, spaces, parentheses |
| Validation | Optional; if provided, must match a valid phone format |

#### Email
| Property | Value |
|----------|-------|
| Type | Text input |
| Required | Yes |
| Placeholder | *(empty)* |
| Validation | Must be a valid email format (e.g., `user@domain.com`); must be unique |

#### User Roles
| Property | Value |
|----------|-------|
| Type | Multi-select dropdown |
| Required | No |
| Placeholder | `Select user roles...` |
| Options | Guest, Admin, SuperAdmin |
| Behavior | Multiple roles can be selected simultaneously |

**Dropdown Options:**
- **Guest** — Basic read-only access
- **Admin** — Can manage users and settings
- **SuperAdmin** — Full system access

The dropdown opens downward and displays all available roles. Selected roles appear as highlighted items or tags inside the field.

#### Enabled
| Property | Value |
|----------|-------|
| Type | Checkbox |
| Required | No |
| Default | Unchecked (disabled) for new users |
| Behavior | When checked, the user account is active; when unchecked, the account is disabled |

---

## User Interactions & Behavior Flow

### Creating a New User

1. User clicks **`+ New User`** button.
2. Right panel resets — all fields are cleared, header shows **"New User"**.
3. User fills in **Username** and **Email** (required), and optionally other fields.
4. User selects one or more **User Roles** from the dropdown.
5. User checks **Enabled** if the account should be active immediately.
6. User clicks **`Save User`**.
7. Validation runs:
   - If valid → new user is added to the table; form resets.
   - If invalid → error messages appear below invalid fields; form stays open.

---

### Editing an Existing User

1. User clicks on a **row** in the left table.
2. Selected row is highlighted.
3. Right panel populates with that user's data; header updates to **"Edit User"**.
4. User modifies desired fields.
5. User clicks **`Save User`**.
6. Validation runs:
   - If valid → user record is updated in the table; form resets to "New User" mode.
   - If invalid → error messages shown; changes not saved.

---

### Hiding Disabled Users

1. User checks the **`Hide Disabled User`** checkbox.
2. Table immediately filters to show only users where `Enabled = true`.
3. User unchecks the checkbox → all users reappear.

---

## Validation Rules

| Field | Rule | Error Message |
|-------|------|---------------|
| Username | Required | "Username is required." |
| Username | Unique | "This username is already taken." |
| Username | Format | "Username can only contain letters, numbers, `_`, and `-`." |
| Email | Required | "Email is required." |
| Email | Format | "Please enter a valid email address." |
| Email | Unique | "This email is already registered." |
| Phone | Format (if provided) | "Please enter a valid phone number." |

- Validation is triggered on **`Save User`** click.
- Invalid fields are highlighted with a **red border**.
- Error messages appear **below** the respective field.
- The form does not close or reset until all validations pass.

---

## Initial State

When the page loads for the first time:

| Element | Initial State |
|---------|---------------|
| User Table | Populated with all users, sorted by ID ascending |
| Hide Disabled User checkbox | Unchecked |
| Right Panel | Shows empty "New User" form |
| All form fields | Empty / default values |
| Enabled checkbox (form) | Unchecked |
| User Roles dropdown | No selection (`Select user roles...` placeholder shown) |
| Save User button | Visible and clickable |

---

## Edge Cases & Error Handling

| Scenario | Expected Behavior |
|----------|-------------------|
| Saving with no fields filled | Show required field errors for Username and Email |
| Duplicate username | Show "Username already taken" error, do not save |
| Duplicate email | Show "Email already registered" error, do not save |
| Clicking `+ New User` with unsaved form data | Discard form data silently, reset form |
| No users in the system | Table shows an empty state message: "No users found." |
| All users are disabled and `Hide Disabled User` is checked | Table shows empty state: "No active users found." |
| Very long username or email | Truncate display in table cell with ellipsis (`...`); full value shown on hover tooltip |
| Network/save error | Show a top-level error banner: "An error occurred while saving. Please try again." |

---

*Document prepared for the P.I. Works internship application task — User Management Screen UI Specification.*
