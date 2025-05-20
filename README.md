# Billing Follow-up Management System (FMS)

**Version:** 1.0.1 
**Last Updated:** May 20, 2025

## Table of Contents
1.  [Introduction](#introduction)
2.  [Features](#features)
3.  [System Overview](#system-overview)
    * [Dashboard Header](#dashboard-header)
    * [Main Action Buttons](#main-action-buttons)
    * [Scorecards](#scorecards)
    * [Entries Table](#entries-table)
4.  [Workflow & Usage](#workflow--usage)
    * [Adding a New Entry (S1)](#adding-a-new-entry-s1)
    * [Viewing Entry Details](#viewing-entry-details)
    * [Processing Through Stages (S2-S10)](#processing-through-stages-s2-s10)
    * [Special Case: Payment Receive (S7)](#special-case-payment-receive-s7)
    * [Marking as Closed-Lost](#marking-as-closed-lost)
    * [Deleting an Entry](#deleting-an-entry)
    * [Responsibilities](#responsibilities)
    * [Syncing Data](#syncing-data)
5.  [Technical Setup](#technical-setup)
    * [Frontend](#frontend)
    * [Backend API Endpoints](#backend-api-endpoints)
6.  [Troubleshooting](#troubleshooting)
7.  [Future Enhancements (Suggestions)](#future-enhancements-suggestions)

## 1. Introduction

The Billing Follow-up Management System (FMS) is a web-based application designed to streamline and track the entire billing process from initial entry to final payment completion. It provides a clear overview of all billing entries, their current status, and facilitates the progression of each entry through predefined stages. The system aims to improve efficiency, reduce manual tracking errors, and provide a centralized platform for managing billing follow-ups.

## 2. Features

* **Sticky Dashboard Header:** Provides quick access to company branding ("Billing FMS"), current date/time, data synchronization, and a user profile icon.
* **Entrance Animation:** A loading animation with a CSS spinner and "Loading Billing FMS..." text is displayed on a transparent blurred background during initial data synchronization.
* **Responsive Design:** Adapts to various screen sizes (desktop, tablet, mobile).
* **Modal-driven Interface:** Uses macOS-style animated popups (scale-in with bounce) for adding entries, viewing details, and other actions.
* **Dynamic Data Fetching & Modification:**
    * Reads data using a Google Apps Script endpoint.
    * Creates, updates, and deletes data using a SheetDB API endpoint.
* **Client-Side Data Management:**
    * Search functionality for entries.
    * Sortable table columns with visual indicators.
    * Pagination for large datasets.
* **Step-by-Step Workflow:** Guides users through a 10-stage billing process with clear status descriptions (e.g., "Entry Added - Awaiting Customer Confirmation").
* **Conditional Workflow Logic:** Special handling for "Payment Receive (S7)" stage to allow for complete or incomplete payment scenarios, directing the flow accordingly.
* **Status Visualization:**
    * Distinct color-coded status badges in the main table for quick identification of each stage.
    * Overdue task highlighting in the "Next Planned On" column.
* **Scorecards:** Display key metrics like total bills, entries at various stages, etc.
* **Action Buttons:**
    * Add new billing entries.
    * View detailed information for each entry.
    * Process entries through different billing stages with confirmation popups.
    * Mark entries as "Closed-Lost".
    * Delete entries (with confirmation).
* **Informational Modals:**
    * "Responsibilities" modal with icons for each task, outlining doer roles (Email ID column removed).
    * "How to Use" modal providing a system guide.
* **Timestamp Management:** Records actual and planned timestamps for each stage, calculating delays.

## 3. System Overview

### Dashboard Header
* **Company Logo & Name:** "Billing FMS" displayed on the left.
* **Dynamic Date & Time:** Shows the current date and time on the right (hidden on small screens).
* **Sync Button (🔄):** Allows manual refresh of data. Rotates on click.
* **User Profile Button (👤):** Placeholder for future user profile functionality.

### Main Action Buttons
Located below the header, these buttons provide primary system actions:
* **Responsibilities:** Opens a modal detailing who is responsible for each stage, now with icons for better readability.
* **How to Use:** Opens a modal with instructions on using the FMS.
* **Add Entry:** Opens a modal form to add a new billing entry.

### Scorecards
Provides a quick visual summary of key data points:
* Total Bills
* Pending Confirmation
* Awaiting Bill Prep
* Invoice Generated
* Payment Followup
* Payment Completed

### Entries Table
Displays all recorded billing entries with the following sortable columns:
* UID
* Timestamp
* Billing Name
* Status (with distinct color-coded badges for each status)
* Category
* Assigned By
* Next Planned On (highlights overdue tasks in red)
* Actions (View Details button)

## 4. Workflow & Usage

### Adding a New Entry
1.  Click the **"Add Entry"** button.
2.  The "Add New Billing Entry" modal will appear with an animated popup effect.
3.  Fill in all the required details in the form.
    * The UID is auto-generated.
    * Select at least one "Requirement" (Buy, Lease, Rent).
    * If "GST Bill" is selected, the "GST #" field becomes active and required.
4.  Click **“Add Entry”**. The data will be sent to the SheetDB API.
5.  A success or error message will be displayed. Upon success, the table will refresh (data fetched via Google Apps Script).

### Viewing Entry Details
1.  In the "All Recorded Entries" table, click the **Eye icon (👁️)** in the "Actions" column for the desired entry.
2.  The "Entry Details" modal will open with an animated popup, showing comprehensive information about the selected entry, including client details, deal specifics, and the process timeline.

### Processing Through Stages
1.  Open the "Entry Details" for an entry.
2.  Scroll to the "Process Timeline" section.
3.  Only steps up to and including the first pending (incomplete and not skipped) step will be visible.
4.  The current actionable step will have a **“Take Action”** button (e.g., "Confirm (S2)", "Prepare Bill (S3)").
5.  Click the "Take Action" button. A confirmation pop-up will appear.
6.  Click "Confirm" in the pop-up. The system will:
    * Record the actual timestamp for the completed step.
    * Calculate any delay.
    * Update the entry's status.
    * Set the planned timestamp for the next logical step.
    * The update is sent to the SheetDB API.
7.  The "Entry Details" modal and the main table will refresh to reflect the changes.

### Special Case: Payment Receive
1.  When the "Process Payment" button is clicked (after the initial confirmation):
    * A choice pop-up will ask: "Is the payment received completely for this entry?"
    * **"Payment Complete":**
        * Step 7 is marked as complete.
        * Steps 8 (Due Followup) and 9 (Payment Arrangement) are marked as "SKIPPED".
        * The next actionable step becomes Step 10 (Payment Completion).
    * **"Payment Incomplete":**
        * Step 7 is marked as complete.
        * The workflow proceeds normally to Step 8 (Due Followup).
2.  The system updates the entry via the SheetDB API.

### Marking as Closed-Lost
1.  In the "Entry Details" modal, if an entry is not yet "Payment Completed" or already "Closed - Lost", a **"Mark as Closed-Lost" (👎) icon button** will be visible in the top-right controls.
2.  Click this button. A confirmation pop-up will appear.
3.  Confirming the action will update the entry's status to "Closed - Lost" via the SheetDB API. No further "Take Action" buttons will appear for this entry.

### Deleting an Entry
1.  In the "Entry Details" modal, click the **"Delete Entry" (🗑️) icon button** in the top-right controls.
2.  A confirmation pop-up will ask if you are sure.
3.  Confirming the action will send a delete request to the SheetDB API.
4.  Upon success, the entry will be removed, and the table will refresh.

### Responsibilities
* Click the **"Responsibilities"** button in the main actions area.
* A modal will display a table showing each task/stage, an icon representing the task, and the responsible doer/department. The "Email ID" column has been removed.

### Syncing Data
* Click the **Sync icon (🔄)** in the sticky dashboard header.
* The icon will animate, and the system will re-fetch the latest data from the Google Apps Script `VIEW_ENTRIES_URL`.
* The main table and scorecards will update. The separate "Refresh Data" button has been removed.

## 5. Technical Setup

### Frontend
* HTML, CSS (Tailwind CSS), and vanilla JavaScript.
* Hosted as a static web page.

### Backend API Endpoints
The system utilizes a dual API endpoint strategy:
* **`VIEW_ENTRIES_URL`**:
    * **Purpose:** Fetching all entry data (Read operations).
    * **Technology:** Google Apps Script Web App.
    * **URL Example:** `https://script.google.com/macros/s/YOUR_APPS_SCRIPT_ID/exec?action=getEntries`
    * **Expected Response:** JSON format `{ "success": true, "data": [...] }` or a direct array `[...]`.
* **`MODIFY_ENTRIES_URL`**:
    * **Purpose:** Adding, Updating, and Deleting entries (Write/Modify operations).
    * **Technology:** SheetDB API.
    * **URL Example:** `https://sheetdb.io/api/v1/YOUR_SHEETDB_API_ID`
    * **`POST` (Add):** Body `{"data": [entryObject]}`
    * **`PUT` (Update):** URL `.../id/:entryId`, Body `{"data": updateObject}`
    * **`DELETE` (Delete):** URL `.../id/:entryId`

**Important:** Ensure your Google Sheet has the correct column headers as expected by both the Apps Script (for reading) and SheetDB (for writing/modifying). The `id` column is crucial for updates/deletes with SheetDB. The `isFullPaymentReceived` column should exist for the S7 conditional logic.

## 6. Troubleshooting

* **"Failed to fetch" / "Network error"**:
    * Verify the `VIEW_ENTRIES_URL` and `MODIFY_ENTRIES_URL` constants in the HTML's JavaScript section are correct.
    * **For Google Apps Script (`VIEW_ENTRIES_URL`):**
        * Ensure the script is deployed correctly as a Web App.
        * "Execute as" should be "Me".
        * "Who has access" should be **"Anyone"** (or "Anyone, even anonymous").
        * Always re-deploy with a "New version" after making script changes.
        * Check the Apps Script "Executions" log for server-side errors in `doGet`.
    * **For SheetDB (`MODIFY_ENTRIES_URL`):**
        * Ensure the API URL is correct and your SheetDB account/sheet has the correct permissions for POST, PUT, DELETE.
        * Check SheetDB's dashboard for any API call errors.
    * Check the browser's Developer Console (Network and Console tabs) for more detailed error messages (e.g., CORS errors, incorrect request format for SheetDB).
* **"Invalid data format" (usually from `VIEW_ENTRIES_URL`):**
    * The Google Apps Script is not returning data in the expected JSON format. Check the "Raw response from Apps Script" log in the browser console and the Apps Script execution logs.
* **Data not saving/updating correctly (usually with `MODIFY_ENTRIES_URL`):**
    * Verify that the column headers in your Google Sheet exactly match what SheetDB expects (case-sensitive). The `id` column is particularly important for updates/deletes with SheetDB.
    * Ensure the request body for `POST` and `PUT` operations to SheetDB is correctly formatted (`{"data": ...}`).

## 7. Future Enhancements (Suggestions)

* **Advanced Table Filtering:** Dropdown filters for Status, Category, Assigned By; Date range filters.
* **User Authentication & Profiles:** Secure the application and allow user-specific views or permissions.
* **Dashboard Charts:** Visual representations of data (e.g., entries per stage, entries over time).
* **Email Notifications:** For overdue tasks or stage completions.
* **Audit Log:** Track changes made to entries.
* **Export to CSV/Excel:** Allow users to export table data.
* **Direct Editing in Table:** For quick modifications without opening the details modal.
