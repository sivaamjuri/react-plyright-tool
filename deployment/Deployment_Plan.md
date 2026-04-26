# Application Deployment Plan & History

This document outlines the complete deployment architecture and the step-by-step configurations that were applied to successfully host both the frontend and backend of the React Plyright Tool.

## 1. Frontend Deployment (Vercel)
* **Hosting Platform:** Vercel
* **Process:** Connected the GitHub repository to Vercel for continuous integration and deployment.
* **Environment Variables:** 
  * `VITE_API_URL` is set to `https://react-plyright-tool-siva.duckdns.org` to ensure secure communication with the backend and prevent Mixed Content (HTTP/HTTPS) errors.

## 2. Backend Deployment (AWS EC2)
* **Hosting Platform:** AWS EC2 Instance
* **Operating System:** Ubuntu Linux
* **Domain Name:** `react-plyright-tool-siva.duckdns.org` (Provided via DuckDNS and mapped to the AWS IP address `13.206.124.210`).

### Server Configuration & Security
* **Reverse Proxy (Nginx):** 
  * Nginx was installed and configured to listen for web traffic on ports 80 and 443.
  * It acts as a reverse proxy, silently passing traffic to the Node.js application running internally on port `3000`.
* **SSL/HTTPS (Certbot):**
  * Installed Let's Encrypt / Certbot to generate a free SSL certificate for the DuckDNS domain.
  * This ensures all data is encrypted and completely resolves the browser Mixed Content blocks.

### Server Optimizations & Fixes
* **Large File Uploads (Fixing 413 Errors):**
  * By default, Nginx blocks file uploads larger than 1MB. Because the application uploads multiple heavy project files at once, this caused `413 Request Entity Too Large` and CORS errors.
  * **Fix Applied:** Created a custom rule (`/etc/nginx/conf.d/uploads.conf`) setting `client_max_body_size 500M;` to allow large batch processing.
* **Automated Maintenance (Cron Jobs):**
  * Added a scheduled server task (Cron Job) to automatically delete old temporary files.
  * **Rule:** `0 3 * * * rm -rf /home/ubuntu/react-plyright-tool/backend/temp/*` (Runs every night at 3:00 AM).

### Backend Environment Variables
* An `.env` file is maintained on the AWS server containing the necessary API keys (such as the Gemini API key used for AI analysis). 

## 3. Deployment Workflow
1. Code changes are pushed to the `main` branch on GitHub.
2. Vercel automatically detects the push and rebuilds the frontend.
3. For backend changes, the developer must SSH into the AWS EC2 instance, pull the latest code from GitHub, and restart the Node.js process.
