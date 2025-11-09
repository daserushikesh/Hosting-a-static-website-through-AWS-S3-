# Hosting a Static Website on AWS S3 — Step-by-step README

This document describes a clear, professional, step-by-step procedure to host a static website on Amazon S3 using the AWS Management Console. Follow the steps below exactly to create a public S3 bucket, upload your site assets, enable static website hosting, and make the objects accessible over the web.

---

## Prerequisites
- An AWS account with permissions to create S3 buckets and modify bucket policies and ACLs.
- A prepared static website folder containing your `index.html` (and optionally `error.html`) plus all assets (images, CSS, JS) inside a single folder.
- Recommended: keep your AWS credentials and access secure. Use MFA on your account.

---

## Quick overview (what you'll do)
1. Create an S3 bucket with a globally unique name.
2. Upload your website files to the bucket (all files in one place).
3. Enable Static Website Hosting on the bucket and set the index document.
4. Make the bucket and objects public so browsers can access them.
5. Verify your site using the object URL provided by S3.

---

## Detailed step-by-step instructions

### 1. Prepare your website files locally
- Put your site files (HTML, CSS, JS, images) into a single folder. Ensure `index.html` is present at the root of that folder.
- If you plan to use a custom error page, include `error.html` at the root as well.

### 2. Sign in to the AWS Management Console and open S3
1. Navigate to the AWS Management Console and sign in.
2. Search for **S3** in the console search box and open the **S3** service page.

### 3. Create an S3 bucket
1. Click **Create bucket**.
2. Give the bucket a name (the bucket name must be globally unique across all AWS accounts). Make it descriptive but globally unique, for example: `your-company-website-2025-unique`.
3. Choose the AWS Region closest to your target users.
4. Leave other default settings for now (you can fine-tune versioning, encryption, and logging later).
5. Click **Create bucket**.

> Note: bucket names are global and must not contain uppercase letters or spaces. They must follow S3 naming rules.

### 4. Upload your website files to the bucket
1. Click on the bucket name in the S3 console to open it.
2. Click **Upload** → **Add files** (and optionally **Add folder** if you want to preserve folders).
3. Select all files from your website folder (including `index.html`).
4. Click **Upload** and wait for the upload to finish.

### 5. Enable static website hosting
1. Inside your bucket, click the **Properties** tab.
2. Scroll down to **Static website hosting** and click **Edit** (or click the static website hosting card).
3. Choose **Enable** (or "Host a static website").
4. Set the **Index document** to `index.html` (or whatever your site's main filename is).
5. Optionally set an **Error document** (e.g., `error.html`).
6. Click **Save changes**.

After enabling, the console displays an **Endpoint** (object URL) for the website. Keep this URL for testing.

### 6. Make the bucket and objects publicly accessible (permission changes)
> Warning: Making a bucket public exposes its contents to anyone on the internet. Only do this for publicly intended static websites.

#### 6.1 Unblock public access at the bucket-level
1. Click the **Permissions** tab on the bucket page.
2. Under **Block public access (bucket settings)** click **Edit**.
3. Uncheck **Block all public access** (or uncheck the specific block settings preventing public access) and confirm by acknowledging the warning.
4. Click **Save changes**.

#### 6.2 Configure object ownership and ACLs (if needed)
1. Under **Permissions**, find **Object ownership** and click **Edit**.
2. Choose **ACLs enabled** (this allows object-level ACLs to be applied). Check the acknowledgement box and **Save changes**.

> Note: AWS recommends using bucket policies instead of ACLs for public website hosting. The steps below follow the ACL flow you requested. 

#### 6.3 Make uploaded objects public using ACL
1. Return to the **Objects** tab inside your bucket.
2. Select all the files (and folders) you uploaded (click the checkbox in the header to select all).
3. Click **Actions** → **Make public using ACL** (or **Make public** depending on the UI version).
4. Confirm the action.

This sets the objects' ACLs so they can be retrieved anonymously via HTTP GET by browsers.

### 7. Test your website
1. In the **Objects** list, click your `index.html` file.
2. Copy the **Object URL** shown in the object details (or use the static website endpoint displayed in Properties).
3. Paste the URL into a browser. You should see your site load.

Example object URL format:
```
https://<bucket-name>.s3.<region>.amazonaws.com/index.html
```
My portfolio's URL 
```
https://potfolowebsidedase.s3.us-east-1.amazonaws.com/index.html
```

---

## Security considerations
- Do not keep sensitive files in a publicly accessible S3 bucket.
- Prefer using bucket policies and object ownership to manage access rather than ACLs when possible.
- Use HTTPS endpoints (the S3 static website endpoint is HTTP-only; use CloudFront for HTTPS and custom domain support).
- If you need a custom domain and HTTPS, use Amazon CloudFront in front of the S3 bucket and attach an ACM certificate for TLS.

---

## Troubleshooting
- If the page returns 403 Forbidden: check bucket public access settings, object ACLs, and bucket policy.
- If the index page displays raw HTML instead of rendering: ensure the file is named `index.html` and correct MIME types are set for objects (S3 usually sets these automatically based on extension).
- If assets are broken (images/CSS not loading): verify uploaded folder structure and object URLs; confirm objects are public and paths in HTML are correct.
- Use the S3 **Properties** → **Static website hosting** endpoint (not the S3 object URL) for proper index and error document handling.

---

## Cleanup (optional)
- If this was a test, delete the bucket and all objects from the S3 console to avoid unintended public exposure and charges.

---

## Final notes
- You now have a publicly accessible static website hosted on S3. For production use consider CloudFront for HTTPS, caching, and custom domain support.
