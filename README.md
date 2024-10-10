# Workshop: Build Your First Low-Code Process with Camunda

Welcome to the "Build Your First Low-Code Process with Camunda" workshop! In this session, you will learn how to integrate Camunda 8 with out-of-the-box connectors within low-code processes. 
Follow the steps below to set up your environment and prepare for the hands-on tasks.

## Prerequisites

- A running Camunda 8 Cluster.
- A SendGrid account.
- Additional details for other connectors.

## Workshop Setup Instructions

### Step 1: Verify Camunda 8 Cluster

Before we start, make sure you have access to a running Camunda 8 Cluster. 
If not, please sign up at: https://signup.camunda.com/
You can confirm a running cluster by logging into the Camunda 8 Console and ensuring that your cluster is listed and healthy.

### Step 2: Create an API Key in SendGrid

1. Sign in to your SendGrid account.
2. Go to **Settings** > **API Keys**.
3. Click the **Create API Key** button.
4. Choose a name for your API Key, set the desired permissions, and then click **Create & View**.
5. Be sure to copy your new API Key and keep it in a secure place, as you will need it shortly when setting up the Connector Secret.

### Step 3: Create Cluster Secret in Camunda 8

1. Open the Camunda 8 Console and navigate to **Cluster**.
2. Select your active cluster from the list provided.
3. Switch to the **Connector Secrets** section.
4. Choose **Create new secret** to add a new one.
5. Name your secret and enter the SendGrid API Key as the value.
6. Confirm by saving the secret.

### Step 4: Create a new Project on Modeler

1. Go to the Camunda Modeler and select **New project** and name it to your choice.
2. Select Create process application and name it Vacation Process
3. You should now be able to see a Main process named Vacation Process.

### Step 5: Create a Start Form for Vacation Requests

For this step, you will create a start form in the Camunda Modeler that will be used to capture vacation requests. 
This form will collect necessary information from an employee who wishes to apply for leave.

Here’s how to create the form:

1. Select **Create new** -> **Form** 
2. Use the form builder to add the following elements to your form:
    - Section **Input** -> **Date time** : Add two date picker element where the employee can select the vacation start and end date.
      -  Select the placed **Date time** element and edit the Date label to something meaningful like: *Vacation Start Date, Vacation End Date* 
    - **Employee Information**: Include **Text fields** from the Input section for employee-specific data such as:
        - Employee Name (required)
        - Employee Email (required)
        - Department
        - (Optionally) Add **Text area** for additional notes or comments
3. Give all elements meaningful label/text descriptions and define Key-names for input variables.
4. Give the form an ID under **General** -> **ID**: *StartRequestForm*
5. (Optionally): Create a new form and utilize the AI Form Generator
   - Example input: *Create a vacation request process where employee's need to enter their vacation start and end date, email, name and optional some more information in a text field*

### Step 6: Model a Form for Managers to Review Vacation Requests

After setting up the initial vacation request form for employees, the next step is to create a form for managers. This form will allow them to review the vacation requests and make a decision to either accept or reject them.

Here’s the process for creating the manager's review form:

1. Continue using the Camunda Modeler and create a second **Form**.
2. Design the form to include the following elements:
   - **Employee Details**: Display-only fields that show the employee's name, email, department, and the requested vacation dates.
   - **Decision**: Add radio buttons or a dropdown for the manager to select 'Accept' or 'Reject' for the vacation request.
   - **Comments**: Include a text area where the manager can provide additional feedback or reasons for their decision.

### Step 7: Design the BPMN Process Model

Now that we have our forms for submitting and reviewing vacation requests, it's time to design the actual process in a BPMN model. You'll be using Camunda Modeler to create a process diagram that outlines the flow from the start event to the end event, including user tasks, decision logic, and email notifications.

Follow these steps to model your process:

#### Step 7.1: Start Event

1. Open **Vacation Process (Main Process)**
2. Link the **Start Event** to the `StartRequestForm` you created earlier by clicking on the link icon or manually define **Camunda form (linked)** -> **Form id**
3. Give the Process Start Event a meaningful name, like *Vacation Request initiated*.
4. (Optionally) Check Publication to be enabled.

#### Step 7.2: User Task for Managers

1. Connect a **User Task** to the **Start Event**.
2. Link the `ReviewVacationRequestForm` to this task.
3. Give the User Task a meaningful name.

#### Step 7.3: XOR Gateway for Decision

1. Place an **XOR Gateway** right after the manager's review task.
2. Label the decision to *Vacation approved?*

#### Step 7.4: Email Tasks

1. Create two **Sendgrid Connector Tasks** for the email notifications.
2. Link the "Approve" path of the XOR Gateway to the first service task, which will be configured to send an approval email.
   - Specify the sequence flow **Condition** -> **Condition expression** -> *managerDecision = "approved"*
3. Link the "Decline" path of the XOR Gateway to the second service task, which will be configured to send a rejection email.
   - Specify the sequence flow **Condition** -> **Condition expression** -> *managerDecision = "declined"*
4. Label these tasks appropriately (e.g., "Send Approval Email" and "Send Rejection Email").

#### Step 7.5: End Event

1. Finally, draw a flow from each of the email tasks to an **End Event**.
2. Give the Process End Events meaningful names, like *Vacation rejected* or *Vacaton approved*
