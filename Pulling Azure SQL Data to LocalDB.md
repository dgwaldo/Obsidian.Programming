# Developer Data

Shared databases pose a liability for development teams. If someone breaks the development database, it needs to be recovered or reset from production. The whole team is impacted if everyone depends on a shared development database. Each developer working off a copy of the database addresses these issues.

This guide addresses the specifics of working with an Azure SQL database.

·       Connecting to Azure

·       Copying data to your local machine

·       Restoring the database into LocalDB

# Connecting to Azure

Download and install the latest version of [SQL Server Management Studio](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15).  

Open SSMS and login to the Azure database. Connection details can be found on Azure.

_Note: SSMS may prompt you to login to Azure and ask to add your IP to the Azure SQL DB access whitelist._

![[Pasted image 20221116114727.png]]

# Copying Data

Next find the database that you’d like to copy to your local machine.

Right click and follow the menu structure. Tasks -> Export Data-tier Application

![[Pasted image 20221116114737.png]]

You will be greeted by a friendly wizard.

![[Pasted image 20221116114746.png]]

Choose a spot to store the bacpac backup file.

![[Pasted image 20221116114759.png]]

Verify the settings and continue. The wizard dialog will show the progress of the file download.

![[Pasted image 20221116114808.png]]

A summary is provided when the operation completes.

![[Pasted image 20221116114815.png]]

# Restoring to Local DB

In SSMS switch your database connection to LocalDb. If you haven’t yet added this connection, the connection string is: (localdb)MSSQLLocalDB

![[Pasted image 20221116114825.png]]

Right click the databases folder in SQL and follow the menu structure.  Import Data-tier Application…

![](file:///C:/Users/DW/AppData/Local/Temp/msohtmlclip1/01/clip_image002.jpg)
You’ll be greeted by a second friendly wizard.

In the Import from local disk dialog choose the file you downloaded in the previous steps.

![[Pasted image 20221116115042.png]]

Name the database and continue.

![[Pasted image 20221116115101.png]]

Review your settings and continue again. The wizard will once again finish with a summary.

![[Pasted image 20221116115121.png]]

Looking back at the databases contained in LocalDB, you should see your newly imported database.

![[Pasted image 20221116115131.png]]

# Making the Connection

To use this database for your connection in development you’ll need to modify any connection strings in your application to point at it.

"Data Source = (localdb)\\MSSQLLocalDB; Initial Catalog = DevelopmentDb;"