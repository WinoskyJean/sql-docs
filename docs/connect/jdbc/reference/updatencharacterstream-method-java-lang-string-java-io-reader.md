---
description: "updateNCharacterStream Method (java.lang.String, java.io.Reader)"
title: "updateNCharacterStream Method (java.lang.String, java.io.Reader) | Microsoft Docs"
ms.custom: ""
ms.date: "01/19/2017"
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ""
ms.technology: connectivity
ms.topic: reference
ms.assetid: 504d7d06-0227-45e1-8b01-899c3e6006e8
author: David-Engel
ms.author: v-davidengel
---
# updateNCharacterStream Method (java.lang.String, java.io.Reader)
[!INCLUDE[Driver_JDBC_Download](../../../includes/driver_jdbc_download.md)]

  Updates the designated column with a character stream value.  
  
## Syntax  
  
```  
  
public void updateNCharacterStream(java.lang.String columnLabel,  
                                  java.io.Reader reader)  
```  
  
#### Parameters  
 *columnLabel*  
  
 A **String** that contains the column label.  
  
 *reader*  
  
 A Reader object.  
  
## Exceptions  
 [SQLServerException](../../../connect/jdbc/reference/sqlserverexception-class.md)  
  
## Remarks  
 This updateNCharacterStream method is specified by the updateNCharacterStream method in the java.sql.ResultSet interface.  
  
 This method passes Unicode characters from a Reader object to selected **nchar**, **nvarchar(max)**, **ntext** and **xml** columns. Using this method on other data type columns will throw an exception.  
  
## See Also  
 [updateNCharacterStream Method &#40;SQLServerResultSet&#41;](../../../connect/jdbc/reference/updatencharacterstream-method-sqlserverresultset.md)   
 [SQLServerResultSet Members](../../../connect/jdbc/reference/sqlserverresultset-members.md)   
 [SQLServerResultSet Class](../../../connect/jdbc/reference/sqlserverresultset-class.md)  
  
  
