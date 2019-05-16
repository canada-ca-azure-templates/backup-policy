#### NIST Controls / ITSG-33 Controls

CP-9.a, CP-9.b, CP-9 (5)

## Implementation and Configuration

#### Recovery Services Vault

- **Name**: <subscription>-<deployment>-Backup-Vault
- **Protected Items**: All VMs

#### Backup Policy

- **Name**: DailyBackupPolicy
- **Backup frequency**: Daily 2:30AM
- **Daily Retention**: 30 Days
- **Weekly Retention**: Sunday for 104 weeks
- **Monthly Retention**: Day based 1st of month for 36 months
- **Yearly Retention**: Day based 1st of January for 5 years

#### Encryption

- Implemented using the **Azure Disk Encryption Extension**

#### System Level Backup

- Implemented using protectedItem resource deployment and **DailyBackupPolicy**

#### User Level Backup

- Implemented using **SQL VM IaaS Extension**
- **Enable**: true,
- **RetentionPeriod**: 30
- **EnableEncryption**: true,
- **BackupScheduleType**: Manual,
- **FullBackupFrequency**: Weekly,
- **FullBackupStartTime**: 2,
- **FullBackupWindowHours**: 2,
- **LogBackupFrequency**: 60

## Compliance Documentation

**CP-9.a: The organization conducts backups of user-level information contained in the information system.**
This template can be used to deploy Azure Backup policies for all virtual machines.

**CP-9.b: The organization conducts backups of system-level information contained in the information system.**
This template can be used to deploy Azure Backup policies for all virtual machines.

**CP-9 (5): The organization the organization transfers information system backup information to the alternate storage site at an organization-defined time period and transfer rate consistent with recovery time and recovery point objectives.**
All deployed storage accounts within this template, including those used for backup, implement geo-redundant storage, ensuring six copies of all data are maintained on separate nodes across two data centers.