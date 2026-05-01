# 🚀 Lab 04: Infrastructure as Code with Terraform (Azure)

**Author:** Hazekiah Kennedy-Wilson  
**Estimated Time:** 45 Minutes  
**Difficulty:** Intermediate  
**Environment:** Azure Cloud Shell  

---

## 🌟 Overview

In this lab, you will deploy Azure infrastructure using **Terraform (Infrastructure as Code)**.

> 💡 **Why this matters:**  
> Instead of manually creating resources in the portal, engineers use code to automate infrastructure.  
> This is how modern cloud environments are built, scaled, and maintained.

---

## 🎯 Objectives

By completing this lab, you will:

- Deploy Azure resources using Terraform  
- Understand the Terraform workflow  
- Modify infrastructure safely  
- Destroy resources to avoid unnecessary costs  

---

## 🧱 Architecture
Resource Group
└── Virtual Network (10.0.0.0/16)
└── Subnet (10.0.1.0/24)
└── Network Security Group (added later)


---

## ⚙️ Prerequisites

- Azure Subscription  
- Access to Azure Cloud Shell  

---

## 🏷️ Naming Convention

Replace `[yourname]` with your name (lowercase, no spaces)

| Resource | Name |
|----------|------|
| Resource Group | rg-lab04-tf-[yourname] |
| VNet | vnet-terraform |
| Subnet | snet-backend |
| NSG | nsg-web |
| Region | East US |

---

## 🧠 Key Concepts

### 🔹 Declarative Infrastructure
Terraform is **declarative**, meaning you define *what you want*, not how to build it.

> 💡 **Example:**  
> You say “create a VNet” — Terraform figures out the steps.

---

### 🔹 Terraform State File

Terraform creates a file called: terraform.tfstate


> 💡 **Why this is important:**  
> - Tracks all resources Terraform manages  
> - Allows safe updates without duplication  
> - Should NEVER be deleted manually  

---

### 🔹 Core Commands

```bash
terraform init     # Initialize project
terraform plan     # Preview changes
terraform apply    # Deploy resources
terraform destroy  # Delete everything

🚀 Phase 1: Setup Environment
mkdir terraform-lab
cd terraform-lab
code main.tf

💡 What’s happening:
You are creating a working directory and opening a Terraform configuration file.

📝 Phase 2: Write Terraform Configuration

Paste this into main.tf:

terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.0"
    }
  }
}

provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "rg" {
  name     = "rg-lab04-tf-YOURNAME"
  location = "East US"
}

resource "azurerm_virtual_network" "vnet" {
  name                = "vnet-terraform"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  address_space       = ["10.0.0.0/16"]
}

resource "azurerm_subnet" "subnet" {
  name                 = "snet-backend"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
}

💡 Explanation:

Defines Azure as the provider
Creates a Resource Group
Creates a Virtual Network
Creates a Subnet inside that network
⚡ Phase 3: Deploy Infrastructure
Step 1: Initialize Terraform
terraform init

💡 What this does:
Downloads required providers and prepares Terraform to run.

Step 2: Preview Changes
terraform plan

💡 Why this matters:
Shows what will be created BEFORE deployment.
Prevents mistakes in real production environments.

Expected output:

Plan: 3 to add, 0 to change, 0 to destroy
Step 3: Apply Configuration
terraform apply

Type:

yes

💡 What’s happening:
Terraform is now creating resources in Azure based on your code.

🔍 Phase 4: Verify Deployment
Go to Azure Portal
Navigate to Resource Groups
Open your resource group

💡 You should see:

Virtual Network
Subnet
🔁 Phase 5: Modify Infrastructure

Open your file again:

code main.tf

Add this block:

resource "azurerm_network_security_group" "nsg" {
  name                = "nsg-web"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
}

💡 Key Insight:
Terraform detects changes and only applies what’s new.
It does NOT recreate existing resources.

Apply Changes
terraform plan

Expected:

Plan: 1 to add
terraform apply

💡 Result:
Only the NSG is created — existing resources remain untouched.

🧹 Phase 6: Clean Up Resources
terraform destroy

Type:

yes

💡 Why this is critical:
Leaving resources running = unexpected cloud charges
Engineers ALWAYS clean up after testing.

🛠️ Troubleshooting
❌ Issue: Resource already exists
Change the resource name
❌ Issue: Terraform command not found
Restart Azure Cloud Shell
❌ Issue: Syntax error
Check brackets and quotes
❌ Issue: No changes detected
Run terraform destroy and retry
💼 Skills Demonstrated
Infrastructure as Code (Terraform)
Azure Networking
Resource Automation
State Management
Secure Cloud Architecture
🏆 Outcome

You successfully:

Deployed Azure infrastructure using code
Used full Terraform workflow (init → plan → apply → destroy)
Modified a live environment safely
Practiced real-world DevOps workflows

