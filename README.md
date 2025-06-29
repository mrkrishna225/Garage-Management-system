# Garage-Management-system
Garage Management system using salesforce


# Garage Management System (GMS) on Salesforce

## Overview

The Garage Management System (GMS) is a comprehensive Salesforce-based application designed to streamline and enhance the operations of automotive repair facilities. Built on the robust Salesforce Lightning Experience platform, this system digitizes key processes, improves operational efficiency, strengthens customer relationships, and provides valuable business insights through integrated reporting and dashboards.

This project was developed as part of a [mention if it's a "Naan Muthalvan project" or a "University Project" as per your context, e.g., "a university project for Anna University"].

## Features

The GMS offers a rich set of functionalities to manage various aspects of a garage business:

  * **Customer Management:** Centralized profiles for all customer details, including contact information (phone, email), and a complete history of their services.
  * **Appointment & Work Order Management:**
      * Effortless scheduling of service appointments.
      * Capture of vehicle details and type of service requested (Maintenance, Repairs, Replacement Parts).
      * Dynamic calculation of service amounts based on selected services.
      * Detailed tracking of service records and their progression.
      * Automated status updates (e.g., "Started" to "Completed" upon quality check).
  * **Billing & Feedback:**
      * Streamlined billing processes with automated payment recording.
      * Collection of customer feedback and service ratings.
      * Automated email notifications to customers upon payment completion.
  * **Smart Automation:**
      * **Apex Trigger for Dynamic Pricing:** Automatically calculates service costs based on selected service types (e.g., combined services offer bundled pricing).
      * **Record-Triggered Flow for Payment & Communication:** Updates payment status and sends automated "Thank You" emails to customers upon confirmed payment.
  * **Robust Security & Access Control:**
      * Configurable **Profiles** to define user permissions (e.g., Manager, Sales Person).
      * Hierarchical **Roles** to control data visibility based on organizational structure.
      * **Public Groups** for simplified access management for teams.
      * Flexible **Sharing Settings** (Organization-Wide Defaults and Sharing Rules) to ensure data privacy while allowing necessary collaboration (e.g., Managers can view Sales Person's records).
  * **Data Validation & Integrity:**
      * **Validation Rules:** Ensure data accuracy, such as enforcing specific vehicle number plate formats and valid service rating inputs.
      * **Matching and Duplicate Rules:** Prevent the creation of duplicate customer records based on email and phone numbers, maintaining data cleanliness.
  * **Reporting & Dashboards:**
      * Custom Report Types to combine data from multiple related objects (Customer, Appointment, Service, Billing).
      * Comprehensive Reports providing insights into service information, customer satisfaction, and payment trends.
      * Interactive Dashboards for high-level visual summaries of key performance indicators, with subscription options for regular updates.

## Getting Started (For Developers/Admins)

To set up and explore the Garage Management System in your Salesforce environment:

### 1\. Create a Salesforce Developer Account

If you don't have one, sign up for a free developer edition:

  * Go to: [https://developer.salesforce.com/signup](https://developer.salesforce.com/signup)
  * Fill in the required details (use your College Name for "Company").
  * Your username can be in the format: `yourname@yourorganization.com` (this does not need to be a real email address).
  * Click "Sign me up".
  * Check your email inbox (the one you used for signup) for the activation link. It might take 5-10 minutes.
  * Click "Verify Account" and set your password and security question.

### 2\. Deployment (Manual Configuration)

Since this project primarily involves declarative configurations and some Apex code, the easiest way to "deploy" it is to follow the detailed creation steps in a new developer org.

#### A. Create Custom Objects

Navigate to **Setup \> Object Manager \> Create \> Custom Object** and create the following, enabling "Allow reports," "Track Field History," and "Allow search" for all:

  * **Customer Details**
      * Label: `Customer Details`
      * Plural Label: `Customer Details`
      * Record Name Label: `Customer Name`
      * Data Type: `Text`
  * **Appointment**
      * Label: `Appointment`
      * Plural Label: `Appointments`
      * Record Name Label: `Appointment Name`
      * Data Type: `Auto Number`
      * Display Format: `app-{000}`
      * Starting Number: `1`
  * **Service records**
      * Label: `Service records`
      * Plural Label: `Service records`
      * Record Name Label: `Service records Name`
      * Data Type: `Auto Number`
      * Display Format: `ser-{000}`
      * Starting Number: `1`
  * **Billing details and feedback**
      * Label: `Billing details and feedback`
      * Plural Label: `Billing details and feedback`
      * Record Name Label: `Billing details and feedback Name`
      * Data Type: `Auto Number`
      * Display Format: `bill-{000}`
      * Starting Number: `1`

#### B. Create Custom Tabs

Navigate to **Setup \> Quick Find \> Tabs \> New (under Custom Object Tabs)**. For each of your newly created Custom Objects, select the object, choose a tab style, and follow the prompts to save. Ensure "Append tab to users' existing personal customizations" is checked.

#### C. Create a Lightning App

Navigate to **Setup \> Quick Find \> App Manager \> New Lightning App**.

  * **App Details:**
      * App Name: `Garage Management Application`
  * **Navigation Items:** Add:
      * `Customer Details`
      * `Appointments`
      * `Service records`
      * `Billing details and feedback`
      * `Reports`
      * `Dashboards`
  * **User Profiles:** Add `System Administrator` and any other relevant profiles (e.g., `Manager`, `sales person` once created).

#### D. Create Fields for Objects

Go to **Setup \> Object Manager**, select each object, then **Fields & Relationships \> New** to create the following fields:

  * **Customer Details Object:**
      * `Phone number` (Data Type: `Phone`)
      * `Gmail` (Data Type: `Email`)
  * **Appointment Object:**
      * **Lookup Field:** To `Customer Details`
      * `Maintenance service` (Data Type: `Checkbox`, Default: Unchecked)
      * `Repairs` (Data Type: `Checkbox`, Default: Unchecked)
      * `Replacement Parts` (Data Type: `Checkbox`, Default: Unchecked)
      * `Appointment Date` (Data Type: `Date`, Required)
      * `Service Amount` (Data Type: `Currency`, Read-only for all profiles in Field Level Security)
      * `Vehicle number plate` (Data Type: `Text`, Length: `10`, Required, Unique)
  * **Service records Object:**
      * **Lookup Field:** To `Appointment` (Make `Required`, Add Lookup Filter: `Appointment: Appointment Date` **less than** `Appointment: Created Date`, Filter Type: `Required`, Error Message: `Value does not match the criteria.`, Active: `True`)
      * `Quality Check Status` (Data Type: `Checkbox`, Default: Unchecked)
      * `Service Status` (Data Type: `Picklist`, Values: `Started`, `Completed`)
      * `service date` (Data Type: `Formula`, Return Type: `Date`, Formula: `CreatedDate`)
  * **Billing details and feedback Object:**
      * **Lookup Field:** To `Service records`
      * `Payment Paid` (Data Type: `Currency`)
      * `Rating for service` (Data Type: `Text`, Length: `1`, Required)
      * `Payment Status` (Data Type: `Picklist`, Values: `Pending`, `Completed`)

#### E. Create Validation Rules

Go to **Setup \> Object Manager**, select the object, then **Validation Rules \> New**.

  * **Appointment Object - Rule Name: `Vehicle`**
      * Error Condition Formula: `NOT(REGEX( Vehicle_number_plate__c , "[A-Z]{2}[0-9]{2}[A-Z]{2}[0-9]{4}"))`
      * Error Message: `Please enter vaild number`
      * Error Location: `Vehicle number plate`
  * **Billing details and feedback Object - Rule Name: `rating_should_be_less_than_5`**
      * Error Condition Formula: `NOT( REGEX( Rating_for_service__c , "[1-5]{1}"))`
      * Error Message: `rating should be from 1 to 5`
      * Error Location: `Rating for Service`

#### F. Create Matching and Duplicate Rules

Go to **Setup \> Quick Find \> Matching Rules / Duplicate Rules**.

  * **Matching Rule (Customer Details):**
      * Object: `Customer details`
      * Rule Name: `Matching customer details`
      * Matching Criteria: `Gmail` (Exact), `Phone Number` (Exact)
      * Activate the rule after saving.
  * **Duplicate Rule (Customer Details):**
      * Object: `Customer details`
      * Rule Name: `Customer Detail duplicate`
      * Matching Rule: `Matching customer details`
      * Activate the rule after saving.

#### G. Configure Profiles, Roles, Users & Sharing Settings

1.  **Profiles (Setup \> Quick Find \> Profiles):**
      * Clone "Standard User" to create `Manager`. Edit it:
          * Custom App settings: Default for `Garage management`.
          * Object Permissions: Full R/C/E/D/VA/MA for all custom objects.
          * Session timeout: `8 hours of inactivity`.
          * Password policies: `never expires`, Min length `8`.
      * Clone "Salesforce Platform User" to create `sales person`. Edit it:
          * Custom App settings: Default for `Garage management`.
          * Object Permissions: Grant appropriate R/C/E/D for relevant custom objects (e.g., `Read/Create/Edit` on Customer Details, Appointment; adjust as needed).
2.  **Roles (Setup \> Quick Find \> Roles \> Set Up Roles):**
      * Add Role under CEO: `Manager`.
      * Add Role under Manager: `sales person`.
3.  **Users (Setup \> Quick Find \> Users \> New User):**
      * Create `Niklaus Mikaelson`: Role `Manager`, User License `Salesforce`, Profile `Manager`.
      * Create at least 3 users with Role `sales person`, User License `Salesforce Platform`, Profile `sales person`.
4.  **Public Groups (Setup \> Quick Find \> Public Groups \> New):**
      * Label: `sales team`
      * Add `Sales person` Role to Selected Members.
5.  **Sharing Settings (Setup \> Quick Find \> Sharing Settings):**
      * Click `Edit` (Organization-Wide Defaults).
      * For `Service records` Object, set Default Access to `Private`.
      * Create a New `Service records` Sharing Rule:
          * Label: `Sharing setting`
          * Rule Type: `Based on record owner`
          * Select which records to be shared: `Roles` -\> `Sales person`
          * Share with: `Roles` -\> `Manager`
          * Access Level: `Read/Write`

#### H. Create Flow (Automated Email)

Go to **Setup \> Quick Find \> Flow \> New Flow \> Record-triggered flow \> Create**.

  * **Object:** `Billing details and feedback`
  * **Trigger the Flow When:** `A record is Created or Updated`
  * **Optimize the flow for:** `Actions and Related Records`
  * **Add Element (Update Records):**
      * Label: `Amount Update`
      * Filter Condition: `Payment_Status__c` `Equals` `Completed`
      * Set Field Values: `Payment_Paid__c` = `{!$Record.Service_records__r.Appointment__r.Service_Amount__c}`
  * **Create New Resource (Text Template):**
      * Resource Type: `Text Template`
      * API Name: `alert`
      * View: `Plain Text`
      * Body:
        ```
        Dear {!$Record.Service_records__r.Appointment__r.Customer_Name__r.Name},

        I hope this message finds you well. I wanted to take a moment to express my sincere gratitude for your recent payment for the services provided by our garage management team. Your prompt payment is greatly appreciated, and it helps us continue to provide top-notch services to you and all our valued customers.

        Amount paid : {!$Record.Payment_Paid__c}
        Thank you for Coming .
        ```
  * **Add Element (Action):**
      * Action: `Send Email`
      * Label: `Email Alert`
      * Set Input Values:
          * Body: `{!alert}`
          * RecipientAddressList: `{!$Record.Service_records__r.Appointment__r.Customer_Name__r.Gmail__c}`
          * Subject: `Thank You for Your Payment - Garage Management`
  * Save and Activate the Flow.

#### I. Create Apex Class and Trigger (Dynamic Pricing)

1.  **Apex Class (`AmountDistributionHandler`):**
    Go to **Setup \> Developer Console \> File \> New \> Apex Class**.
    Name: `AmountDistributionHandler`
    Paste the following code:
    ```apex
    public class AmountDistributionHandler {
        public static void amountDist(list<Appointment__c> listApp){
            for(Appointment__c app : listApp){
                if(app.Maintenance_service__c == true && app.Repairs__c == true && app.Replacement_Parts__c == true){
                    app.Service_Amount__c = 10000;
                }
                else if(app.Maintenance_service__c == true && app.Repairs__c == true){
                    app.Service_Amount__c = 5000;
                }
                else if(app.Maintenance_service__c == true && app.Replacement_Parts__c == true){
                    app.Service_Amount__c = 8000;
                }
                else if(app.Repairs__c == true && app.Replacement_Parts__c == true){
                    app.Service_Amount__c = 7000;
                }
                else if(app.Maintenance_service__c == true){
                    app.Service_Amount__c = 2000;
                }
                else if(app.Repairs__c == true){
                    app.Service_Amount__c = 3000;
                }
                else if(app.Replacement_Parts__c == true){
                    app.Service_Amount__c = 5000;
                }
            }
        }
    }
    ```
    Save the class.
2.  **Apex Trigger (`AmountDistribution`):**
    Go to **Setup \> Developer Console \> File \> New \> Apex Trigger**.
    Name: `AmountDistribution`
    sObject: `Appointment__c`
    Paste the following code:
    ```apex
    trigger AmountDistribution on Appointment__c (before insert, before update) {
        if(trigger.isbefore && trigger.isinsert || trigger.isupdate){
            AmountDistributionHandler.amountDist(trigger.new);
        }
    }
    ```
    Save the trigger.

#### J. Create Report & Dashboard Folders and Reports

1.  **Report Folder (App Launcher \> Reports \> New Folder):**
      * Label: `Garage Management Folder`
      * Share with `Roles` -\> `Manager` (Access: `View`).
2.  **Report Type (Setup \> Quick Find \> Report Types \> New Custom Report Type):**
      * Primary Object: `Customer details`
      * Report Type Label: `Service information`
      * Store in Category: `Other Reports`
      * Deployment Status: `Deployed`
      * Relate Objects (A to B, then B to C, etc.):
          * `Customer details` to `Appointment`
          * `Appointment` to `Service records`
          * `Service records` to `Billing details and feedback`
3.  **Report (App Launcher \> Reports \> New Report):**
      * Select Category: `Other Reports` -\> `Service information`
      * Add Columns: `Customer name`, `Appointment Date`, `Service Status`, `Payment paid`.
      * Group Rows by: `Rating for Service`, `Payment Status`.
      * Add Chart: `Line Chart`.
      * Save Report: Name `New Service information Report`, save to `Garage Management Folder`.
4.  **Dashboard Folder (App Launcher \> Dashboards \> New Folder):**
      * Label: `Service Rating dashboard`
      * Share with `Roles` -\> `Manager` (Access: `View`).
5.  **Dashboard (App Launcher \> Dashboards \> New Dashboard):**
      * Name: `Service Rating dashboard` (select the folder).
      * Add Component: Select `New Service information Report`.
      * Choose `Line Chart` (and theme).
      * Save and Done.
      * Subscribe: Set frequency to `weekly`, day `Monday`.

## Usage (For End-Users)

Once the app is configured, users can interact with it through the "Garage Management Application".

### 1\. Create Customer Details

  * Go to the "Garage Management" app via the App Launcher.
  * Click on the "Customer Details" tab.
  * Click "New" and fill in the customer's `Customer Name`, `Phone number`, and `Gmail`.
  * Save the record.

### 2\. Create an Appointment

  * Click on the "Appointments" tab.
  * Click "New".
  * Enter the `Customer Name` (select the customer you just created).
  * Provide an `Appointment Date` (ensure it's logically before the anticipated service record creation date).
  * Enter the `Vehicle number plate` (ensure it matches the required format like `XXNNXXNNNN`).
  * Select the desired services by checking the `Maintenance service`, `Repairs`, and `Replacement Parts` checkboxes.
  * Click "Save" to see the `Service Amount` automatically calculated and displayed.

### 3\. Create a Service Record

  * Click on the "Service records" tab.
  * Click "New".
  * Select the relevant `Appointment`. The `Service Status` will default to "Started".
  * Save the record.
  * **Update the Service Record:** Open the newly created Service Record, check the `Quality Check Status` checkbox, and save. Observe how the `Service Status` automatically changes to "Completed".

### 4\. Create Billing Details and Feedback

  * Click on the "Billing details and feedback" tab.
  * Click "New".
  * Select the `Service records` that was completed.
  * Enter the `Rating for service` (a single digit from 1 to 5).
  * Set the `Payment Status` to "Completed".
  * Click "Save".
  * **Observe Automation:** The `Payment Paid` field should automatically populate, and a "Thank You" email will be sent to the customer's Gmail address (if configured correctly).

### 5\. View Reports and Dashboards

  * Click on the "Reports" or "Dashboards" tabs.
  * Explore the "Garage Management Folder" and "Service Rating dashboard" to view operational insights.

-----

## Contributions

Feel free to fork this repository, suggest improvements, or contribute to this project.

-----

