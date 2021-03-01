# AZCOPYDSC

PowerShell AZCOPY DSC __Class based Resource__

This is a DSC Resource for performing File sync tasks with Azure File Shares

[azcopy sync /?](https://docs.microsoft.com/en-us/azure/storage/common/storage-ref-azcopy-sync)

__Requirements__
* PowerShell Version 5.0 +
* Server 2012 +

```powershell
    # sample configuation data

            # Blob copy with Managed Identity - Oauth2
            AZCOPYDSCDirPresentSource   = @(

                @{
                    SourcePath      = 'https://{0}.blob.core.windows.net/PSModules/'
                    DestinationPath = 'F:\Source\PSModules\'
                },

                @{
                    SourcePath      = 'https://{0}.blob.core.windows.net/Tools/'
                    DestinationPath = 'F:\Source\Tools\'
                }
            )
```


```powershell


Configuration AppServers
{
    Param (
        [String]$StorageAccountId,
        [String]$clientIDGlobal
    )

    node $AllNodes.NodeName
    {
        $StringFilter = '\W', ''
        #-------------------------------------------------------------------     
        foreach ($AZCOPYDSCDir in $Node.AZCOPYDSCDirPresentSource)
        {
            $Name = ($AZCOPYDSCDir.SourcePath -f $StorageAccountName + $AZCOPYDSCDir.DestinationPath) -replace $StringFilter 
            AZCOPYDSCDir $Name
            {
                SourcePath              = ($AZCOPYDSCDir.filesSourcePath -f $StorageAccountName)
                DestinationPath         = $AZCOPYDSCDir.filesDestinationPath
                Ensure                  = 'Present'
                ManagedIdentityClientID = $clientIDGlobal
                LogDir                  = 'F:\azcopy_logs'
            }
            $dependsonDirectory += @("[AZCOPYDSCDir]$Name")
        }
```

Full sample available here

- DSC Configuration
    - [ADF/ext-DSC/DSC-WVDServers.ps1](https://github.com/brwilkinson/AzureDeploymentFramework/blob/65fc52c88806f495651d14ee67136967af354982/ADF/ext-DSC/DSC-WVDServers.ps1)
- DSC ConfigurationData
    - [ADF/ext-CD/WVD-ConfigurationData.psd1](https://github.com/brwilkinson/AzureDeploymentFramework/blob/65fc52c88806f495651d14ee67136967af354982/ADF/ext-CD/WVD-ConfigurationData.psd1)