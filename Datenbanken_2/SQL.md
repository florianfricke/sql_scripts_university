# SQL BEFEHLE 
Verwendete SQL-Befehle für die im Microsoft-SQL-Server erstellte Test-Datenbank.

```SQL
2.1. ALTER TABLE Projekt ADD LeiterNr varchar(30)
2.2 UPDATE Projekt SET LeiterNr = (SELECT MitID FROM Mitarbeiter WHERE Nachname = ProLeiter) WHERE ProLeiter is not Null
--korrelierte Abfrage (nicht eigenständig)
ALTER TABLE Projekt ALTER COLUMN LeiterNr char(3) -- Datentyp ändern
ALTER TABLE Projekt ADD FOREIGN KEY (LeiterNr) REFERENCES Mitarbeiter(MitID) 
--FS hinzufügen

2.3
UPDATE Projekt SET LeiterNr = (SELECT MitID FROM Mitarbeiter WHERE MitID='130'), ProLeiter = (SELECT Nachname FROM Mitarbeiter WHERE MitID='130') WHERE ProLeiter= 'Igel'

2.4
ALTER TABLE Projekt DROP COLUMN ProLeiter -- Spalte löschen

3.1
INSERT INTO Mitarbeiter VALUES ('210','Fricke','Florian','Dresden','2016-02-25','Student','0351888888')

3.2
INSERT INTO Projekt VALUES('46','MS-SQL-Prakt','Dresden','test','3','210')

3.3
INSERT INTO Zuordnung VALUES('210','46',0.5,1)

3.4
INSERT INTO Zuordnung VALUES('210','38',0.5,0)

3.5
INSERT INTO Zuordnung VALUES('106','35',0.2,0.1)

3.6
INSERT INTO Projekt VALUES('47','Oracle-Prakt','Dresden','test','0', NULL)

ALTER PROCEDURE Proz4b (@ProNr int)
AS

SELECT p.ProNr, p.ProName, p.ProAufwand, m.MitID, m.Nachname, m.Vorname FROM Projekt p JOIN Mitarbeiter m ON p.LeiterNr = m.MitID WHERE p.ProNr=@ProNr

DECLARE @mitid varchar(3)
DECLARE @name varchar(20)
DECLARE @vorname varchar(20)
DECLARE @beruf varchar(15)
DECLARE @proname varchar(30)
DECLARE @proaufwand varchar(30)
DECLARE @plananteil float
DECLARE @istanteil float
DECLARE @abweichung float
DECLARE @message char(500)

DECLARE mitarbeiter CURSOR FOR 
	SELECT m.MitID, m.Nachname, m.Vorname, m.Beruf, p.ProNr, p.ProName, p.ProAufwand, z.Plananteil, z.Istanteil 
	FROM Mitarbeiter m JOIN Zuordnung z ON m.MitID = z.MitID JOIN Projekt p ON z.ProNr = p.ProNr  
	WHERE p.ProNr=@ProNr

OPEN mitarbeiter --lade ich die Datensätze in Cursor, habe eine Menge

FETCH mitarbeiter INTO --Zeilen durchgehen
	@mitid, @name, @vorname, @beruf, @ProNr, @proname, @proaufwand, @plananteil, @istanteil
	
IF(@@FETCH_STATUS<0)
BEGIN
	IF EXISTS(SELECT ProNr FROM Projekt EXCEPT SELECT ProNr FROM Zuordnung)
	BEGIN
		print 'Das Projekt hat keinen Mitarbeiter'
	END
	ELSE
	BEGIN
		print 'Das Projekt gibt es nicht'
	END
	CLOSE mitarbeiter
	DEALLOCATE mitarbeiter
	return
END

ELSE
BEGIN
	WHILE(@@FETCH_STATUS=0)
	BEGIN
		set @abweichung=@plananteil-@istanteil
		set @message= 'Nachname: '+@name + ' Vorname: '+@vorname + ' Beruf: '+@beruf + ' Projektnr: '+CONVERT(char(3),@ProNr) + ' Projektname: '+@proname + ' Projektaufwand: '+@proaufwand +' Plananteil: ' + CONVERT(char(3),@plananteil) + ' Istanteil: ' + CONVERT(char(3),@istanteil) + ' Abweichung: ' + CONVERT(char(8),@abweichung)
		print @message
		
		FETCH mitarbeiter INTO 
		@mitid, @name, @vorname, @beruf, @ProNr, @proname, @proaufwand, @plananteil, @istanteil
	END
	
	CLOSE mitarbeiter
	DEALLOCATE mitarbeiter
	return
END

EXEC Proz4b 36


5 Abfragen
5.1. 
SELECT m.Nachname, m.Vorname, m.Ort, p.ProName FROM Mitarbeiter m, Projekt p WHERE m.MitID = p.LeiterNr
SELECT m.Nachname, m.Vorname, m.Ort, p.ProName FROM Mitarbeiter m JOIN Projekt p ON m.MitID = p.LeiterNr

5.2
SELECT p.ProNr, z.Istanteil, m.* FROM Mitarbeiter m, Zuordnung z, Projekt p WHERE m.MitID = p.LeiterNr AND z.MitID = m.MitID; 
SELECT p.ProNr, z.Istanteil, m.* FROM Mitarbeiter m JOIN Projekt p  ON m.MitID = p.LeiterNr JOIN Zuordnung z ON z.MitID = m.MitID; 

5.3
SELECT m.Nachname, m.Vorname, m.Ort, z.Istanteil, p.* FROM Mitarbeiter m, Zuordnung z, Projekt p WHERE m.MitID = p.LeiterNr AND z.MitID = m.MitID AND (p.ProAufwand >= 3)
SELECT m.Nachname, m.Vorname, m.Ort, z.Istanteil, p.* FROM Mitarbeiter m JOIN Projekt p ON m.MitID = p.LeiterNr JOIN Zuordnung z ON z.MitID = m.MitID WHERE (p.ProAufwand >= 3); 

5.4
SELECT p.ProNr, p.ProName, SUM(z.Istanteil) AS SUMME_Ist FROM Zuordnung z RIGHT JOIN Projekt p ON z.ProNr = p.ProNr GROUP BY p.ProNr, p.ProName
--Immer wenn ich eine Aggregatsfunktion verwende, muss ich die restlichen Spalten gruppieren

SELECT p.ProNr, p.ProName, SUM(z.Istanteil) AS Gesamt_Istanteil FROM 
Projekt p JOIN Zuordnung z ON p.ProNr = z.ProNr GROUP BY p.ProNr,
p.ProName
UNION
SELECT p.ProNr, p.ProName, 0 AS Gesamt_Itanteil FROM Projekt p WHERE
ProNr NOT IN (SELECT ProNr FROM Zuordnung)
--Auswahl der ProNr aus der Tabelle Projekt, welche nicht Zuordnung vorkommt
--0 ist der konstante Dummy/ Default Wert -- leere Spalte (Projekt wird nicht bearbeiet)

5.5
SELECT m.MitID, m.Nachname, 1-SUM(z.Plananteil) AS Gesamt_Plananteil FROM 
Mitarbeiter m JOIN Zuordnung z ON m.MitId = z.MitID GROUP BY m.MitID, m.Nachname
UNION
SELECT MitID, Nachname, 1 AS Gesamt_Plananteil FROM Mitarbeiter WHERE
MitID NOT IN (SELECT MitID FROM Zuordnung)


SQL Aufgabekomplex 3

1.1. Skalarwerfkt
CREATE FUNCTION AlterErmitteln (@Datum date)
RETURNS INT
AS
	BEGIN
		DECLARE @Alter int
		SET @Alter = DATEDIFF(YEAR, @Datum,getdate()) 
		RETURN @Alter
	END

SELECT *, dbo.AlterErmitteln(Gebdat) AS Alter_MA FROM Mitarbeiter 

1.2 Tabellenwertfkt
CREATE FUNCTION ErmittleMitarbeiterDaten(@schwellenwert float)
RETURNS @table TABLE (
	MitID char(3),
	Auslastung float 
)

AS
BEGIN
	INSERT INTO @table
		SELECT MitID, SUM(Istanteil) FROM Zuordnung 
		GROUP BY MitID 
		HAVING SUM(Istanteil) > @schwellenwert
	RETURN
END

SELECT * FROM dbo.ErmittleMitarbeiterDaten(0.9)

2.1 DEFAULTS/ CHECK
d) ALTER TABLE Mitarbeiter ADD CONSTRAINT DF_Mitarbeiter_Ort DEFAULT 'Dresden' FOR Ort
e) ALTER TABLE Mitarbeiter ADD CONSTRAINT DF_Mitarbeiter_Beruf DEFAULT 'Dipl.-Ing' FOR Beruf
f) ALTER TABLE Mitarbeiter ADD CONSTRAINT CK_Mitarbeiter_Alter 
CHECK (dbo.AlterErmitteln > 18 AND dbo.AlterErmitteln < 60)
g) ALTER TABLE Mitarbeiter ADD CONSTRAINT CK_Mitarbeiter_MiID
CHECK (CONVERT(INT, MitID) BETWEEN 100 AND 999)

TEST: INSERT INTO Mitarbeiter 
(MitID, Nachname, Vorname, Gebdat, Telnr) 
VALUES ('21a', 'Georg','Maurer','16.04.1999','112')

2.2
ALTER TABLE Zuordnung ADD CONSTRAINT CK_Zuordnung_Istanteil
CHECK(Istanteil <= 1.0)


TEST: INSERT INTO Zuordnung
(MitID, ProNr, Istanteil, Plananteil) 
VALUES (211, 34,12,0.2)

3.1 Referentielle Integrität
Der Fall, wenn der FS schon gesetzt ist:
ALTER TABLE Projekt DROP CONSTRAINT FK__Projekt__LeiterN__36B12243 
Löschen des schon vorhandenen Schlüsselnamens (vom System vergeben)

ALTER TABLE Projekt ADD CONSTRAINT FK_ProLeiter
FOREIGN KEY(LeiterNr) 
REFERENCES Mitarbeiter (MitID) ON DELETE SET NULL
Neu Anlegen eines Constraints

h)
INSERT INTO Mitarbeiter 
(MitID, Nachname, Vorname, Gebdat, Telnr) 
VALUES ('212', 'Gregor','Bauer','16.04.1994','112')

INSERT INTO Projekt
(ProNr, ProName, ProOrt, ProBeschreibung, ProAufwand, LeiterNr) 
VALUES ('48', 'Mauern','Dresden','test','5','212')

UPDATE Mitarbeiter SET MitID = 250 WHERE MitID = 212
Nicht möglich

i) DELETE Mitarbeiter WHERE MitID = 212 
Löschen war möglich, in Projekt wird die LeiterNr = NULL aufgrund Contstraint SET NULL

j) UPDATE Projekt SET LeiterNr=211 WHERE ProNr=48
DELETE Mitarbeiter WHERE MitID = 211 (passiert das gleiche wie bei i)

k) UPDATE Projekt SET LeiterNr=700 WHERE ProNr=47 (nicht möglich)

l) UPDATE Projekt SET LeiterNr=NULL WHERE ProNr=48

4.1
ALTER TRIGGER trigger_plananteil ON Zuordnung
FOR UPDATE, INSERT AS
IF UPDATE(Plananteil)
BEGIN

DECLARE @plananteil_neu float

SET @plananteil_neu = (SELECT SUM(Plananteil) FROM Zuordnung GROUP BY MitID HAVING MitID = (SELECT MitID FROM INSERTED))

	IF @plananteil_neu > 1.0
	BEGIN
		ROLLBACK TRANSACTION
		PRINT 'Fehler: Plananteil ist groesser 1'
	END
	
	ELSE
		PRINT 'Eingefuegt'
END
ALTER TRIGGER trigger_plananteil ON Zuordnung
FOR UPDATE, INSERT AS
IF UPDATE(Plananteil)
BEGIN
	DECLARE @summeplan float

	DECLARE plananteil CURSOR FOR 
	SELECT SUM(Plananteil) FROM Zuordnung WHERE MitID IN (SELECT MitID FROM INSERTED) GROUP BY MitID 

	OPEN plananteil
	FETCH plananteil INTO @summeplan
	
	IF(@@FETCH_STATUS<0)
	BEGIN
		CLOSE plananteil
		DEALLOCATE plananteil
		return
	END

	ELSE
	BEGIN
		WHILE(@@FETCH_STATUS=0)
		BEGIN
			IF(@summeplan > 1.0)
			BEGIN
				ROLLBACK TRANSACTION
				PRINT 'Fehler1: Plananteil ist groesser 1'
			END
		FETCH plananteil INTO @summeplan
		END
	END
	CLOSE plananteil
	DEALLOCATE plananteil
END


SELECT * FROM Mitarbeiter
SELECT * FROM Zuordnung ORDER BY ProNr

UPDATE Zuordnung SET Plananteil = 0.2 WHERE MitID=105
UPDATE Zuordnung SET Plananteil = Plananteil + 0.3 WHERE ProNr=31
INSERT INTO Zuordnung(MitID, ProNr, Istanteil, Plananteil) VALUES ('211','44',0.5,1.2)
UPDATE Zuordnung SET Plananteil = Plananteil + 0.4 WHERE MitID=105

Testen
BEGIN TRANSACTION
SELECT m.*,z.* FROM Mitarbeiter m JOIN Zuordnung z ON m.MitID=z.MitID WHERE z.ProNr=31
UPDATE Zuordnung SET Plananteil = Plananteil + 0.3 WHERE ProNr=31
SELECT m.*,z.* FROM Mitarbeiter m JOIN Zuordnung z ON m.MitID=z.MitID WHERE z.ProNr=31
ROLLBACK TRANSACTION

BEGIN TRANSACTION
SELECT m.*,z.* FROM Mitarbeiter m JOIN Zuordnung z ON m.MitID=z.MitID WHERE z.MitID=105
UPDATE Zuordnung SET Plananteil = Plananteil + 0.4 WHERE MitID=105
SELECT m.*,z.* FROM Mitarbeiter m JOIN Zuordnung z ON m.MitID=z.MitID WHERE z.MitID=105
ROLLBACK TRANSACTION



IF EXISTS(SELECT MitID, COUNT(MitID) FROM INSERTED GROUP BY MitID HAVING COUNT(MitID)=0)
--Mengenoperation: =0 nicht möglich, IN muss verwendet werden



4.2
ALTER TRIGGER trigger_zuordnung_pro ON Zuordnung
FOR DELETE, UPDATE AS

IF EXISTS(SELECT ProNr FROM Zuordnung WHERE ProNr IN (SELECT ProNr FROM DELETED))
	--testet ob die Projektnummer aus der alten Tabelle(DELETED) noch in der Zuordnung vorhanden ist
	PRINT 'Projekt noch in Bearbeitung'
ELSE
BEGIN
	PRINT 'Projekt ist erledigt'
	UPDATE Projekt SET ProBeschreibung='erledigt' WHERE ProNr IN (SELECT ProNr FROM DELETED)
END

INSERT INTO Zuordnung (MitID,ProNr,Istanteil,Plananteil) VALUES (104,32,0.2,0.1)
DELETE FROM Zuordnung WHERE (ProNr=32 AND MitID=104)
Update Zuordnung SET ProNr=38 WHERE (ProNr=32 AND MitID=104)
Update Zuordnung SET ProNr=31 WHERE (ProNr=32 AND MitID=109)
Update Projekt SET ProBeschreibung= NULL WHERE ProBeschreibung='erledigt'

SELECT * FROM Projekt
SELECT * FROM  Zuordnung ORDER BY ProNr
Test
BEGIN TRANSACTION
SELECT * FROM  Zuordnung WHERE ProNr=36
DELETE FROM Zuordnung WHERE MitID=104
DELETE FROM Zuordnung WHERE MitID=106
DELETE FROM Zuordnung WHERE MitID=107

Update Zuordnung SET ProNr=35 WHERE MitID IN(SELECT MitID FROM Zuordnung WHERE ProNr=37)
ROLLBACK TRANSACTION

5.1

CREATE TABLE Bprotokoll (
	MitID char(3),
	Nutzer char(16),
	Zeit datetime,
	Beruf_alt char(15),
	Beruf_neu char(15)
)

ALTER TRIGGER beruf_aenderung ON Mitarbeiter
FOR UPDATE AS
IF UPDATE (Beruf)
BEGIN
	DECLARE @mitid char(3)
	DECLARE @beruf_alt varchar(15)
	DECLARE @beruf_neu varchar(15)

	SET @mitid = (SELECT MitID FROM DELETED)
	SET @beruf_alt = (SELECT Beruf FROM DELETED)
	SET @beruf_neu = (SELECT Beruf FROM INSERTED)

	INSERT INTO Bprotokoll VALUES (@mitid, user_name(), getdate(), @beruf_alt, @beruf_neu)
END
--------------------------------------
SELECT * FROM Bprotokoll
SELECT * FROM Mitarbeiter

UPDATE Mitarbeiter SET Beruf='Musiker' WHERE Ort='Disneyland'

ALTER TRIGGER beruf_aenderung ON Mitarbeiter
FOR UPDATE AS
IF UPDATE (Beruf)
BEGIN
	
	INSERT INTO Bprotokoll
	SELECT d.MitID, user_name(), getdate(), d.Beruf, i.Beruf, d.BprotokollID+1
	FROM DELETED d JOIN INSERTED i ON d.MitID = i.MitID
END


5.5
CREATE TRIGGER mitarbeiter_loeschen ON Mitarbeiter
FOR DELETE AS
BEGIN
	DELETE FROM Bprotokoll WHERE MitID IN (SELECT MitID FROM DELETED)
END

BEGIN TRANSACTION
DELETE FROM Zuordnung WHERE MitID IN (SELECT MitID FROM Mitarbeiter WHERE Beruf='Musiker')
DELETE FROM Mitarbeiter WHERE Beruf='Musiker'
SELECT * FROM Bprotokoll
SELECT * FROM Mitarbeiter WHERE Beruf='Musiker'
ROLLBACK TRANSACTION

SELECT * FROM Bprotokoll
SELECT * FROM Mitarbeiter
5.6
ALTER TABLE Bprotokoll ADD BprotokollID INT IDENTITY(1,1) NOT NULL
ALTER TABLE Bprotokoll ADD PRIMARY KEY (BprotokollID)
--IDENTITY ist eine Zählervariable (1 Startzahl, 1 Inkrement)
	

SELECT * INTO Zuordnungkopie FROM Zuordnung

BEGIN TRANSACTION -- zu erst ausführen, dann die einzelnen Anweisungen
DELETE FROM ZKopie 
SELECT * FROM ZKopie
ROLLBACK --beendet auch eine Transaktion
SELECT * FROM ZKopie
COMMIT -- nicht notwendig, Rollback beendet schon Transaction

/*Alle oder gar keine der Befehle in einer Transaction werden ausgeführt
Transaktion können nicht auf andere Transaktionen zugreifen*/
SCHEMA: User erhält Rechte für ein Teil der DB 

1.	Nicht möglich
2.	CREATE USER georg FOR LOGIN [smb\s74014] WITH DEFAULT_SCHEMA = extern
sp_helpuser --zeigt die Nutzer an/ Test ob georg existiert
3.	CREATE SCHEMA extern AUTHORIZATION georg 
--nicht notwendig, beim Anlegen des Users, wird das Schema mit erstellt
4.	USE iw15s74003
5.	Nicht möglich (Schema verweigert)
6.	GRANT SELECT ON Mitarbeiter TO georg
7.	Ist möglich
8.	sp_helpuser --zeigt die Nutzer an
sp_helprotect Mitarbeiter --zeigt an wer die Rechte auf ein Objekt (Tabelle Mitarbeiter) hat

9.	BEGIN TRANSACTION
SELECT * FROM Mitarbeiter WITH (HOLDLOCK) 
–-die Tabelle wird solange gesperrt, bis ein commit von dem user(georg) erfolgt

10.	UPDATE Mitarbeiter SET Ort='Glauchau' WHERE MitID=211 
–-der Befehl wartet solange/macht nichts, bis der commit erfolgt
11.	sp_lock 
sp_who 
12.	COMMIT
13.	GRANT CREATE TABLE TO georg
GRANT ALTER ON SCHEMA :: extern TO georg


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
--georg darf aber noch nicht insert, update, select auf die erstellte Tabelle ausführen
--er darf nur eine Tabelle anlegen und wieder löschen


14.	GRANT INSERT ON extern.MitarbeiterGeorg TO georg
15.	SELECT * FROM extern.MitarbeiterGeorg
16.	DROP TABLE MitarbeiterGeorg
17.	REVOKE CREATE TABLE FROM georg sp_helprotect
REVOKE SELECT ON Mitarbeiter FROM georg sp_helprotect
DROP USER georg sp_helpuser



