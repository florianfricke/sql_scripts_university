# SQL BEFEHLE 
Verwendete SQL-Befehle für die im Microsoft-SQL-Server erstellte Test-Datenbank.
```SQL
CREATE TABLE Mitarbeiter
(
	MitID CHAR(3) NOT NULL,
	Nachname VARCHAR(20) NOT NULL,
	Vorname VARCHAR(20),
	Ort VARCHAR(30) NOT NULL,
	Gebdat DATE NOT NULL,
	Beruf VARCHAR(15) NOT NULL,
	Telnr VARCHAR(20)
	PRIMARY KEY(MitID)
)

CREATE TABLE Projekt
(
	ProNr Int Primary Key not null,
	ProName varchar(20) not null,
	ProOrt varchar(30) not null,
	ProBeschreibung varchar(30),
	ProAufwand int not null,
	ProLeiter varchar(20)
)

CREATE TABLE Zuordnung
(
	MitID char(3) Not Null,
	ProNr int Not Null,
	Istanteil float Not Null
	PRIMARY KEY(MitID, ProNr)
	FOREIGN KEY(MitID) REFERENCES Mitarbeiter(MitID),
	FOREIGN KEY(ProNr) REFERENCES Projekt(ProNr)
)

1.3
ALTER TABLE Zuordnung ADD Plananteil float NOT NULL


1.4
Diese Zeile zuerst ausführen, man brauch die Datensätze für den Fremdschlüssel
INSERT INTO Projekt (ProName, ProNr, ProOrt, ProLeiter, ProAufwand) 
SELECT Proname, ProNr, ProOrt, Leiter, Aufwand FROM depotN.dbo.quelleProjekt
INSERT INTO Mitarbeiter SELECT * FROM depotN.dbo.quelleMitarbeiter
INSERT INTO Zuordnung SELECT * FROM depotN.dbo.quelleZuordnung

1.6
WHERE Name IN('Müller','Bauer')

INSERT INTO Mitarbeiter 
(MitID, Nachname, Vorname, Ort, Gebdat, Beruf, Telnr) 
VALUES (200, 'Deutschmann','Bastian', 'Dresden', '16.04.1997','Student','112')

INSERT INTO Mitarbeiter 
(MitID, Nachname, Vorname, Ort, Gebdat, Beruf, Telnr) VALUES (201, 'Müller','Julien', 'Dresden', '16.04.1997','Praktikant','113')

1.7
Create unique Index In_Mitarbeiter On Mitarbeiter(Nachname, Vorname)
Create Index In_Mitarbeiter_Beruf On Mitarbeiter(Beruf)
Drop Index Mitarbeiter.In_Mitarbeiter_Beruf
/*einzelne Attribute eines Sek.Index löschen ist nicht möglich, nur Index komplett löschen*/
1.8
View = virtuelle Tabelle 
(muss nicht immer die einzelnen Select-anweisungen aufschreiben)
Gibt mir nur Daten aus, keine Prozedur die Daten verändern kann
Vorteile: 	abgespeichert, muss nicht jedes Mal die Select-Anweisung schreiben,
		Rechtevergabe möglich

Create View vOrtBeruf (Mitarbeiter_ID, Nachname, Vorname, Ort, Beruf) 
AS Select MitID, Nachname, Vorname, Ort, Beruf FROM Mitarbeiter 
Where Beruf <> 'Vertreter' /*View anlegen*/

Select * From vOrtBeruf /*View anzeigen*/

1.9
Create View vPaz(Mitarbeiter_ID, Nachname, Projektname, Plananteil) 
AS Select m.MitID, m.Nachname, p.Proname, z.Plananteil 
From Mitarbeiter m JOIN Zuordnung z ON m.MitID=z.MitID JOIN Projekt p ON z.ProNr = p.ProNr


STRG+UMSCHALT+R = Cache leeren, damit beim anzeigen der Name des Views nicht rot markiert wird
/*Berufe der Tabelle werden zurückgesetzt */
UPDATE Mitarbeiter SET Beruf=
(
SELECT Beruf FROM depotN.dbo.quelleMitarbeiter m2 
WHERE Mitarbeiter.MitID = m2.MitID
) WHERE MitID != '200' AND MitID != '201' AND MitID != '202'

2.1
a) Select * From Mitarbeiter WHERE Ort = 'Dresden'
b) Select * From Mitarbeiter WHERE Ort <> 'Dresden'
c) Select * From Projekt WHERE ProName Like 'R%'
d) Select * From Mitarbeiter Where MitID IN 
   (
Select MitID From Mitarbeiter Where Beruf Like 'Dipl.%' AND Ort='Radebeul'
   ) Order By Nachname
e) SELECT * FROM Zuordnung WHERE (Istanteil > 0.7) OR (Plananteil > 0.7)
f) SELECT * FROM Projekt WHERE ProLeiter is Null

2.2
a) SELECT COUNT(MitID) AS [Anzahl MA] FROM Mitarbeiter 
b) SELECT Ort, COUNT(*) AS Anz FROM Mitarbeiter Group By Ort
c) SELECT SUM(Istanteil) AS Summe From Zuordnung Where ProNr=31
d) SELECT MitId, SUM(Istanteil)-SUM(Plananteil) AS Differenz From Zuordnung Group   
   by MitID
e) SELECT MAX(ProAufwand)AS Maximum , COUNT(ProNr) AS Anzahl, SUM(ProAufwand) 
   AS Summe From Projekt

2.3
a) SELECT Nachname, Vorname, DATEDIFF(YEAR, Gebdat,getdate()) AS Alt FROM 
   Mitarbeiter
b) SELECT Nachname, Vorname, DATEDIFF(YEAR, Gebdat,getdate()) AS Alt FROM 
   Mitarbeiter WHERE DATEDIFF(YEAR, Gebdat,getdate())>30
c) SELECT Nachname, Vorname, Gebdat FROM Mitarbeiter Where MONTH(Gebdat)=5 
d) SELECT * FROM Mitarbeiter ORDER BY MONTH(Gebdat),DAY(Gebdat)
   SELECT MitID, MONTH(Gebdat) AS Monat,DAY(Gebdat) AS Tag FROM Mitarbeiter ORDER       
   BY MONTH(Gebdat),DAY(Gebdat) 
   /*spaltet das Datum und zeigt das Jahr nicht an*/
e) SELECT AVG(DATEDIFF(YEAR,Gebdat,getdate())) AS Durschnittsalter FROM  
   Mitarbeiter
f) SELECT Ort, AVG(DATEDIFF(YEAR,Gebdat,getdate())) AS Durschnittsalter FROM 
   Mitarbeiter GROUP BY Ort

2.4
a) SELECT z.MitID, m.Nachname, z.ProNr, z.Istanteil FROM Mitarbeiter m, Zuordnung   
   z WHERE z.MitID = m.MitID

   SELECT z.MitID, m.Nachname, z.ProNr, z.Istanteil FROM Mitarbeiter m JOIN     
   Zuordnung z ON z.MitID = m.MitID 

b) SELECT p.ProNr, p.ProName, m.Nachname, m.Vorname, m.Beruf FROM Projekt p,    
   Mitarbeiter m, Zuordnung z WHERE m.MitID = z.MitID AND z.ProNr = p.ProNr 
   ORDER BY p.ProNr

SELECT p.ProNr, p.ProName, m.Nachname, m.Vorname, m.Beruf FROM Zuordnung z JOIN Mitarbeiter m ON m.MitID = z.MitID JOIN Projekt p ON z.ProNr = p.ProNr ORDER BY p.ProNr


c) SELECT m.MitID, m.Nachname, p.Proname, z.Plananteil FROM Mitarbeiter m, Projekt p, Zuordnung z  WHERE m.MitID = z.MitID AND z.ProNr = p.ProNr AND ProName='Reportgenerator'

SELECT m.MitID, m.Nachname, p.Proname, z.Plananteil FROM Mitarbeiter m JOIN Zuordnung z ON m.MitID = z.MitID JOIN Projekt p ON z.ProNr = p.ProNr WHERE ProName='Reportgenerator'

d) SELECT m.MitId, m.Nachname, m.Vorname, p.ProNr, p. ProName, p.ProLeiter FROM Projekt p, Mitarbeiter m, Zuordnung z WHERE m.MitID = z.MitID AND z.ProNr = p.ProNr

SELECT m.MitId, m.Nachname, m.Vorname, p.ProNr, p. ProName, p.ProLeiter FROM Mitarbeiter m JOIN Zuordnung z ON m.MitID = z.MitID JOIN Projekt p ON z.ProNr = p.ProNr

Die Tabelle Zuordnung (Zwischenbeziehung) wird benötigt. Es gibt keine direkte Verknüpfung zwischen Projekt und Mitarbeiter

e) 
SELECT z.MitId, z.Plananteil, p.ProNr FROM Projekt p LEFT OUTER JOIN Zuordnung z ON z.ProNr = p. ProNr
-- es werden alle Projekte aufgeführt
-- alle Datensätze von links
-- auch Datensätze die in der Verknüpfung leer sind

2.5
a) SELECT * From Mitarbeiter WHERE Nachname NOT IN (SELECT ProLeiter FROM Projekt WHERE ProLeiter IS NOT NULL)

Die Werte die NULL sind müssen ausgeschlossen werden, sonst funktioniert die Abfrage nicht
Vergleich mit NULL ist unknown, nicht wahr oder falsch

b) SELECT MitID, Nachname, Vorname, DATEDIFF(YEAR, Gebdat,getdate()) AS Alt, Beruf FROM Mitarbeiter 
WHERE Beruf = 'Dipl.-Ing.' AND DATEDIFF(YEAR, Gebdat,getdate()) = 
(
SELECT MIN(DATEDIFF(YEAR, Gebdat,getdate())) FROM Mitarbeiter WHERE Beruf = 'Dipl.-Ing.'
)

Die Unterabfrage liefert das Alter des jüngsten Dipl Ing zurück --> eine zahl z.B. 22, es können mehrere 22 Jahre Alt sein, deswegen noch einmal Beruf = Dipl Ing

c)
korrelierte/ abhängige Abfrage
SELECT MitID, Plananteil, ProNr FROM Zuordnung z WHERE Plananteil IN 
(
SELECT MAX(Plananteil) FROM Zuordnung WHERE ProNr = z.ProNr
)
Die Abfrage kann nicht selbständig existieren, weil ProNr = z.ProNr

unkorrelierte/ unhängige Abfrage
SELECT z.MitID , m.Plananteil, m.ProNr  FROM
(
SELECT ProNr, MAX(Plananteil) as Plananteil FROM Zuordnung 
GROUP BY ProNr) m, 
Zuordnung z WHERE m.ProNr = z.ProNr and z.Plananteil = m.Plananteil

2.6
a) 
SELECT MitID, SUM(Istanteil) AS SummeIstanteil FROM Zuordnung
GROUP BY MitID HAVING SUM(Istanteil)>0.7

b) 
SELECT p.ProAufwand, SUM(z.Plananteil) AS SummePlananteil 
FROM Projekt p JOIN Zuordnung z ON (z.ProNr = p.ProNr)
GROUP BY z.ProNr, p.ProAufwand HAVING SUM(z.Plananteil) < p.ProAufwand 

2.7
a)
SELECT * FROM Mitarbeiter WHERE Nachname ='Fuchs' OR Nachname='Elster'
UPDATE Mitarbeiter SET Ort='Dresden' WHERE Nachname ='Fuchs' OR Nachname='Elster'

b)
UPDATE Mitarbeiter SET Beruf='Dipl.-Ing.' WHERE Nachname='Fuchs'
SELECT * FROM Mitarbeiter WHERE Nachname='Fuchs'

c) -- löschen erst in Zuordnung (da abhängige 1-Beziehung) und danach kann erst der MA gelöscht werden (n)
DELETE FROM Zuordnung WHERE MitID IN (SELECT MitID FROM Mitarbeiter m WHERE Beruf='Industriekauf.')
DELETE FROM Mitarbeiter WHERE Beruf='Industriekauf.'

d) 
DELETE FROM ZUORDNUNG WHERE MitID IN 
(
SELECT MitID AS SummePlananteil FROM Zuordnung z GROUP BY MitID HAVING SUM(Plananteil) < 0.3
)

DELETE FROM Mitarbeiter WHERE MitID='101'

2.8
e) SELECT Ort FROM Mitarbeiter EXCEPT SELECT ProOrt FROM Projekt
f) SELECT Ort FROM Mitarbeiter INTERSECT SELECT ProOrt FROM Projekt
g) SELECT Ort FROM Mitarbeiter UNION SELECT ProOrt FROM Projekt


2.9
a) SELECT * FROM Mitarbeiter m, Projekt p WHERE m.Ort = p.ProOrt 
--doppeltes Vorkommen der Spalten Ort
b) SELECT m.*, ProNr, ProName, ProBeschreibung, ProAufwand, ProLeiter FROM Mitarbeiter m, Projekt p WHERE m.Ort = p.ProOrt
--ProOrt wird nicht angezeigt, Spaltenauswahl = Projektion
c) SELECT * FROM Mitarbeiter, Projekt
--CrossJoin wäre auch möglich


2.10
SELECT MitID, Nachname,Gebdat, DATEDIFF(YEAR, Gebdat,getdate()) AS altesAlt, DATEDIFF(YEAR, Gebdat,getdate()) -
CASE 
	WHEN MONTH(Gebdat) > MONTH(getdate()) THEN 1 
--hatte noch nicht Geburtstag, Alter um 1 verringern
WHEN MONTH(Gebdat) = MONTH(getdate()) AND DAY(Gebdat) > DAY(getdate()) THEN 1 
-- es ist der aktuelle Monat
	ELSE 0 
--hatte schon Geburtstag oder heute
END
AS neuesAlt FROM Mitarbeiter

3.1
CREATE PROCEDURE Proz1
AS
SELECT m.Vorname, m.Nachname, p.ProName 
FROM Mitarbeiter m JOIN Zuordnung z ON m.MitID=z.MitID JOIN Projekt p ON p.ProNr=z.ProNr

Proz1 '35'

3.2
CREATE PROCEDURE Proz2 (@ProNr CHAR(10))
AS
SELECT m.Vorname, m.Nachname, m.Beruf, p.ProName 
FROM Mitarbeiter m JOIN Zuordnung z ON m.MitID=z.MitID JOIN Projekt p ON z.ProNr=p.ProNr
WHERE p.ProNr=@ProNr

EXEC Proz2 '35' –-Aufruf

3.3
CREATE PROCEDURE Proz3 (@anzzyklen int=0)
AS
declare @i int 
set @i=0

WHILE(@i<@anzzyklen)
BEGIN
	UPDATE MKopie SET Gebdat=DATEADD(YEAR,-1,Gebdat)

	DELETE FROM MKopie WHERE DATEDIFF(YEAR, Gebdat,getdate()) >=60

	SELECT *, DATEDIFF(YEAR, Gebdat,getdate()) AS Alt FROM MKopie
	set @i=@i+1

	if((SELECT COUNT(MitID) FROM MKopie)=0)
	BEGIN
		PRINT 'Alle Mitarbeiter in Rente'
		BREAK
	END
	WAITFOR DELAY '00:00:01'
END

--Statt SET Geht auch SELECT

3.4
ALTER PROCEDURE Proz4 (@mitid char(3))
AS

DECLARE @name varchar(20)
DECLARE @vorname varchar(20)
DECLARE @beruf varchar(15)
DECLARE @projektbez varchar(20)
DECLARE @plananteil float
DECLARE @istanteil float
DECLARE @abweichung float
DECLARE @message1 char(500)
DECLARE @message2 char(500)
DECLARE @summeplan float
set @summeplan = 0
DECLARE @summeist float
set @summeist = 0

DECLARE mitarbeiter CURSOR FOR 
	SELECT m.Nachname, m.Vorname, m.Beruf, p.ProName, z.Plananteil, z.Istanteil 
FROM Mitarbeiter m JOIN Zuordnung z ON m.MitID=z.MitID JOIN Projekt p ON p.ProNr = z.ProNr
	WHERE m.MitID=@mitid

OPEN mitarbeiter --laden der Datensätze in den Cursor, habe eine Menge

FETCH mitarbeiter INTO --Zeilen iterieren
	@name, @vorname, @beruf, @projektbez, @plananteil, @istanteil
	
IF(@@FETCH_STATUS<0)
BEGIN
	print 'Es gibt keine Mitarbeiter im Unternehmen'
	CLOSE mitarbiter
	DEALLOCATE mitarbeiter
	return
END

ELSE
BEGIN
	WHILE(@@FETCH_STATUS=0)
	BEGIN
		--Ausgabe2
		set @abweichung=@plananteil-@istanteil
		--planateil und istanteil sind float werte
set @message2= 'Projekt: '+@projektbez + 'Plananteil: ' + CONVERT(char(29),@plananteil) + 'Istanteil: ' + CONVERT(char(29),@istanteil) + 'Abweichung: ' + CONVERT(char(29),@abweichung)
		print @message2
		

		FETCH mitarbeiter INTO 
		@name, @vorname, @beruf, @projektbez, @plananteil, @istanteil
		
set @summeplan=@summeplan+@plananteil
		set @summeist=@summeist+@istanteil
	END

	--Ausgabe1
set @message1= 'Name: ' + @name + ' Vorname: ' + @vorname + ' Beruf: ' + @beruf
	print @message1
	
IF EXISTS(SELECT p.Pronr FROM Projekt p JOIN Zuordnung z ON z.ProNr = p.Pronr JOIN Mitarbeiter m ON m.MitID=z.MitID WHERE p.ProNr='')
	BEGIN
		print 'Der Mitarbeiter hat kein Projekt'
	END
	
	--Ausgabe3
	--(1)
	IF(@summeplan<0.5)
	BEGIN
		print'Mitarbeiter '+ @name +' ist ueberfordert!'
	END
	
	IF((@summeplan<1))
	BEGIN
		--(2)
		IF(@summeplan-@summeist = 0)
		BEGIN
			print'Mitarbeiter '+ @name +' ist optimal ausgelastet!'
		END

		--(3)
		IF((@summeplan-@summeist < 0) or (@summeist > 1))
		BEGIN
			print'Mitarbeiter '+ @name +' ist ueberlastet!'
		END

		IF((@summeplan-@summeist > 0) or (@summeist < 1))
		BEGIN
			print'Mitarbeiter '+ @name +' hat noch Reserven!'
		END
	END
	
	CLOSE mitarbeiter
	DEALLOCATE mitarbeiter
	return
END

EXEC Proz4 112
