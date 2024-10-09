### Steps for Cross-Account Access in AWS

#### Step 1: Create Role in the Provider Account
1. Log in to **AWS Console** with the Provider account (the account you want to grant access to).
2. Navigate to **IAM Service**.
3. In the left navigation pane, click on **Roles**.
4. Click on **Create Role**.

#### Step 2: Set Up Trusted Entity
1. In the **Trusted Entity Type** section, select **AWS Account**.
2. Scroll down to the **An AWS Account** section.
3. Choose **Another AWS Account** and enter the Utilizer account's **Account ID**.

#### Step 3: Add Permissions
1. In the **Add Permissions** section, filter the permissions by **AWS Managed - Job Function**.
2. In the search bar, search for **ReadOnlyAccess**.
3. Select the **ReadOnlyAccess** policy to provide read-only access to the Utilizer account.

#### Step 4: Name, Review, and Create Role
1. Enter a name for the role.
2. Review the role configuration and click on **Create Role**.

#### Step 5: Retrieve Role ARN and Switch Role Link
1. Go to the newly created role and copy the **Role ARN**.
2. Open the **Link to switch roles in console** in a new tab.
3. When the link opens:
   - Enter a **Display Name**.
   - Choose a **Display Color**.
   
#### Step 6: Create IAM User in Utilizer Account
1. Log in to the **Utilizer** account.
2. Go to **IAM Service** and create a new **IAM User**.
3. Name the user, then attach the **AdministratorAccess** policy.
4. Set a custom password for the new user and create the user.

#### Step 7: Switch Role in Utilizer Account
1. Log in with the IAM user that you just created in the Utilizer account.
2. In the top-right corner, click on the **Account Name**.
3. Click on **Switch Role**.
4. Enter the **Provider Account ID** and the **Role Name** (the one created in the Provider account).
5. Enter the **Display Name** and choose a **Display Color** if desired.
6. Click on **Switch Role**.

#### Step 8: Verify Access
1. After switching roles, navigate to any service in the AWS Console.
2. Verify that you have read-only access to the Provider account's services.

#### Done!
You have successfully set up cross-account access between the Provider and Utilizer accounts!