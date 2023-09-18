# SearchStax Cloud Connector for Sitecore
## Introduction
This script is used to connect a Sitcore installation to a SearchStax' Solr instance. 
It does following: 
- Upload the config files to Solr
- Create collections in Solr
- Configure sitecore files

## Supported Sitecore Versions
Currently the script only supports following sitecore XP versions and their XConnect:
- 9.0 Update-2 (9.0.2)
- 9.1 Update-1 (9.1.1) 
- 9.2 Initial Update (9.2.0)
- 9.3 Initial Update (9.3.0)
- 10.0 Initial Update (10.0.0)
- 10.0 Update-1 (10.0.1)
- 10.0 Update-2 (10.0.2)
- 10.0 Update-3 (10.0.3)
- 10.1 Initial Update (10.1.0)
- 10.1 Update-1 (10.1.1)
- 10.1 Update-2 (10.1.2)
- 10.2 Initial Update (10.2.0)
- 10.3 Initial Update (10.3.0)

## Requirements
- Powershell v6 or above
- Powershell-yaml  module

### Installing
#### Powershell v6
Powershell v6 can be installed by running following command via Powershell Windows.
```powershell
iex "& { $(irm https://aka.ms/install-powershell.ps1) } -UseMSI"
```

#### Powershell Yaml Module
```powershell
Install-Module powershell-yaml
```


In order to run the script, first you have to update the config file
### Config File
Config file is located at `.\config.yml`  

It contains following fields:

|Name|Description|Example|
|----|:-----------|:-----|
|accountName|Name of the SearchStax account| ABCInternational |
|deploymentUid| UID of the SearchStax deployment to connect to| ss123456 |
|sitecorePrefix| Prefix of the sitecore installation| sitecore |
|pathToWWWRoot| Path to wwwroot folder in inetpub, i.e. your %IIS_SITE_HOME% variable| C:\inetpub\wwwroot|
|solrUsername| Solr username (Optional)||
|solrPassword| Solr password (Optional)||
|sitecoreVersion| Version of sitecore from the above list| 10.1.1|
|isUniqueConfigs| "true" will create a separate config file for each collection, "false" will create only 1 config which will be used by all the collections. (Note: This defaults to true for Sitecore v9.0.2) | true/false|
|configurationMode| Select the part of Sitecore being configured - "XP", "XCONNECT" | XP\|XCONNECT|
|isAzurePaaS| "True" will only configure Solr. "False" will configure both Solr and On-Premise Sitecore | true/false|
|isSxa| "True" will add two additional collections to Solr for SXA support | true/false|

### Instructions
1. Configure the `config.yml` file.
2. Start Powershell v6 as Administrator.
3. Change the execution policy to skip checking.
```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
```
4. Execute following command
```powershell
.\sitecore-searchstax-connector.ps1
```
5. Go to your sitecore page > Control Panel > Populate Solr Managed Schema > Select All > Populate
6. On the same page, Indexing Manager > Select All > Rebuild

### Best Practice/Recommendation:
Use your CI/CD deployment to set Configuration and ConnectionString values using secure environment unique tokens/variables.

### Instructions for PaaS/Scaled/Custom Environments
Every Sitecore Web Instance (CD/CM) needs these updates:
1. Web.config: ```<AppSettings><add key="search:define" value="Solr"/>``` - set to Solr
2. App_Config/ConnectionStrings.config: ```<add name="solr.search" connectionString=```

Every XConnect Instance needs these updates:
1. App_Config/ConnectionStrings.config: ```<add name="solrCore" connectionString=```
2. App_Data\jobs\continuous\IndexWorker\App_Config\ConnectionStrings.config: ```<add name="solrCore" connectionString=```
3. Use the SearchStax API and upload the Schema - App_Data\solrcommands\schema.json to both XDB Collections. - reference: https://lucene.apache.org/solr/guide/8_6/schema-api.html

## How can I get help with SearchStax Cloud Connector for Sitecore?

You can use GitHub to submit [bug reports](https://github.com/searchstax/searchstax-cloud-connector-for-sitecore/issues/new?template=bug_report.md) or [feature requests](https://github.com/searchstax/searchstax-cloud-connector-for-sitecore/issues/new?template=feature_request.md) for Searchstax-Cloud-Connector-for-Sitecore. Please do not submit usage questions via GitHub.

## FAQ
### Recommended Performance Optimization Settings by Sitecore
Sitecore recommends updating some settings for performance optimization when working with Solr. You can find more details here: [Performance optimizations when using Solr](https://kb.sitecore.net/articles/671173)

### Sitecore SXA
This plugin currently does not support Sitecore SXA configuration.
### IP Filtering
If you have enabled IP filtering on your Solr instance, then make sure that you add the IP/CIDR block of your network or machine to the IP Filtering page. For more instructions on how to set up IP filtering, please follow our guide here - [How To Set-up IP Filtering](https://www.searchstax.com/docs/security/#IPfilter)
### Sitecore v9.0 Update-2
The plugin will automatically default to creating a separate config directory for every collection when being used to setup Sitecore v9.0.2.
### Have additional questions?
Check [SearchStax Help Center](https://www.searchstax.com/docs/helpcenter/)
