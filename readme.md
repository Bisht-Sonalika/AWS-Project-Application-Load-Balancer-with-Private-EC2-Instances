# AWS Project: Application Load Balancer with Private EC2 Instances

## 📌 About the Project

This project demonstrates how to create a **secure and highly available AWS VPC** that hosts application servers in **private subnets**.
The setup ensures:

* EC2 instances are not directly accessible from the internet.
* Traffic is routed via an **Application Load Balancer (ALB)**.
* Different EC2 instances serve different HTML pages through **target groups**.

---

## 🏗️ Architecture Overview

* **Public Subnets**: Contain Bastion Host, NAT Gateway, and Load Balancer.
* **Private Subnets**: Contain application EC2 instances (EC2a and EC2b).
* **NAT Gateway**: Allows private EC2 instances to reach the internet (for updates, package installations).
* **Application Load Balancer**: Routes incoming traffic to EC2 instances.
* **Target Groups**:

  * `aws-prod-example` → EC2a (serves `index.html`)
  * `aws-prod` → EC2b (serves `second.html`)

Architecture diagram(vpc-example-private-subnets.png)

---

## ⚙️ AWS Services Used

* **VPC** with public & private subnets across 2 Availability Zones
* **EC2** (application servers in private subnets)
* **Bastion Host** (SSH access to private EC2)
* **NAT Gateway**
* **Application Load Balancer (ALB)**
* **Target Groups & Listener Rules**
* **Security Groups** for controlled access

---

## 🚀 Step-by-Step Implementation

### 1. VPC & Networking Setup

* Create a **VPC** with 2 public and 2 private subnets across different AZs.
* Attach an **Internet Gateway**.
* Create a **NAT Gateway** in a public subnet.
---

### 2. Bastion Host

* Launch a Bastion Host EC2 in the **public subnet**.
* Allow **SSH (port 22)** from your IP.
* Use this instance to connect to EC2s in private subnets.
---

### 3. Application EC2 Instances

* Launch **two EC2 instances** in private subnets.
* SSH into them using the Bastion Host.

On **EC2a**:

```bash
python3 -m http.server 8000
```

On **EC2b**:

```bash
python3 -m http.server 8000
```


---

### 4. Target Groups

* Create **two Target Groups**:

  * `aws-prod-example` → maps to EC2a (index.html)
  * `aws-prod` → maps to EC2b (second.html)
* Health check: HTTP → `/` on port `8000`
targetrule1.png
targetrule2.png
---

### 5. Application Load Balancer

* Create an **ALB** in public subnets.
* Add both Target Groups.
* Configure Listener Rules (port 80):

  * `/a*` → forward to `aws-prod-example`
  * `/b*` → forward to `aws-prod`


---

### 6. Testing

* Access via the ALB DNS name:

  * `http://<ALB-DNS>/a` → shows **index.html** (EC2a)
  * `http://<ALB-DNS>/b` → shows **second.html** (EC2b)

firstec2.png
secondec2.png


---

## ✅ Outcome

* Application is securely deployed in **private subnets**.
* Accessible only via **Application Load Balancer**.
* Different EC2s serve different paths (`/a`, `/b`).
* High availability across multiple AZs.


