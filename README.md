
-----

````
# Frappe 15 Installation Guide (Ubuntu 24.04 LTS)

A step-by-step guide to installing Frappe Framework (Version 15) and ERPNext on a clean Ubuntu 24.04 LTS server.

## Prerequisites
* **OS:** Ubuntu 24.04 LTS
* **User:** A non-root user with sudo privileges.

---

## Installation Steps

### 1. Update and Upgrade Packages
Ensure your system packages are up to date.

```bash
sudo apt-get update -y
sudo apt-get upgrade -y
````

### 2\. Install Git

```bash
sudo apt-get install git -y
```

### 3\. Install Python Dependencies

Ubuntu 24.04 uses Python 3.12 by default.

```bash
sudo apt-get install python3-dev -y
sudo apt-get install python3-setuptools python3-pip -y
sudo apt install python3.12-venv -y
```

### 4\. Install MariaDB

Install the database server and required libraries.

```bash
sudo apt-get install software-properties-common -y
sudo apt install mariadb-server -y
```

#### Secure the MySQL Installation

Run the security script:

```bash
sudo mysql_secure_installation
```

**Follow the prompts:**

  * Enter current password for root: **(Enter your SSH root password)**
  * Switch to unix\_socket authentication: **Y**
  * Change root password: **Y**
  * Remove anonymous users: **Y**
  * Disallow root login remotely: **N**
  * Remove test database: **Y**
  * Reload privilege tables: **Y**

#### Configure MariaDB

Open the configuration file:

```bash
sudo nano /etc/mysql/my.cnf
```

Add the following configuration at the end of the file:

```ini
[mysqld]
character-set-client-handshake = FALSE
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci

[mysql]
default-character-set = utf8mb4
```

Save and exit (Ctrl+O, Enter, Ctrl+X), then restart the service:

```bash
sudo service mysql restart
```

### 5\. Install Redis Server

```bash
sudo apt-get install redis-server -y
```

### 6\. Install Node.js, NPM, and Yarn

Frappe 15 recommends Node Version 18.

```bash
# Install CURL
sudo apt install curl -y

# Install NVM (Node Version Manager)
curl [https://raw.githubusercontent.com/creationix/nvm/master/install.sh](https://raw.githubusercontent.com/creationix/nvm/master/install.sh) | bash

# Load NVM
source ~/.profile

# Install Node 18
nvm install 18

# Install NPM and Yarn
sudo apt-get install npm -y
sudo npm install -g yarn -y
```

### 7\. Install wkhtmltopdf

Required for generating PDF documents.

```bash
sudo apt-get install xvfb libfontconfig wkhtmltopdf -y
```

### 8\. Install Frappe Bench

**Note:** On Ubuntu 24.04, we use the `--break-system-packages` flag due to Python environment restrictions (PEP 668).

```bash
sudo -H pip3 install frappe-bench --break-system-packages
sudo -H pip3 install ansible --break-system-packages
```

### 9\. Initialize Bench and Install Apps

**Initialize the Bench:**
Replace `[folder_name]` with your desired directory name.

```bash
bench init frappe-bench --frappe-branch version-15 [folder_name]
cd frappe-bench
```

**Adjust Permissions:**
Replace `[frappe-user]` with your current system username.

```bash
chmod -R o+rx /home/[frappe-user]
```

**Create a New Site:**
Replace `[site-name]` with your domain (e.g., `site1.local`).

```bash
bench new-site [site-name]
```

**Set the site as default:**

```bash
bench use [site-name]
```

### 10\. Install ERPNext and HRMS

Download the apps (Version 15 branch) and install them on your site.

```bash
# Get the apps
bench get-app payments
bench get-app --branch version-15 erpnext
bench get-app --branch version-15 hrms

# Install apps on your site
bench --site [site-name] install-app erpnext
bench --site [site-name] install-app hrms
```

### 11\. Start the Server

Start the bench to run the application in development mode.

```bash
bench start
```

You can now access your installation at `http://localhost:8000`.

```

---

### Updates for Ubuntu 24.04
* **Flag added:** `--break-system-packages` included in step 8. This is mandatory for Ubuntu 24.04 because it enforces PEP 668, which prevents pip from installing packages globally without this flag (or using a virtual environment).

```
