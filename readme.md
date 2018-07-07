MySql Query Engine Bug Report  
Sat Jul 7, 2018  
Reporter: Marco Marques (developer@mdc.com.br)  
Repository: MDC Brasil (https://github.com/mdcbrasil/mysql)  
  
Environment:
===========
Engine  : MySQL 5.7  
Hosting : Google Cloud Platform  
Service : Cloud SQL MySql  
   
Excerpt:
=======
Using WHERE IN with a temporary table feed as a parameter, MySql query engine returns a completely unexpected result.
  
Steps to reproduce:
==================
  
CREATE TEMPORARY TABLE IF NOT EXISTS StoreRev (Stores nvarchar(44));  
TRUNCATE TABLE StoreRev;  
INSERT INTO StoreRev SELECT StoreId FROM MOS_ClienteMDC WHERE StoreId IN ('00013');  
  
CREATE TEMPORARY TABLE IF NOT EXISTS CaixaRev (CaixaID int, StoreID char(20));  
TRUNCATE TABLE CaixaRev;  
INSERT INTO CaixaRev SELECT CaixaId, StoreId FROM MOS_Caixa CX WHERE CAST(CX.TurnoData AS date) BETWEEN '2018-05-01' AND '2018-05-01' AND CX.StoreID IN (SELECT StoreID FROM StoreRev);  
  
SELECT * FROM StoreRev;  
SELECT * FROM CaixaRev;  
  
DROP TEMPORARY TABLE StoreRev;  
DROP TEMPORARY TABLE CaixaRev;  
  
Actual Result:
=============
MySql evaluates CX.StoreID IN (SELECT StoreID FROM StoreRev) as true, instead of throwing a 'INVALID SYNTAX ERROR';  
  
Expected Result:
===============
MySql should throw a 'INVALID SYNTAX ERROR' reporting that there's no 'StoreID' field in StoreRev temporary table;  
  
