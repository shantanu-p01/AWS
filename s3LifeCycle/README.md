## S3 LifeCycle Setup

---

1. Go to **S3 Service** and click on **Create Bucket**  
   ![S3 Bucket Creation](/s3LifeCycle/img/s3BucketCreation.png)

2. Enter **Bucket Name** (e.g., s3-ccsa-ty-1113)  
   ![S3 Bucket Name](/s3LifeCycle/img/s3BucketName.png)  
   Then click on **Create Bucket**.

3. Go to the **Bucket** you just created and upload some files.  
   ![Uploading Files in S3 Bucket](/s3LifeCycle/img/s3FilesUploading.png)

4. After uploading the files, go to the **S3 Bucket**. In the **Objects** section, switch to the **Management** section. Click on **Create Lifecycle Rule**.  
   ![Management Section in S3 Bucket](/s3LifeCycle/img/s3BuckerManagementSection.png)

5. Enter the **Lifecycle Rule Name**. Select the **Rule Scope** as **Apply to all objects in S3 Bucket**.  
   ![LifeCycle Rule Config 1](/s3LifeCycle/img/s3LifeCycleRuleConfig1.png)

6. In **Lifecycle Rule Actions**, select the actions as per your requirement. Here, choose **Transition current versions of objects between storage classes**.  
   ![LifeCycle Rule Config 2](/s3LifeCycle/img/s3LifeCycleRuleConfig2.png)

7. In the **Transition Current Versions of Objects between Storage Classes** section, set up the storage classes as needed. For this example, configure the transition as shown in the image below:  
   ![S3 LifeCycle Storage Classes](/s3LifeCycle/img/s3LifeCycleStorageClassesSetup.png)

8. Review all configurations.  
   ![S3 LifeCycle Storage Classes](/s3LifeCycle/img/s3LifeCycleRuleReviewSection.png)

9. After reviewing, click on **Create Rule**. The rule will be applied to the S3 bucket.  
   ![S3 LifeCycle Rule Set Success](/s3LifeCycle/img/s3LifeCycleRuleSetSuccess.png)

As per this setup, the objects in your S3 bucket will automatically transition between storage classes according to the configured lifecycle rules. Done!