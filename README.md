# gcp-blog-lab
A GCP hands-on lab project integrating Compute Engine, Cloud SQL, and Cloud Storage using Apache and PHP
# ☁️ GCP Blog Hosting Lab — Apache + PHP + Cloud SQL + Cloud Storage

This project was created during my journey to becoming a **Google Cloud Associate Cloud Engineer (ACE)**. It demonstrates how to host a web application in Google Cloud using:

- **Compute Engine** (to host the web server)
- **Cloud SQL** (MySQL database backend)
- **Cloud Storage** (for static image hosting)
- **gcloud CLI + Cloud Shell** (for hands-on deployment)

---

## 🌐 What This Project Does

A virtual machine (`bloghost`) runs a PHP-based blog page that:
- Connects to a Cloud SQL instance to show database integration
- Displays a banner image loaded from Cloud Storage
- All resources are deployed, configured, and tested via CLI and web browser

---

## ⚙️ GCP Services Used

| GCP Service      | Purpose                                       |
|------------------|-----------------------------------------------|
| Compute Engine   | Web server using Apache + PHP                 |
| Cloud SQL        | MySQL backend for simulating blog database    |
| Cloud Storage    | Hosting a public image for the blog           |
| Cloud Shell + gcloud | Deployment and scripting                  |

---

## 🚀 Step-by-Step Setup

### 1️⃣ VM Instance Setup

```bash
gcloud compute instances create bloghost \
  --zone=us-central1-a \
  --machine-type=e2-micro \
  --image-family=debian-12 \
  --image-project=debian-cloud \
  --tags=http-server \
  --metadata startup-script='apt-get update; apt-get install apache2 php php-mysql -y; service apache2 restart' \
  --allow-traffic
2️⃣ Cloud Storage Bucket (for image)
export LOCATION=US
gcloud storage buckets create -l $LOCATION gs://$DEVSHELL_PROJECT_ID

# Download and upload banner image
gcloud storage cp gs://cloud-training/gcpfci/my-excellent-blog.png .
gcloud storage cp my-excellent-blog.png gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png

# Make it public
gsutil acl ch -u allUsers:R gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png
3️⃣ Create Cloud SQL Instance
In Console:

Database Engine: MySQL (Enterprise → Sandbox)

Instance ID: blog-db

User: blogdbuser

Password: (choose one you can remember)

Region/Zone: Same as VM

➡️ Copy the Public IP of this instance.

4️⃣ Allow VM to Access Cloud SQL
In SQL → Connections → Networking:

Add Network:
Name: web front end
IP: YOUR_VM_EXTERNAL_IP/32

➡️ This allows your VM to connect securely to the database.

5️⃣ Create Blog Webpage (on VM)
bash
Copy
Edit
cd /var/www/html
sudo nano index.php
Paste this content:

php
Copy
Edit
<html>
<head><title>Welcome to my excellent blog</title></head>
<body>
<img src='https://storage.googleapis.com/YOUR_BUCKET/my-excellent-blog.png'>
<h1>Welcome to my excellent blog</h1>
<?php
 $dbserver = "CLOUDSQLIP";
 $dbuser = "blogdbuser";
 $dbpassword = "DBPASSWORD";

 try {
  $conn = new PDO("mysql:host=$dbserver;dbname=mysql", $dbuser, $dbpassword);
  $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
  echo "Connected successfully";
 } catch(PDOException $e) {
  echo "Database connection failed:: " . $e->getMessage();
 }
?>
</body></html>
➡️ Replace CLOUDSQLIP with the public IP of your Cloud SQL instance
➡️ Replace DBPASSWORD with your actual password
➡️ Replace the image URL with your bucket’s public link

Restart Apache:

bash
Copy
Edit
sudo service apache2 restart
✅ Final Result
Open your blog in the browser:

arduino
Copy
Edit
http://<YOUR_VM_EXTERNAL_IP>/index.php
You’ll see:

A banner image

A blog title

A success message if the database connection works

📌 Summary
Component	Technology Used
Web Server	Apache + PHP on Compute Engine
Database	Cloud SQL (MySQL)
Image Hosting	Cloud Storage (public bucket)
Deployment	gcloud CLI in Cloud Shell


**AUTHOR
EYO SAVIOUR
Learning Google Cloud — Preparing for the ACE exam**
