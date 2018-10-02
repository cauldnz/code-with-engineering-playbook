# Principles & Practices for Enhanced Security Projects 

## CSE Authentication and Authorization for Enhanced Security Projects

*CJTA - TODO: We should discuss mandating the use of two factor auth for all enhanced security projects*.

## Secret Management for Enhanced Security Projects

Developers working on [CSE](../CSE.md) projects with Enhanced Security requirements *CJTA - TODO: Suggest this gets flagged somehow in VSTS?* will use secure secret storage techiques to minimize risk of compromize.

A _Secret_ in this context, is anything that you need to store and access but which you want to tightly control access to. Examples of common secrets include credentials, tokens, certificates and other pieces of information that are used to authenticate access to other resources or to encrypt, decrypt, sign and verify information.

### Goals

* Follow a standardized approach to secure _all_ secrets for _all_ Enhanced Security projects; secure storage must be provided for not only production secrets, but also those used throughout the rest of the development lifecycle.
* Secrets must never be shared by non-secure means such as email, teams chats etc...
* Storage of secrets on developer workstations should be eliminated as far as is practicable

### Evidence and Measures

* [ ] All secrets are stored in the agreed project secure store. The default secret store for CSE projects is Azure Key Vault.
* [ ] No secrets are committed into [source control](../Engineering/SourceControl.md) stores
* [ ] If secrets are committed to source control, appropriate remediation is taken. *CJTA - TODO:* Let's discuss remediation requirements; my position is that as soon as a secret is pushed it must be rolled.
* [ ] Secret access is granted on the [Principle of Least Privlege](https://en.wikipedia.org/wiki/Principle_of_least_privilege) and differential access policies are always applied as between development and production secrets stores.
* [ ] Access to secrets is audited and those audit log files are secured against unauthorized tampering and deletion. The default storage for Key Vault Logging files must be _Immutable blob storage_ with a _retention period_ of no less than 120 days and a _locked_ policy.
* [ ] Secrets are not written to logs, audit files, local storage, azure storage or any storage other than the agreed project secret store


### Approved Secret Stores

An Enhanced Security project must make use of an agreed secure secret store. The following are pre-approved secure secret stores for CSE projects.

* [Azure Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/)
* *CJTA - TODO: Suggest that we consider explicitly evaluating and blessing key stores for other major cloud platforms so that these can be used in situations where we work on multi-cloud projects. e.g. AWS KMS, Google Cloud HSM etc...

### Secret Lifecycle

#### Creation

Where possible, secrets should be created directly in the secret store itself.  

The use of [Key Vault Certificates](https://docs.microsoft.com/en-us/azure/key-vault/certificate-scenarios) allows certificates to be created directly within the store. . Creating certificates within the store guarantees that every operation on that certificate will be auditable back through to initial creation.

#### Deployment

*CJTA - TODO: Discuss:*

* ARM integration per https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-keyvault-parameter
* App Service Certificate deployment including the Certificate based secret trick discussed in the article. https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/



#### Access Control

As a general principle, CSE team members and other Microsoft staff or contractors:

* should not have management plane access for  secret stores running in customer subscriptions
* should not have management plane or data plane access to production secrets

When using Azure Key Vault, the use of [Firewall and Virtual Network Service Endpoints](https://docs.microsoft.com/en-nz/azure/key-vault/key-vault-network-security) to protect the Key Vault endpoints is recommended. 

#### Auditing

[Azure Key Vault Logging](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-logging) should be enabled whenever Azure Key Vault is used as an agreed project secure store.  The storage account used for Key Vault Logging should be configured as [_Immutable blob storage_](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-immutable-storage#Getting-started) with _Time-based retention_.  The _retention period_ should be set to a value of no less than 120 days. The policy should be _locked_ immediately after creation.

#### Rotation

#### Other Services
Discuss integration with;

* Azure SQL Database and SQL Server
* Azure Storage
* Azure Disk Encryption
* Data Lake Store
* Information Protection
* CosmosDB https://github.com/rsarosh/CosmosDB-KeyVault & https://sarosh.wordpress.com/2017/11/23/cosmos-db-and-key-vault/

## Resources

* [Azure Key Vault FlexVol](https://github.com/Azure/kubernetes-keyvault-flexvol) allows developers to access application-specific secrets, keys, and certs stored in Azure Key Vault directly from their _Kubernetes_ pods.
