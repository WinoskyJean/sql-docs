---
title: "Server memory configuration options | Microsoft Docs"
ms.custom: contperf-fy20q4
ms.date: "08/10/2021"
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ""
ms.technology: configuration
ms.topic: conceptual
description: Learn how to configure the amount of memory the SQL Server Memory Manager allocates to SQL Server processes. View memory management approaches and examples.
helpviewer_keywords: 
  - "Virtual Memory Manager"
  - "max server memory option"
  - "virtual memory [SQL Server]"
  - "VMM"
  - "server memory options [SQL Server]"
  - "servers [SQL Server], memory"
  - "buffer pools [SQL Server]"
  - "min server memory option"
  - "manual memory options [SQL Server]"
  - "memory [SQL Server], servers"
ms.assetid: 29ce373e-18f8-46ff-aea6-15bbb10fb9c2
author: pmasl
ms.author: pelopes
---
# Server memory configuration options
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

Reconfigure the amount of memory (in megabytes) for a SQL Server process used by an instance of [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)].  There are two server memory options, **min server memory** and **max server memory**. These options change the amount of memory the SQL Server Memory Manager can allocate to a SQL Server process.
  
The default settings and minimum allowable values for these options are:

|Option  |  Default | Min allowable  |
|---------|---------|---------|
|**min server memory**     |    0     |    0     |
|**max server memory**     |     2,147,483,647 megabytes (MB)     |  128 MB       |

By default, [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] can change its memory requirements dynamically based on available system resources. For more information, see [dynamic memory management](../../relational-databases/memory-management-architecture-guide.md#dynamic-memory-management).

> [!IMPORTANT]  
> Setting **max server memory** value too high can cause a single instance of [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] to compete for memory with other [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] instances hosted on the same host. However, setting this value too low could cause significant memory pressure and performance problems. 
> Setting **max server memory** to the minimum value can even prevent [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] from starting. If you cannot start [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] after changing this option, start it using the **_-f_** startup option and reset **max server memory** to its previous value. For more information, see [Database Engine Service Startup Options](../../database-engine/configure-windows/database-engine-service-startup-options.md).  
    
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] can use memory dynamically; however, you can set the memory options manually and restrict the amount of memory that [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] can access. Before you set the amount of memory for [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)], determine the appropriate memory setting by subtracting, from the total physical memory, the memory required for the OS, memory allocations not controlled by the max_server_memory setting, and any other instances of [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (and other system uses, if the computer is not wholly dedicated to [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]). This difference is the maximum amount of memory you can assign to the current [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] instance.  
 
## <a name="manually"></a> Set options manually  
The server options **min server memory** and **max server memory** can be set to span a range of memory values. This method is useful for system or database administrators to configure an instance of [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] in conjunction with the memory requirements of other applications, or other instances of [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] that run on the same host.

> [!NOTE]
> The **min server memory** and **max server memory** options are advanced options. If you are using the **sp_configure** system stored procedure to change these settings, you can change them only when **show advanced options** is set to 1. These settings take effect immediately without a server restart.  
  
<a name="min_server_memory"></a> Use **min_server_memory** to guarantee a minimum amount of memory available to the [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Memory Manager for an instance of [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]. [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] will not immediately allocate the amount of memory specified in **min server memory** on startup. However, after memory usage has reached this value due to client load, [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] cannot free memory unless the value of **min server memory** is reduced. For example, when several instances of [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] can exist concurrently in the same host, set the min_server_memory parameter instead of max_server_memory for the purpose of reserving memory for an instance. Also, setting a min_server_memory value is essential in a virtualized environment to ensure memory pressure from the underlying host does not attempt to deallocate memory from the buffer pool on a guest [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] virtual machine (VM) beyond what is needed for acceptable performance.

>[!NOTE]
>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] is not guaranteed to allocate the amount of memory specified in **min server memory**. If the load on the server never requires allocating the amount of memory specified in **min server memory**, [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] will run with less memory.

<a name="max_server_memory"></a> Use **max_server_memory** to guarantee the OS does not experience detrimental memory pressure. To set max server memory configuration, monitor overall consumption of the [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] process in order to determine memory requirements. For an initial configuration or when there was no opportunity to collect [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] process memory usage over time, use the following generalized best practice approach to configure **max_server_memory** for a single instance:
- From the total OS memory, subtract the equivalent of potential [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] memory allocations outside the **max server memory** control, which is comprised of **stack size <sup>1</sup> \* calculated max worker threads <sup>2</sup>**. 
- Then subtract 25% for other memory allocations outside the **max server memory** control, such as backup buffers, and other components. This is a generic approximation, mileage may vary.
- What remains should be the max_server_memory setting for a single instance setup.

<sup>1</sup> Refer to the [Memory Management Architecture guide](../../relational-databases/memory-management-architecture-guide.md#stacksizes) for information on thread stack sizes per architecture.

<sup>2</sup> Refer to the documentation page on how to [Configure the max worker threads Server Configuration Option](../../database-engine/configure-windows/configure-the-max-worker-threads-server-configuration-option.md), for information on the calculated default worker threads for a given number of affinitized CPUs in the current host.

## Use [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]  
Use the two server memory options, **min server memory** and **max server memory**, to reconfigure the amount of memory (in megabytes) managed by the [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Memory Manager for an instance of [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]. By default, [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] can change its memory requirements dynamically based on available system resources.  
  
### Procedure for configuring a fixed amount of memory (not recommended)  
To set a fixed amount of memory:  
  
1.  In Object Explorer, right-click a server and select **Properties**.  
  
2.  Click the **Memory** node.  
  
3.  Under **Server Memory Options**, enter the amount that you want for **Minimum server memory** and **Maximum server memory**.  
  
     Use the default settings to allow [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] to change its memory requirements dynamically based on available system resources. It is recommended to set a **max server memory** as [detailed above](#max_server_memory). 

The following screenshot demonstrates all three steps: 

:::image type="content" source="media/server-memory-server-configuration-options/configure-memory-in-ssms.png" alt-text="Configure memory in SSMS":::

  
## Lock Pages in Memory (LPIM) 
This Windows policy determines which accounts can use a process to keep data in physical memory, preventing the system from paging the data to virtual memory on disk. Locking pages in memory may keep the server responsive when paging memory to disk occurs. The **Lock Pages in Memory** option is set to ON in instances of [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Standard edition and higher when the account with privileges to run sqlservr.exe has been granted the Windows *Lock Pages in Memory* (LPIM) user right.  
  
To disable the **Lock Pages In Memory** option for [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)], remove the *Lock Pages in Memory* user right for the account with privileges to run sqlservr.exe (the [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] startup account) startup account.  
 
Setting this option does not affect [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [dynamic memory management](../../relational-databases/memory-management-architecture-guide.md#dynamic-memory-management), allowing it to expand or shrink at the request of other memory clerks. When using the *Lock Pages in Memory* user right it is recommended to set an upper limit for **max server memory** as [detailed above](#max_server_memory).

> [!IMPORTANT]
> Setting this option should only be used when necessary, namely if there are signs that sqlservr process is being paged out.
> In this case, error 17890 will be reported in the Errorlog, resembling the below example:
> `A significant part of sql server process memory has been paged out. This may result in a performance degradation. Duration: #### seconds. Working set (KB): ####, committed (KB): ####, memory utilization: ##%.`
> Starting with [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)], [trace flag 845](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md) is not needed for Standard Edition to use Locked Pages. 
  
### To enable Lock Pages in Memory  
To enable the lock pages in memory option:  
  
1.  On the **Start** menu, click **Run**. In the **Open** box, type **gpedit.msc**.  
  
     The **Group Policy** dialog box opens.  
  
2.  On the **Group Policy** console, expand **Computer Configuration**, and then expand **Windows Settings**.  
  
3.  Expand **Security Settings**, and then expand **Local Policies**.  
  
4.  Select the **User Rights Assignment** folder.  
  
     The policies will be displayed in the details pane.  
  
5.  In the pane, double-click **Lock pages in memory**.  
  
6.  In the **Local Security Policy Setting** dialog box, add the account with privileges to run sqlservr.exe (the [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] startup account).  
  
## Multiple instances of [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]  
 When you are running multiple instances of the [!INCLUDE[ssDE](../../includes/ssde-md.md)], there are three approaches you can use to manage memory:  
  
-   Use **max server memory** to control memory usage, as [detailed above](#max_server_memory). Establish maximum settings for each instance, being careful that the total allowance is not more than the total physical memory on your machine. You might want to give each instance memory proportional to its expected workload or database size. This approach has the advantage that when new processes or instances start up, free memory will be available to them immediately. The drawback is that if you are not running all of the instances, none of the running instances will be able to utilize the remaining free memory.  
  
-   Use **min server memory** to control memory usage, as [detailed above](#min_server_memory). Establish minimum settings for each instance, so that the sum of these minimums is 1-2 GB less than the total physical memory on your machine. Again, you may establish these minimums proportionately to the expected load of that instance. This approach has the advantage that if not all instances are running at the same time, the ones that are running can use the remaining free memory. This approach is also useful when there is another memory-intensive process on the computer, since it would insure that [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] would at least get a reasonable amount of memory. The drawback is that when a new instance (or any other process) starts, it may take some time for the running instances to release memory, especially if they must write modified pages back to their databases to do so.  
  
-   Do nothing (not recommended). The first instances presented with a workload will tend to allocate all of memory. Idle instances, or instances started later, may end up running with only a minimal amount of memory available. [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] makes no attempt to balance memory usage across instances. All instances will, however, respond to Windows Memory Notification signals to adjust the size of their memory footprint. Windows does not balance memory across applications with the Memory Notification API. It merely provides global feedback as to the availability of memory on the system.  
  
 You can change these settings without restarting the instances, so you can easily experiment to find the best settings for your usage pattern.  
  
## Provide the maximum amount of memory

Memory can be configured up to the process virtual address space limit in all [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] editions. For more information, see [Memory Limits for Windows and Windows Server Releases](/windows/desktop/Memory/memory-limits-for-windows-releases#physical-memory-limits-windows-server-2016).

## Examples

### Example A. Set the max server memory option to 4 GB.
 The following example sets the `max server memory` option to 4 GB.  Note that although `sp_configure` specifies the name of the option as `max server memory (MB)`, the example demonstrates omitting the `(MB)`.

```sql
sp_configure 'show advanced options', 1;
GO
RECONFIGURE;
GO
sp_configure 'max server memory', 4096;
GO
RECONFIGURE;
GO
```
This will output a statement similar to:

> Configuration option 'max server memory (MB)' changed from 2147483647 to 4096. Run the RECONFIGURE statement to install.

### Example B. Determining Current Memory Allocation  
 The following query returns information about currently allocated memory.  

```sql  
SELECT 
  physical_memory_in_use_kb/1024 AS sql_physical_memory_in_use_MB, 
   large_page_allocations_kb/1024 AS sql_large_page_allocations_MB, 
   locked_page_allocations_kb/1024 AS sql_locked_page_allocations_MB,
   virtual_address_space_reserved_kb/1024 AS sql_VAS_reserved_MB, 
   virtual_address_space_committed_kb/1024 AS sql_VAS_committed_MB, 
   virtual_address_space_available_kb/1024 AS sql_VAS_available_MB,
   page_fault_count AS sql_page_fault_count,
   memory_utilization_percentage AS sql_memory_utilization_percentage, 
   process_physical_memory_low AS sql_process_physical_memory_low, 
   process_virtual_memory_low AS sql_process_virtual_memory_low
FROM sys.dm_os_process_memory;  
```  

### Example C. Determining value for 'max server memory (MB)'
The following query returns information about the currently configured value and the value in use by SQL Server.  This query will return results regardless of whether 'show advanced options' is true.

```sql
SELECT c.value, c.value_in_use
FROM sys.configurations c WHERE c.[name] = 'max server memory (MB)'
```
  
## Next steps
 [Memory Management Architecture Guide](../../relational-databases/memory-management-architecture-guide.md)   
 [Monitor and Tune for Performance](../../relational-databases/performance/monitor-and-tune-for-performance.md)   
 [RECONFIGURE &#40;Transact-SQL&#41;](../../t-sql/language-elements/reconfigure-transact-sql.md)   
 [Server Configuration Options &#40;SQL Server&#41;](../../database-engine/configure-windows/server-configuration-options-sql-server.md)   
 [sp_configure &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)   
 [Database Engine Service Startup Options](../../database-engine/configure-windows/database-engine-service-startup-options.md)   
 [Editions and supported features of SQL Server 2016](../../sql-server/editions-and-components-of-sql-server-2016.md#Cross-BoxScaleLimits)   
 [Editions and supported features of SQL Server 2017](../../sql-server/editions-and-components-of-sql-server-2017.md#Cross-BoxScaleLimits)   
 [Editions and supported features of SQL Server 2017 on Linux](../../linux/sql-server-linux-editions-and-components-2017.md#Cross-BoxScaleLimits)   
 [Memory Limits for Windows and Windows Server Releases](/windows/desktop/Memory/memory-limits-for-windows-releases)
