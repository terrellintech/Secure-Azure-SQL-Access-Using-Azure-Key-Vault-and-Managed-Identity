# Secure Azure SQL Access Using Azure Key Vault and Managed Identity

### Watch me demonstrate this lab here: https://www.loom.com/share/9475450e06cc4754be6ab3abb90bd479

## Overview

This lab demonstrates how to securely deploy and access an **Azure SQL Database** by storing credentials in **Azure Key Vault** and granting access to a **virtual machine using a system-assigned managed identity**.

The goal is to eliminate hard-coded credentials, improve security, and follow Azure best practices for secrets management.

---

## Architecture Summary

The environment consists of the following components:

- **Azure SQL Database**
  - Uses SQL authentication
  - Public endpoint enabled for lab access
- **Azure Key Vault**
  - Stores the SQL admin password as a secret
  - Uses access policies for secret permissions
- **Web Server Virtual Machine**
  - Uses a system-assigned managed identity
  - Retrieves the SQL password from Key Vault
  - Connects to Azure SQL using `sqlcmd`

---

## Resource Organization

All resources were deployed into a dedicated resource group:

- **Resource Group:** `RG-Lab-003`

This ensures clear organization and simplified lifecycle management.

---

## Azure SQL Database Deployment

The Azure SQL Database was created with the following configuration:

- **Database Name:** `db-003`
- **SQL Server Name:** `sql-server-lab003`
- **Authentication:** SQL authentication
- **Admin Username:** `sqladmin`
- **Compute Tier:** Basic (for lab purposes)
- **Networking:**
  - Public endpoint enabled
  - Azure services allowed to access the server
  - Client IP allowed for administrative access

A strong, randomly generated password was created for the SQL admin account.

---

## Azure Key Vault Configuration

An Azure Key Vault was deployed to securely store the SQL admin password.

### Key Vault Details
- **Name:** `AZKey-Lab003`
- **Region:** Central US
- **Access Model:** Vault access policy
- **Networking:** Public access enabled (no private endpoint for this lab)

### Secret Configuration
- **Secret Name:** `SqlAdminPassword`
- **Secret Value:** SQL admin password
- **State:** Enabled

---

## Virtual Machine Identity Configuration

The web server virtual machine was configured with a **system-assigned managed identity**.

This identity allows the VM to authenticate to Azure services without storing credentials.

---

## Key Vault Access Policy

An access policy was created in the Key Vault to allow the VM to retrieve secrets:

- **Principal:** Web Server VM (managed identity)
- **Secret Permissions:**
  - Get
  - List

This enables the VM to securely read the SQL admin password from Key Vault.

---

## Retrieving the Secret from the VM

From the web server VM, the Azure CLI was used to retrieve the SQL password:

```bash
az keyvault secret show \
  --vault-name AZKey-Lab003 \
  --name SqlAdminPassword \
  --query value \
  --output tsv
```
The secret was successfully retrieved using the VM’s managed identity.

## Connecting to Azure SQL Database

The virtual machine connects to the Azure SQL Database using SQL command-line tools, leveraging credentials retrieved securely from Azure Key Vault.

---

### Prerequisites

- Azure CLI installed
- SQL command-line tools (`sqlcmd`) installed

---

### Connection Example

```bash
sqlcmd -S sql-server-lab003.database.windows.net -U sqladmin
```
When prompted, enter the password retrieved from **Azure Key Vault**.

The connection completes successfully, confirming secure database access from the virtual machine.

---

## Validation Results

- SQL admin password securely stored in Azure Key Vault
- Virtual machine authenticated to Key Vault using managed identity
- Secret retrieved without hard-coded credentials
- Successful Azure SQL database login from the VM

## Security Principles Applied

- Centralized secrets management using Azure Key Vault
- Managed identity authentication
- Elimination of hard-coded credentials
- Least-privilege access controls
- Secure database access from trusted compute resources

---

## Conclusion

This lab demonstrates a secure and production-aligned approach to managing database credentials in Azure. By combining **Azure SQL**, **Azure Key Vault**, and **managed identities**, the solution minimizes credential exposure and aligns with cloud security best practices.

---

## Skills Demonstrated

- Azure SQL Database deployment
- Azure Key Vault configuration
- Managed identity implementation
- Secure secrets retrieval using Azure CLI
- SQL connectivity from Linux-based virtual machines
- Cloud security best practices


