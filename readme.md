# Backup Policy

## Introduction

This template is used to create [Backup Policies](<https://docs.microsoft.com/en-us/azure/templates/microsoft.recoveryservices/2016-06-01/vaults/backuppolicies>)

## Security Controls

The following security controls can be met through configuration of this template:

* [Backups](documentation/backup.md): CP-9.a, CP-9.b, CP-9 (5)

## Dependancies

The following items are assumed to exist already in the deployment:

* [Resource Group](<https://github.com/canada-ca-azure-templates/resourcegroups>)
* [Recvery Service Vault](<https://github.com/canada-ca-azure-templates/recovery-service-vault>)

## Parameter format

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Recovery Services Vault"
            }
        },
        "policyName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Backup Policy"
            }
        },
        "scheduleRunTimes": {
            "type": "array",
            "metadata": {
                "description": "Times in day when backup should be triggered. e.g. 01:00 or 13:00. Must be an array, however for IaaS VMs only one value is valid. This will be used in LTR too for daily, weekly, monthly and yearly backup."
            }
        },
        "timeZone": {
            "type": "string",
            "metadata": {
                "description": "Any Valid timezone, for example:UTC, Pacific Standard Time. Refer: https://msdn.microsoft.com/en-us/library/gg154758.aspx"
            }
        },
        "dailyRetentionDurationCount": {
            "type": "int",
            "metadata": {
                "description": "Number of days you want to retain the backup"
            }
        },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults",
            "apiVersion": "2015-11-10",
            "name": "[parameters('vaultName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "RS0",
                "tier": "Standard"
            },
            "properties": {}
        },
        {
            "apiVersion": "2016-06-01",
            "name": "[concat(parameters('vaultName'), '/', parameters('policyName'))]",
            "type": "Microsoft.RecoveryServices/vaults/backupPolicies",
            "dependsOn": [
                "[concat('Microsoft.RecoveryServices/vaults/', parameters('vaultName'))]"
            ],
            "location": "[parameters('location')]",
            "properties": {
                "backupManagementType": "AzureIaasVM",
                "schedulePolicy": {
                    "scheduleRunFrequency": "Daily",
                    "scheduleRunDays": null,
                    "scheduleRunTimes": "[parameters('scheduleRunTimes')]",
                    "schedulePolicyType": "SimpleSchedulePolicy"
                },
                "retentionPolicy": {
                    "dailySchedule": {
                        "retentionTimes": "[parameters('scheduleRunTimes')]",
                        "retentionDuration": {
                            "count": "[parameters('dailyRetentionDurationCount')]",
                            "durationType": "Days"
                        }
                    },
                    "retentionPolicyType": "LongTermRetentionPolicy"
                },
                "timeZone": "[parameters('timeZone')]"
            }
        }
    ]
}
```

## Parameter Values

### Main Template

|Name        |Type   |Required |Value                               |
|------------|-------|---------|------------------------------------|
|vaultName   |string| Yes|The name of the recovery service vault to apply the backup policy on|
|policyName |string |Yes|the name of the backup policy|
|scheduleRunTimes|array| Yes|Times in day when backup should be triggered. e.g. 01:00 or 13:00. Must be an array, however for IaaS VMs only one value is valid. This will be used in LTR too for daily, weekly, monthly and yearly backup.|
|timeZone|string|Yes|Any Valid timezone, for example:UTC, Pacific Standard Time. Refer: https://msdn.microsoft.com/en-us/library/gg154758.aspx|
|dailyRetentionDurationCount |int|Yes|Number of days you want to retain the backup|
|location|string|No|The location for the backup policy|
  
## History

|Date       |Release| Change                |
|-----------|-------|-----------------------|
| 20190516 | [20190516](https://github.com/canada-ca-azure-templates/backup-policy/tree/20190516) | Created test validation.|
