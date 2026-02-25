# User Stories

## 1. User Module

### Login/Registration
**Title:** Kakao Login/Registration
**As a** User,
**I want to** log in or register using my Kakao account,
**So that** I can easily access the platform without creating a separate account.
**Acceptance Criteria:**
- **Given** I am on the login/registration page
- **When** I click the "Kakao Login" button
- **Then** I am redirected to the Kakao authentication page, and upon success, I am logged into the platform.

**Title:** Google Login/Registration
**As a** User,
**I want to** log in or register using my Google account,
**So that** I can easily access the platform without creating a separate account.
**Acceptance Criteria:**
- **Given** I am on the login/registration page
- **When** I click the "Google Login" button
- **Then** I am redirected to the Google authentication page, and upon success, I am logged into the platform.

**Title:** Apple Login/Registration
**As a** User,
**I want to** log in or register using my Apple account,
**So that** I can easily access the platform without creating a separate account.
**Acceptance Criteria:**
- **Given** I am on the login/registration page
- **When** I click the "Apple Login" button
- **Then** I am redirected to the Apple authentication page, and upon success, I am logged into the platform.

**Title:** SNS Login and User Information Integration
**As a** User,
**I want to** have my profile information automatically populated from my SNS account upon registration,
**So that** I don't have to manually enter my details.
**Acceptance Criteria:**
- **Given** I successfully authenticate via an SNS account
- **When** my profile is created
- **Then** my basic information (e.g., name, email) from the SNS provider is saved to my profile.

**Title:** Bypass Email Verification for SNS Registration
**As a** User,
**I want to** skip email verification when signing up via SNS,
**So that** the registration process is faster.
**Acceptance Criteria:**
- **Given** I am registering using an SNS account
- **When** the registration is complete
- **Then** the system receives the verified email from the SNS platform and skips the manual email verification step.
**Notes/Technical Considerations:** The platform must successfully retrieve the email address from the SNS provider.

**Title:** Find User ID
**As a** User,
**I want to** find my registered ID,
**So that** I can log in if I forget my credentials.
**Acceptance Criteria:**
- **Given** I am on the "Find ID" page
- **When** I enter my verified information (e.g., phone number or email)
- **Then** my registered ID is displayed or sent to me.

### Terms and Conditions
**Title:** Terms and Conditions CRUD (Admin/System)
**As an** Administrator,
**I want to** create, read, update, and delete Terms and Conditions,
**So that** the platform's legal agreements are kept up to date.
**Acceptance Criteria:**
- **Given** I am on the Admin Terms Management page
- **When** I perform a create, read, update, or delete action
- **Then** the Terms and Conditions are updated accordingly in the system.

**Title:** View User Terms List
**As a** User,
**I want to** view the list of Terms and Conditions I have agreed to,
**So that** I know my rights and responsibilities.
**Acceptance Criteria:**
- **Given** I am logged into my account settings
- **When** I navigate to the Terms section
- **Then** a list of all current terms and conditions is displayed.

**Title:** Register User Terms Agreement
**As a** User,
**I want to** agree to the Terms and Conditions during registration or when they are updated,
**So that** I can use the platform's services.
**Acceptance Criteria:**
- **Given** I am presented with the terms
- **When** I click "Agree"
- **Then** my agreement is recorded in the system with a timestamp.

### Kakao
**Title:** Kakao Plus Friend Integration
**As a** User,
**I want to** link the platform to the Kakao Plus Friend channel,
**So that** I can receive notifications and engage with customer service easily.
**Acceptance Criteria:**
- **Given** I am on the platform
- **When** I choose to link Kakao Plus Friend
- **Then** I am directed to the Kakao interface to add the channel.

### Main Page
**Title:** Additional Agreement Popup for New Terms
**As a** User,
**I want to** be prompted with a popup if there are new Terms and Conditions I haven't agreed to,
**So that** I can review and agree to them before continuing to use the service.
**Acceptance Criteria:**
- **Given** the Terms and Conditions have been updated since my last login
- **When** I access the Main Page
- **Then** a popup appears requiring my consent for the new terms.
**Notes/Technical Considerations:** Requires verification logic to check the user's agreement status against the latest terms version.

### Vehicle & Affiliate Company Management
**Title:** Unified "Manage Your Vehicles" List View
**As a** User,
**I want to** view a single consolidated list of all my registered vehicles,
**So that** I can easily see each vehicle, its associated affiliate company (if any), and its active plans.

**Acceptance Criteria:**
- **Given** I navigate to the "Manage Your Vehicles" menu
- **When** the page loads
- **Then** a list is displayed showing each vehicle's details.
- **And** if a vehicle belongs to an affiliate company, the company name is displayed alongside it.
- **And** if a vehicle does not belong to a company, the company field is left empty.
- **And** any active plans tied to the vehicle are clearly visible.

**Title:** Register New Vehicle with Optional Affiliate Company
**As a** User,
**I want to** register a new vehicle and optionally link it to an affiliate company,
**So that** my vehicle is tracked and the company association is recorded simultaneously.

**Acceptance Criteria:**
- **Given** I am on the "Add Vehicle" screen within the "Manage Your Vehicles" menu
- **When** I enter the vehicle details
- **And** I optionally input or select an affiliate company name
- **Then** the vehicle is successfully saved to my profile.
- **And** if a company was provided, it is linked to this specific vehicle.

**Notes/Technical Considerations:**
This establishes the business rule: Affiliate companies only exist as an attribute or relation tied directly to a vehicle. An affiliate company cannot exist in the system without at least one associated vehicle.

**Title:** Edit/Delete Vehicle & Affiliate Company Link
**As a** User,
**I want to** modify a vehicle's details, change/remove its affiliate company, or delete the vehicle entirely,
**So that** my records remain accurate over time.

**Acceptance Criteria:**
- **Given** I select a specific vehicle from the "Manage Your Vehicles" list
- **When** I update the vehicle information or change the affiliate company
- **Then** the updates are saved immediately.
- **When** I choose to delete the vehicle
- **Then** the vehicle is removed from my list, and if it was the only vehicle linked to a specific affiliate company, that company association is also removed/archived.

**Title:** Endorsement Email Notification upon Vehicle Change
**As a** System,
**I want to** automatically send an endorsement email when a user who is subscribed to a plan changes their vehicle,
**So that** the insurance policy reflects the updated vehicle information.
**Acceptance Criteria:**
- **Given** a user is currently enrolled in a plan
- **When** the user updates or changes their vehicle information
- **Then** an endorsement email is generated and sent to the relevant parties (user/insurer).

---

## 2. Plan Module

### Pricing Management
**Title:** Add Insurance and Membership Cost Fields to Plan Management
**As an** Administrator,
**I want to** have separate fields for Insurance Cost and Membership Cost when managing plans,
**So that** pricing components are clearly divided.
**Acceptance Criteria:**
- **Given** I am creating or editing a plan
- **When** I input pricing details
- **Then** I must specify both Insurance Cost and Membership Cost separately.
**Notes/Technical Considerations:** Requires splitting the existing price column in the database.

**Title:** Record Insurance and Membership Costs on Plan CRUD Events
**As a** System,
**I want to** capture and log the separated Insurance and Membership costs whenever a plan is created, updated, or deleted,
**So that** there is an accurate historical record of pricing changes.
**Acceptance Criteria:**
- **Given** a plan is modified
- **When** the transaction is logged
- **Then** the discrete Insurance and Membership costs are included in the event log.

### Insurance Company Management
**Title:** Insurance Company CRUD
**As an** Administrator,
**I want to** manage (create, read, update, delete) insurance company profiles,
**So that** we have an accurate registry of available insurers.
**Acceptance Criteria:**
- **Given** I am on the Insurance Company management page
- **When** I submit an action
- **Then** the system updates the insurance company database.

**Title:** Map Insurance Companies to Plans
**As an** Administrator,
**I want to** link insurance companies to specific plans,
**So that** users know which insurer provides which plan.
**Acceptance Criteria:**
- **Given** I am editing an insurance company or a plan
- **When** I create a mapping
- **Then** the relationship is successfully saved and displayed.

### Plan Management
**Title:** Batch Mapping of Users and Vehicles to Plans
**As an** Administrator,
**I want to** map multiple users and their vehicles to a plan in a single batch action,
**So that** I can efficiently manage bulk enrollments.
**Acceptance Criteria:**
- **Given** I am in the batch management interface
- **When** I upload or select multiple users/vehicles and assign a plan
- **Then** all selected entities are mapped to the plan simultaneously.

**Title:** Comprehensive Entity Mapping Registration
**As an** Administrator,
**I want to** map Insurance Companies, Plans, Certificates, Vendors, Individual/Corporate Users, and Vehicles together,
**So that** the entire insurance value chain is correctly linked in the database.
**Acceptance Criteria:**
- **Given** I am setting up a complex enrollment
- **When** I link the required entities
- **Then** the system establishes and saves all necessary relationships.

### Page Additions
**Title:** Delivery Safe Plan Description Page
**As a** User,
**I want to** view a detailed description page for the "Delivery Safe Plan",
**So that** I understand the benefits and coverage.
**Acceptance Criteria:**
- **Given** I navigate to the plans section
- **When** I click on "Delivery Safe Plan"
- **Then** I am presented with a dedicated detail page.

### Registration (Pre-requisites)
**Title:** Add Footer to Main Page
**As a** User,
**I want to** see a footer on the main page,
**So that** I can access important links like terms, privacy policy, and company info.
**Acceptance Criteria:**
- **Given** I am on the main page
- **When** I scroll to the bottom
- **Then** the footer is visible.

**Title:** Plan Details Page with Policies
**As a** User,
**I want to** see mandatory pricing and refund policies on the Plan Details page,
**So that** I am fully informed before making a purchase.
**Acceptance Criteria:**
- **Given** I am viewing a plan detail page
- **When** I review the information
- **Then** section(s) explicitly detailing the pricing policy and refund policy are present.

**Title:** Payment Method Selection Page
**As a** User,
**I want to** choose my preferred payment method on a dedicated page,
**So that** I can seamlessly complete my transaction.
**Acceptance Criteria:**
- **Given** I am checking out or enrolling in a plan
- **When** I proceed to payment
- **Then** I am directed to a page where I can select my payment instrument.

---

## 3. Payment Module

### Payment Processing
**Title:** Credit Card Payment
**As a** User,
**I want to** pay for my plan using a credit card,
**So that** my transaction is processed instantly.
**Acceptance Criteria:**
- **Given** I am on the payment page
- **When** I input my credit card details and confirm
- **Then** the payment is processed via the PG and my plan is activated.

**Title:** Bank Transfer Payment
**As a** User,
**I want to** pay for my plan via direct bank transfer,
**So that** I have an alternative to credit card payments.
**Acceptance Criteria:**
- **Given** I select bank transfer as my payment method
- **When** I complete the transfer process
- **Then** the system verifies the deposit and activates my plan.

**Title:** Virtual Account Payment
**As a** User,
**I want to** be issued a temporary virtual account number to make my payment,
**So that** I can securely transfer funds specific to my order.
**Acceptance Criteria:**
- **Given** I select Virtual Account payment
- **When** I confirm the order
- **Then** a unique account number is generated and provided to me.
**Notes/Technical Considerations:** Need to confirm with stakeholders if this feature will be implemented.

**Title:** Payment Information CRUD
**As a** System,
**I want to** create, read, update, and delete payment records,
**So that** transaction histories are accurately maintained.
**Acceptance Criteria:**
- **Given** a payment transaction occurs or is modified
- **When** the system processes it
- **Then** the database correctly reflects the payment state.

**Title:** Collect PG Payment Events
**As a** System,
**I want to** collect and log event data (success, failure, pending) from the Payment Gateway,
**So that** we can monitor payment health and troubleshoot issues.
**Acceptance Criteria:**
- **Given** a payment is attempted via the PG
- **When** the PG returns a response webhook/callback
- **Then** the event details are securely logged in our system.

### Refund Processing
**Title:** Standard Refund
**As a** User,
**I want to** request and receive a refund for an eligible cancellation,
**So that** my money is returned to my original payment method.
**Acceptance Criteria:**
- **Given** my refund request is approved
- **When** the admin executes the refund
- **Then** the funds are returned via the PG.

**Title:** Virtual Account Refund
**As a** User,
**I want to** receive my refund via a bank account if I paid using a Virtual Account,
**So that** the funds are returned securely since virtual accounts are temporary.
**Acceptance Criteria:**
- **Given** a refund is processed for a Virtual Account order
- **When** the refund is executed
- **Then** the funds are routed to the bank account I specify.
**Notes/Technical Considerations:** Need to confirm if Virtual Accounts are adopted.

**Title:** Refund History CRUD
**As an** Administrator,
**I want to** manage and view the history of all refunds,
**So that** financial records are complete and auditable.
**Acceptance Criteria:**
- **Given** I am in the Admin Refund section
- **When** I view or modify refund data
- **Then** the system accurately displays the refund status and history.

**Title:** Collect PG Refund Events
**As a** System,
**I want to** log all refund events from the Payment Gateway,
**So that** we have real-time tracking of refund statuses.
**Acceptance Criteria:**
- **Given** a refund is processed via the PG
- **When** the PG returns the status
- **Then** the event is logged.

### Settlement
**Title:** PG Payment Reconciliation
**As an** Administrator,
**I want to** verify the system's payment records against the actual payout data from the PG,
**So that** I can ensure financial accuracy.
**Acceptance Criteria:**
- **Given** I am performing financial reconciliation
- **When** I upload or connect the PG settlement data
- **Then** the system highlights any discrepancies.

**Title:** Collect PG Settlement History
**As a** System,
**I want to** automatically fetch and store settlement reports from the PG,
**So that** financial reporting is automated.
**Acceptance Criteria:**
- **Given** the PG generates a daily/weekly settlement report
- **When** the schedule triggers
- **Then** the data is ingested and saved to our database.

---

## 4. Claim Module

### Simplified Accident Registration
**Title:** Real-time Location Collection via Kakao Map
**As a** User,
**I want to** use Kakao Map to automatically capture and submit my exact location during an accident report,
**So that** the registration process is fast and accurate.
**Acceptance Criteria:**
- **Given** I am filing an accident report
- **When** I use the location feature
- **Then** Kakao Map opens, retrieves my GPS coordinates, and attaches them to the report.

**Title:** Block Accident Reporting for Uninsured Users
**As a** System,
**I want to** verify a user's plan enrollment status before allowing them to file a claim,
**So that** invalid claims are blocked immediately.
**Acceptance Criteria:**
- **Given** a user attempts to file an accident report
- **When** the system checks their active plans
- **Then** if no valid plan exists, the submission is blocked and an error message is shown.

### Reflecting Accident Registration Results
**Title:** Upload Accident Result Excel File (Admin)
**As an** Administrator,
**I want to** upload an Excel file containing the outcomes of accident claims,
**So that** bulk updates can be processed efficiently.
**Acceptance Criteria:**
- **Given** I have a formatted Excel file of claim results
- **When** I upload the file in the Admin portal
- **Then** the system parses the file successfully.

**Title:** Update Accident Registration Results
**As a** System,
**I want to** update the compensation amount and status of accident reports based on the uploaded Excel file,
**So that** users have the latest information on their claims.
**Acceptance Criteria:**
- **Given** an Excel file is uploaded
- **When** the data is processed
- **Then** the respective claim records are updated with the corresponding compensation amounts and new statuses.

---

## 5. Admin Module

### User Management
**Title:** Excel Download of User Information
**As an** Administrator,
**I want to** download user data as an Excel file,
**So that** I can perform external analysis or reporting.
**Acceptance Criteria:**
- **Given** I am viewing the user list
- **When** I click "Download Excel"
- **Then** an Excel file containing the filtered user information is generated and downloaded.

**Title:** Manage User Vehicles and Affiliate Companies (Admin)
**As an** Administrator,
**I want to** view, add, edit, or delete vehicles and their associated affiliate companies for a specific user,
**So that** I can provide customer support and ensure data accuracy.
**Acceptance Criteria:**
- **Given** I am viewing a specific user's detailed profile in the Admin portal
- **When** I navigate to the user's "Vehicles" section
- **Then** I see a consolidated list of their vehicles, affiliate companies, and active plans.
- **When** I choose to edit a vehicle or its affiliate company name
- **Then** the updates are saved and reflected in the user's profile.
- **When** I add or delete a vehicle for the user
- **Then** the system enforces the business rule that the affiliate company association is tied directly to the vehicle record.

*(Note: Admin CRUD functions for Terms, User Terms Agreement History, and User Vehicle/Affiliate Company Data are integrated within the detailed admin management interfaces.)*

### Alimtalk (Notification) Management
**Title:** Aligo Token Management (Generate/Save/Modify)
**As a** System Administrator,
**I want to** manage the Aligo API tokens (generate, save, modify),
**So that** the platform can authenticate and send Alimtalk messages.
**Acceptance Criteria:**
- **Given** I access the Aligo configuration page
- **When** I enter or refresh the token
- **Then** the new token is securely saved and used for subsequent API calls.

**Title:** Alimtalk Type CRUD
**As an** Administrator,
**I want to** manage different categories or types of Alimtalk messages,
**So that** messages are properly classified.
**Acceptance Criteria:**
- **Given** I am on the Alimtalk Type management page
- **When** I add or edit a type
- **Then** the change is saved to the database.

**Title:** Alimtalk Template CRUD
**As an** Administrator,
**I want to** create, read, update, and delete Alimtalk message templates,
**So that** standard messages can be reused.
**Acceptance Criteria:**
- **Given** I am managing message templates
- **When** I create or alter a template
- **Then** the template is saved and available for use.

**Title:** Collect Alimtalk Template Approval Events
**As a** System,
**I want to** track whether Alimtalk templates have been approved by the provider (e.g., Kakao),
**So that** unapproved templates are not used.
**Acceptance Criteria:**
- **Given** a template is submitted for approval
- **When** the approval status changes
- **Then** the system logs the event and updates the template's usable status.

**Title:** Alimtalk Scheduling CRUD
**As an** Administrator,
**I want to** schedule Alimtalk messages to be sent at a future date/time,
**So that** communications occur at the optimal moment.
**Acceptance Criteria:**
- **Given** I am preparing a message
- **When** I set a future date and time and save
- **Then** the message is queued in the scheduling system.

**Title:** Alimtalk Scheduler Batch Processing
**As a** System,
**I want to** run a batch process to evaluate and send scheduled Alimtalk messages,
**So that** queued messages are dispatched on time.
**Acceptance Criteria:**
- **Given** messages are queued with specific send times
- **When** the batch job runs
- **Then** all messages whose send time has arrived are dispatched.

**Title:** Send Alimtalk Immediately
**As an** Administrator,
**I want to** trigger an Alimtalk message manually right away,
**So that** urgent communications can be dispatched.
**Acceptance Criteria:**
- **Given** I am viewing a user or a template
- **When** I click "Send Now"
- **Then** the message is dispatched immediately.

**Title:** Alimtalk Sending History CRUD
**As an** Administrator,
**I want to** view and manage the log of all sent Alimtalk messages,
**So that** I can verify delivery and troubleshoot issues.
**Acceptance Criteria:**
- **Given** I am in the Alimtalk history section
- **When** I search or filter records
- **Then** I see the accurate status (sent, failed, delivered) of past messages.

### Plan Management (Admin)
**Title:** Upload User Plan Data via Excel
**As an** Administrator,
**I want to** upload a batch of user plan enrollments using an Excel file,
**So that** I can onboard multiple participants quickly.
**Acceptance Criteria:**
- **Given** I have a formatted Excel file of user plans
- **When** I upload it in the Admin portal
- **Then** the system creates mapping records for the data provided.

**Title:** Upload User Policy (Certificate) PDF
**As an** Administrator,
**I want to** upload PDF files of insurance policies (certificates) and attach them to specific users,
**So that** users can access their policy documents.
**Acceptance Criteria:**
- **Given** I select a user plan mapping
- **When** I upload a PDF
- **Then** the PDF is securely stored and linked to the user's plan.

**Title:** Download Entity Mapping History as Excel
**As an** Administrator,
**I want to** download the history of all user, plan, and vehicle mappings into an Excel file,
**So that** I have a backup and can perform offline analysis.
**Acceptance Criteria:**
- **Given** I am viewing the mapping records
- **When** I click download
- **Then** the Excel file is generated.

**Title:** Record Corporate Deposit Details
**As an** Administrator,
**I want to** input and save deposit records for corporate clients,
**So that** B2B payments are tracked accurately.
**Acceptance Criteria:**
- **Given** a corporate client has made a payment
- **When** I enter the deposit details
- **Then** the financial record is saved and linked to the corporate account.

### Accident Registration (Admin)
**Title:** Modify Accident Registration Details
**As an** Administrator,
**I want to** edit the details of an existing accident report,
**So that** inaccuracies can be corrected.
**Acceptance Criteria:**
- **Given** I am viewing a specific accident report
- **When** I modify fields and save
- **Then** the changes are saved to the database.

**Title:** Download Accident Registration Data as Excel
**As an** Administrator,
**I want to** download all accident registration and claim data,
**So that** the data can be analyzed by insurance partners.
**Acceptance Criteria:**
- **Given** I am viewing the accident reports list
- **When** I hit download
- **Then** an Excel file containing the data is provided.

### Settlement (Admin)
**Title:** Settlement Reporting
**As an** Administrator,
**I want to** view aggregated reports of financial settlements,
**So that** I can understand revenue and payouts.
**Acceptance Criteria:**
- **Given** I navigate to the Settlement reporting dashboard
- **When** the page loads
- **Then** accurate financial totals and summaries are displayed.

**Title:** Download Settlement Data as Excel
**As an** Administrator,
**I want to** export the settlement reports,
**So that** they can be shared with the finance department.
**Acceptance Criteria:**
- **Given** I am viewing a settlement report
- **When** I trigger the export
- **Then** the data is compiled into an Excel format.

### Administrator Settings
**Title:** Collect Admin Action Logs
**As a** System,
**I want to** log every action taken by an Administrator,
**So that** there is a complete audit trail of system changes.
**Acceptance Criteria:**
- **Given** an Admin performs a CRUD operation or significant action
- **When** the action occurs
- **Then** the system logs the user ID, timestamp, and action details.

**Title:** Role-Based Admin Access Control
**As a** System,
**I want to** enforce distinct permissions for different admin roles (Sales, Operations, Management Support) regarding read/write capabilities,
**So that** sensitive data and functions are restricted appropriately.
**Acceptance Criteria:**
- **Given** an admin logs in
- **When** they attempt to access sections of the application
- **Then** the system restricts access and write permissions based on their assigned role.

---

## 6. Mobile Sensing Module

### Data Collection
**Title:** Store GPS Data Locally
**As a** Mobile App,
**I want to** cache GPS tracking data locally on the device,
**So that** data is not lost if the network connection drops.
**Acceptance Criteria:**
- **Given** the app is tracking location
- **When** a network connection is unavailable
- **Then** the coordinates are securely stored in the local SQLite/storage.

**Title:** Transmit GPS Data to Server
**As a** Mobile App,
**I want to** send stored GPS data to the server,
**So that** the platform has real-time or near real-time tracking information.
**Acceptance Criteria:**
- **Given** the device has network connectivity
- **When** local GPS data exists
- **Then** the app uploads the data securely to the backend and clears the local cache upon success.

---

## 7. RMS Integration Module

### RMS Integration
**Title:** Share User Information with RMS
**As a** System,
**I want to** securely transmit relevant user profile data to the RMS (Risk Management System),
**So that** systems remain synchronized.
**Acceptance Criteria:**
- **Given** a user signs up or updates their profile
- **When** the data is saved
- **Then** an API call is made to the RMS to reflect the changes.

**Title:** Landing on RMS Enrollment Page
**As a** User,
**I want to** be seamlessly redirected to the RMS enrollment landing page when required,
**So that** I can complete external registration flows easily.
**Acceptance Criteria:**
- **Given** I attempt an action requiring RMS enrollment
- **When** I click proceed
- **Then** I am navigated to the correct external RMS page.

**Title:** Landing on RMS Accident Registration Page
**As a** User,
**I want to** be redirected to the RMS dedicated accident report page,
**So that** I can file claims within the RMS ecosystem.
**Acceptance Criteria:**
- **Given** I select the RMS claim option
- **When** I click the link
- **Then** I am routed to the RMS accident registration page.

---

## 8. Community Module

### Bulletin Board
**Title:** Community Post CRUD
**As a** User,
**I want to** create, read, update, and delete my posts on the community board,
**So that** I can interact and share information with other users.
**Acceptance Criteria:**
- **Given** I am logged into the community section
- **When** I manage my posts
- **Then** the system successfully saves or removes my content as requested.
