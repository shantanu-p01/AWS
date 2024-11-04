# Steps for Cross-Account Access in AWS

---

#### Step 1: Create a Role in the Provider Account
1. Log in to the **AWS Console** with the Provider account (the account you want to grant access to).
2. Navigate to the **IAM Service**.
3. In the left navigation pane, click on **Roles**.
4. Click on **Create Role**.
   ![IAMService](/crossAccountAccess/img/IAMService.png)

#### Step 2: Set Up Trusted Entity
1. In the **Trusted Entity Type** section, select **AWS Account**.
   ![IAMRoleTrustedEntityType](/crossAccountAccess/img/IAMRoleTrustedEntityType.png)
2. Scroll down to the **An AWS Account** section.
3. Choose **Another AWS Account** and enter the Utilizer account's **Account ID**.
   ![IAMAnotherAWSAccountID12Digits](/crossAccountAccess/img/IAMAnotherAWSAccountID12Digits.png)

#### Step 3: Add Permissions
1. In the **Add Permissions** section, filter the permissions by **AWS Managed - Job Function**.
2. In the search bar, search for **ReadOnlyAccess**.
3. Select the **ReadOnlyAccess** policy to provide read-only access to the Utilizer account.
   ![IAMRolePermissionPolicies](/crossAccountAccess/img/IAMRolePermissionPolicies.png)

#### Step 4: Name, Review, and Create Role
1. Enter a name and description for the role.
   ![IAMRoleNameDescription](/crossAccountAccess/img/IAMRoleNameDescription.png)
2. Review the role configuration and click on **Create Role**.

#### Step 5: Create an IAM User in the Utilizer Account
1. Log in to the **Utilizer** account.
2. Go to the **IAM Service** and create a new **IAM User**.
3. Name the user, check the **Provide user access to the AWS Management Console** field, and then provide a password.
   ![IAMUserUtilizerAccount](/crossAccountAccess/img/IAMUserUtilizerAccount.png)
4. Attach the **AdministratorAccess** policy to the user.
   ![IAMUserUtilizerSetPermission](/crossAccountAccess/img/IAMUserUtilizerSetPermission.png)
5. Click on **Create User**.

#### Step 6: Switch Role in the Utilizer Account
1. Log in with the IAM user you just created in the Utilizer account.
   ![IAMUserUtilizerAccountLogin](/crossAccountAccess/img/IAMUserUtilizerAccountLogin.png)
2. In the top-right corner, click on the **Account Name**.
3. Click on **Switch Role**.
   ![IAMUserUtilizerAccountSwitchRole](/crossAccountAccess/img/IAMUserUtilizerAccountSwitchRole.png)
4. Enter the **Provider Account ID** and the **Role Name** (the one created in the Provider account).
5. Enter a **Display Name** and choose a **Display Color** if desired.
   ![IAMUserRoleSwitching](/crossAccountAccess/img/IAMUserRoleSwitching.png)
6. Click on **Switch Role**.

#### Step 7: Verify Access
1. To verify access, create an S3 bucket in the Provider account.
   - Enter a unique name for the bucket.
   ![ProviderAccountBucketCreationName](/crossAccountAccess/img/ProviderAccountBucketCreationName.png)
   - Click on **Create Bucket**.
   ![ProviderAccountBucketCreated](/crossAccountAccess/img/ProviderAccountBucketCreated.png)
2. Now, to verify if cross-account access works, return to the Utilizer account IAM user and navigate to the S3 service to check the bucket you just created.
   ![UtilizerAccountIAMS3BucketVerify](/crossAccountAccess/img/UtilizerAccountIAMS3BucketVerify.png)

#### Done!