# Billing Follow-up Management System (FMS)

**Version:** 1.0.0
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

* **Sticky Dashboard Header:** Provides quick access to company branding, current date/time, data synchronization, and user profile.
* **Entrance Animation:** A loading animation with a blurred background is displayed during initial data synchronization.
* **Responsive Design:** Adapts to various screen sizes (desktop, tablet, mobile).
* **Modal-driven Interface:** Uses macOS-style animated popups for adding entries, viewing details, and other actions.
* **Dynamic Data Fetching:** Loads data from backend APIs.
* **Client-Side Data Management:**
    * Search functionality for entries.
    * Sortable table columns.
    * Pagination for large datasets.
* **Step-by-Step Workflow:** Guides users through a 10-stage billing process.
* **Conditional Workflow Logic:** Special handling for "Payment Receive" (S7) stage to allow for complete or incomplete payment scenarios.
* **Status Visualization:**
    * Color-coded status badges in the main table for quick identification.
    * Overdue task highlighting in the "Next Planned On" column.
* **Scorecards:** Display key metrics like total bills, entries at various stages, etc.
* **Action Buttons:**
    * Add new billing entries.
    * View detailed information for each entry.
    * Process entries through different billing stages.
    * Mark entries as "Closed-Lost".
    * Delete entries (with confirmation).
* **Informational Modals:**
    * "Responsibilities" modal outlining doer roles for each task.
    * "How to Use" modal providing a system guide.
* **Timestamp Management:** Records actual and planned timestamps for each stage, calculating delays.
* **Dual API Integration:**
    * Uses Google Apps Script for fetching data (read operations).
    * Uses SheetDB API for creating, updating, and deleting data (write/modify operations).

## 3. System Overview

### Dashboard Header
* **Company Logo & Name:** "Billing FMS" displayed on the left.
* **Dynamic Date & Time:** Shows the current date and time on the right (hidden on small screens).
* **Sync Button (🔄):** Allows manual refresh of data from the backend. Rotates on click.
* **User Profile Button (👤):** Placeholder for future user profile functionality.

### Main Action Buttons
Located below the header, these buttons provide primary system actions:
* **Responsibilities:** Opens a modal detailing who is responsible for each stage.
* **How to Use:** Opens a modal with instructions on using the FMS.
* **Add Entry (S1):** Opens a modal form to add a new billing entry.

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
* Timestamp (S1)
* Billing Name
* Status (with color-coded badges)
* Category
* Assigned By
* Next Planned On (highlights overdue tasks)
* Actions (View Details button)

## 4. Workflow & Usage

### Adding a New Entry (S1)
1.  Click the **"Add Entry (S1)"** button.
2.  The "Add New Billing Entry" modal will appear with an animated popup effect.
3.  Fill in all the required details in the form.
    * The UID is auto-generated.
    * Select at least one "Requirement" (Buy, Lease, Rent).
    * If "GST Bill" is selected, the "GST #" field becomes active and required.
4.  Click **“Add Entry”**. The data will be sent to the SheetDB API.
5.  A success or error message will be displayed. Upon success, the table will refresh.

### Viewing Entry Details
1.  In the "All Recorded Entries" table, click the **Eye icon (👁️)** in the "Actions" column for the desired entry.
2.  The "Entry Details" modal will open with an animated popup, showing comprehensive information about the selected entry, including client details, deal specifics, and the process timeline.

### Processing Through Stages (S2-S10)
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

### Special Case: Payment Receive (S7)
1.  When the "Process Payment (S7)" button is clicked (after the initial confirmation):
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
* A modal will display a table showing each task/stage, the responsible doer/department, and relevant icons.

### Syncing Data
* Click the **Sync icon (🔄)** in the sticky dashboard header.
* The icon will animate, and the system will re-fetch the latest data from the Google Apps Script `VIEW_ENTRIES_URL`.
* The main table and scorecards will update.

## 5. Technical Setup

### Frontend
* HTML, CSS (Tailwind CSS), and vanilla JavaScript.
* Hosted as a static web page.

### Backend API Endpoints
The system is configured to use two separate API endpoints:
* **`VIEW_ENTRIES_URL`**: (Google Apps Script)
    * Used for `GET` requests to fetch all entry data.
    * Expected to be a Google Apps Script Web App URL that responds to `?action=getEntries`.
    * Expected JSON response: `{ "success": true, "data": [...] }` or an array `[...]`.
* **`MODIFY_ENTRIES_URL`**: (SheetDB API)
    * Used for `POST` (add), `PUT` (update), and `DELETE` (delete) operations.
    * Example: `https://sheetdb.io/api/v1/your_sheetdb_api_id`
    * `POST` body: `{ "data": [entryObject] }`
    * `PUT` URL: `.../id/:entryId`, body: `{ "data": updateObject }`
    * `DELETE` URL: `.../id/:entryId`

**Important:** Ensure your Google Sheet used by the Apps Script and SheetDB has the correct column headers as expected by the application logic (e.g., `id`, `uid`, `status`, `isFullPaymentReceived`, all timestamp fields, etc.).

## 6. Troubleshooting

* **"Failed to fetch" / "Network error"**:
    * Verify the `VIEW_ENTRIES_URL` and `MODIFY_ENTRIES_URL` constants in the HTML's JavaScript section are correct.
    * For Google Apps Script:
        * Ensure the script is deployed correctly as a Web App.
        * "Execute as" should be "Me".
        * "Who has access" should be "Anyone" (or "Anyone, even anonymous").
        * Always re-deploy with a "New version" after making script changes.
        * Check the Apps Script "Executions" log for server-side errors.
    * For SheetDB: Ensure the API URL is correct and your SheetDB account/sheet has the correct permissions.
    * Check the browser's Developer Console (Network and Console tabs) for more detailed error messages (e.g., CORS errors).
* **"Invalid data format"**:
    * The `VIEW_ENTRIES_URL` (Apps Script) is not returning data in the expected JSON format (`{success: true, data: [...]}` or a direct array). Check the "Raw response from Apps Script" log in the browser console and the Apps Script execution logs.
* **Data not saving/updating correctly**:
    * Verify that the column headers in your Google Sheet exactly match what the `MODIFY_ENTRIES_URL` (SheetDB) and the JavaScript code expect (case-sensitive). The `id` column is particularly important for updates/deletes with SheetDB.
    * Ensure the `isFullPaymentReceived` column exists in your sheet if using the conditional S7 workflow.

## 7. Future Enhancements (Suggestions)

* **Advanced Table Filtering:** Dropdown filters for Status, Category, Assigned By; Date range filters.
* **User Authentication & Profiles:** Secure the application and allow user-specific views or permissions.
* **Dashboard Charts:** Visual representations of data (e.g., entries per stage, entries over time).
* **Email Notifications:** For overdue tasks or stage completions.
* **Audit Log:** Track changes made to entries.
* **Export to CSV/Excel:** Allow users to export table data.
* **Direct Editing in Table:** For quick modifications without opening the details modal.
