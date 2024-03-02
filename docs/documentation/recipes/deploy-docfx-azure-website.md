---
layout: default
title: deploy-docfx-azure-website
nav_order: 1
has_children: false
---

# Deploy the DocFx Documentation website to an Azure Website automatically

In the article [Using DocFx and Companion Tools to generate a Documentation website](using-docfx-and-tools.md) the process is described to generate content of a documentation website using DocFx. This document describes how to setup an Azure Website to host the content and automate the deployment to it using a pipeline in Azure DevOps.

The [QuickStart sample](https://github.com/mtirionMSFT/DocFxQuickStart) that is provided for a quick setup of DocFx generation also contains the files explained in this document. Especially the **.pipelines** and **infrastructure** folders.

The following steps can be followed when using the Quick Start folder. In the **infrastructure** folder you can find the Terraform files to create the website in an Azure environment. Out of the box, the script will create a website where the documentation content can be deployed to.

## 1. Install Terraform

You can use tools like [Chocolatey](https://chocolatey.org/) to install Terraform:

```shell
choco install terraform
```

## 2. Set the proper variables

> **IMPORTANT:** Make sure you modify the value of the **app_name**, **rg_name** and **rg_location** variables. The *app_name* value is appended by **azurewebsites.net** and must be unique. Otherwise the script will fail that it cannot create the website.

In the Quick Start, authentication is disabled. If you want that enabled, make sure you have create an *Application* in the Azure AD and have the *client ID*. This client id must be set as the value of the **client_id** variable in *variables.tf*. In the *main.tf* make sure you uncomment the authentication settings in the *app-service*. For more information see [Configure Azure AD authentication - Azure App Service](https://learn.microsoft.com/en-us/azure/app-service/configure-authentication-provider-aad).

If you want to set a custom domain for your documentation website with an SSL certificate you have to do some extra steps. You have to create a *Key Vault* and store the certificate there. Next step is to uncomment and set the values in *variables.tf*. You also have to uncomment the necessary steps in *main.tf*. All is indicated by comment-boxes. For more information see [Add a TLS/SSL certificate in Azure App Service](https://learn.microsoft.com/en-us/azure/app-service/configure-ssl-certificate).

Some extra information on SSL certificate, custom domain and Azure App Service can be found in the following paragraphs. If you are familiar with that or don't need it, go ahead and continue with [Step 3](#3-deploy-azure-resources-from-your-local-machine).

### SSL Certificate

To secure a website with a custom domain name and a certificate, you can find the steps to take in the article [Add a TLS/SSL certificate in Azure App Service](https://learn.microsoft.com/en-us/azure/app-service/configure-ssl-certificate). That article also contains a description of ways to obtain a certificate and the requirements for a certificate. Usually you'll get a certificate from the customers IT department. If you want to start with a development certificate to test the process, you can create one yourself. You can do that in PowerShell with the script below. Replace:

* **[YOUR DOMAIN]** with the domain you would like to register, e.g. `docs.somewhere.com`
* **[PASSWORD]** with a password of the certificate. It's required for uploading a certificate in the Key Vault to have a password. You'll need this password in that step.
* **[FILENAME]** for the output file name of the certificate. You can even insert the path here where it should be store on your machine.

You can store this script in a PowerShell script file (ps1 extension).

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation cert: