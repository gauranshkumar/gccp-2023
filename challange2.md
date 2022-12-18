## Challange Lab 2 : 
#### Perform Foundational Infrastructure Tasks in Google Cloud: Challenge Lab
https://www.cloudskillsboost.google/focuses/10379?parent=catalog


> **Task 1: Create a bucket**

We need to create a storgae bucket with the name mentioned on the lab page
1. Open Google Cloud Shell
2. Run command `gcloud storage buckets create gs://<name-of-bucket-from-lab-page>`
> **Task 2. Create a Pub/Sub topic**

1. Run Command `gcloud pubsub topics create <name-of-pubsub-topic-from-lab-page>`


> **Task 3. Create the thumbnail Cloud Function**

1. Goto Cloud Functions and choose Create a new Function
2. Read the instructions on Lab Page for configuration. It should look something like this.

![Cloud Function Config](https://i.imgur.com/oj4mME2.png)

3. Go to Code Section and follow the guide on the lab page. Change the `index.js` and `package.json` as per the lab page.

It should look something like this:

![Cloud Function Code](https://i.imgur.com/eqjsNJf.png)

4. Deploy the Function and test it by uploading a sample image to your bucket. Go to Cloud Storage bucket and choose the one you created in Task 1 and finally upload a file using the Upload button.

5. Check Progress

> **Task 4. Remove the revious cloud engineer**

1. Use Command `gcloud projects remove-iam-policy-binding <project-id-from-lab-page> --member=user:<email-id-of-user-to-be-removed-from-lab-page> --role=roles/viewer`

2. Wait for sometime and Check the Progress.

