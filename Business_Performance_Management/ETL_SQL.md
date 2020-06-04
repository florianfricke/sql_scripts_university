# ETL Prozedur
ETL Prozedur die csv Daten in das Data Warehouse lädt.
```SQL
CREATE TABLE BelegeTMP2 (
	Bon_ID VARCHAR(4),
	Fil_ID VARCHAR(5),
	Datum DATE,
	Prod_ID VARCHAR(4),
	Preis float,
	Anzahl int
)

CREATE TABLE UmsatzdatenTMP2 (
	Mon_ID VARCHAR(6),
	Land_ID VARCHAR(2),
	Produkt_ID VARCHAR(4),
	Mitarbeiter_ID VARCHAR(2),
	Umsatzbetrag MONEY,
	Umsatzmenge INTEGER
)

CREATE TABLE UmsatzdatenTMP_ERROR2 (
	Mon_ID VARCHAR(6),
	Land_ID VARCHAR(2),
	Produkt_ID VARCHAR(4),
	Mitarbeiter_ID VARCHAR(2),
	Umsatzbetrag MONEY,
	Umsatzmenge INTEGER
)

CREATE TABLE Umsatzdaten2 (
	Mon_ID VARCHAR(6),
	Land_ID VARCHAR(2),
	Produkt_ID VARCHAR(4),
	Mitarbeiter_ID VARCHAR(2),
	Umsatzbetrag MONEY,
	Umsatzmenge INTEGER
	PRIMARY KEY (Mon_ID,Land_ID,Produkt_ID,Mitarbeiter_ID),
	FOREIGN KEY (Mon_ID) REFERENCES Zeit(Mon_ID),
	FOREIGN KEY (Land_ID) REFERENCES Geografie(Land_ID),
	FOREIGN KEY (Produkt_ID) REFERENCES Produkt(Produkt_ID),
	FOREIGN KEY (Mitarbeiter_ID) REFERENCES Mitarbeiter(Mitarbeiter_ID)
)

CREATE TABLE BelegeTMP3 (
	Bon_ID VARCHAR(4),
	Fil_ID VARCHAR(5),
	Datum DATE,
	Prod_ID VARCHAR(4),
	Preis float,
	Anzahl int
)

CREATE TABLE UmsatzdatenTMP3 (
	Mon_ID VARCHAR(6),
	Land_ID VARCHAR(2),
	Produkt_ID VARCHAR(4),
	Mitarbeiter_ID VARCHAR(2),
	Umsatzbetrag MONEY,
	Umsatzmenge INTEGER
)

CREATE TABLE Umsatzdaten3 (
	Mon_ID VARCHAR(6),
	Land_ID VARCHAR(2),
	Produkt_ID VARCHAR(4),
	Mitarbeiter_ID VARCHAR(2),
	Umsatzbetrag MONEY,
	Umsatzmenge INTEGER
	PRIMARY KEY (Mon_ID,Land_ID,Produkt_ID,Mitarbeiter_ID),
	FOREIGN KEY (Mon_ID) REFERENCES Zeit(Mon_ID),
	FOREIGN KEY (Land_ID) REFERENCES Geografie(Land_ID),
	FOREIGN KEY (Produkt_ID) REFERENCES Produkt(Produkt_ID),
	FOREIGN KEY (Mitarbeiter_ID) REFERENCES Mitarbeiter(Mitarbeiter_ID)
)

--BULK INSERT BelegeTMP FROM 'T:\Temp\Belege_2016_4.csv' WITH (FIELDTERMINATOR = ',', FIRSTROW = 2)
--firstrow, ab wo er einlesen soll, FIELDTERMINATOR = Feldtrennerzeichen

CREATE PROCEDURE ETL 
	@path nvarchar(100), 
	@fieldterminator varchar(3),
	@firstrow int
	AS 
	DECLARE @sql_statement varchar(max)

	--EXTRACT
	select @sql_statement='BULK INSERT BelegeTMP3 FROM ''';
	select @sql_statement=@sql_statement + @path;
	select @sql_statement=@sql_statement + ''' with (FIELDTERMINATOR = ''';
	select @sql_statement=@sql_statement + @fieldterminator;
	select @sql_statement=@sql_statement + ''',  ROWTERMINATOR = ''\n'' , FIRSTROW =';
	select @sql_statement=@sql_statement + CONVERT(char(4),@firstrow);
	select @sql_statement=@sql_statement + ')';

	PRINT(@sql_statement)
	exec(@sql_statement)

	--TRANSFORM
	INSERT INTO UmsatzdatenTMP3 (Mon_ID,Land_ID,Produkt_ID,Umsatzbetrag,Umsatzmenge)
	SELECT 
	CASE
		WHEN (MONTH(Datum) < 10) THEN CONCAT (YEAR(Datum),'0', MONTH(Datum)) --concat: String anfuegen und ausgeben
		ELSE CONCAT (YEAR(Datum), MONTH(Datum))
	END,
	LEFT(Fil_ID,2), --left: abspalten der ersten 2 Zeichen
	Prod_ID,
	SUM(Preis*Anzahl),
	SUM(Anzahl)
	FROM BelegeTMP3
	GROUP BY --Alternative: GroupBy beim laden von UmsatzTMP in Umsatz, aber Performance schlechter (siehe bei (1))
	CASE
		WHEN (MONTH(Datum) < 10) THEN CONCAT (YEAR(Datum),'0', MONTH(Datum)) --concat: String anfuegen und ausgeben
		ELSE CONCAT (YEAR(Datum), MONTH(Datum))
	END,
	LEFT(Fil_ID,2), --left: abspalten der ersten 2 Zeichen fuer die LandID
	Prod_ID
	--GROUP BY Mon_ID, Land_ID, Produkt_ID

	--Testen, ob eine neuer DS aus den Quelldaten in den Dimensionstabellen noch nicht eingepflegt wurde (bspw. neues Produkte im Sortiment) -> luest FS-Fehler aus
	IF EXISTS(SELECT Land_ID FROM UmsatzdatenTMP3 EXCEPT SELECT Land_ID FROM Geografie) 
	BEGIN
		print 'Die LandID aus den Quelldaten ist in der Dimensionstabelle Geographie nocht nicht vorhanden.' --Protokolltabelle
		DELETE FROM BelegeTMP3
		DELETE FROM UmsatzdatenTMP3
		return --Procedur wird vorzeitig beendet
	END

	IF EXISTS(SELECT Produkt_ID FROM UmsatzdatenTMP3 EXCEPT SELECT Produkt_ID FROM Produkt) -- pruefen, was auf der linken Seite auf der rechten Seite vorhanden ist
	BEGIN
		print 'Die Produkt_ID aus den Quelldaten ist in der Dimensionstabelle Produkt noch nicht vorhanden.'
		DELETE FROM BelegeTMP3
		DELETE FROM UmsatzdatenTMP3
		return --Procedur wird vorzeitig beendet
	END
	
	--ADD MA_ID TO PRODUCT_ID
	UPDATE UmsatzdatenTMP3 SET Mitarbeiter_ID = ps.Mitarbeiter_ID
	FROM Produkt p, UmsatzdatenTMP3 u, Produktsubkategorie ps WHERE p.Subkategorie_ID = ps.Subkategorie_ID AND p.Produkt_ID = u.Produkt_ID

	--LOAD (1)
	INSERT INTO Umsatzdaten3
	--SELECT Mon_ID, Land_ID, Produkt_ID, Mitarbeiter_ID, SUM(Umsatzbetrag), SUM(Umsatzmenge) 
	SELECT Mon_ID, Land_ID, Produkt_ID, Mitarbeiter_ID, Umsatzbetrag, Umsatzmenge 
	FROM UmsatzdatenTMP3 
	--GROUP BY Mon_ID, Land_ID, Produkt_ID, Mitarbeiter_ID

	--CLEAN TMP
	DELETE FROM BelegeTMP3
	DELETE FROM UmsatzdatenTMP3

EXEC ETL @path = 'T:\Belege_2015_1.csv', @fieldterminator = ',', @firstrow = 2;

--check if duplicates are in fakttabelle
SELECT Mon_ID, Land_ID, Produkt_ID, Mitarbeiter_ID, Umsatzbetrag, Umsatzmenge, COUNT(*)
FROM Umsatzdaten2
GROUP BY Mon_ID, Land_ID, Produkt_ID, Mitarbeiter_ID, Umsatzbetrag, Umsatzmenge
HAVING COUNT(*) > 1

--SELECT COUNT(*) FROM BelegeTMP2
--SELECT COUNT(*) FROM BelegeTMP3
--SELECT COUNT(*) FROM UmsatzdatenTMP2
--SELECT COUNT(*) FROM UmsatzdatenTMP3
SELECT Count(*) FROM Umsatzdaten3
DELETE FROM Umsatzdaten3


DELETE FROM UmsatzdatenTMP2
SELECT Count(*) FROM BelegeTMP2
SELECT * FROM BelegeTMP
SELECT Count(*) FROM UmsatzdatenTMP2
DELETE FROM Umsatzdaten2
SELECT * FROm Produktsubkategorie
SELECT * FROM UmsatzdatenTMP2
SELECT SUM(umsatzbetrag) AS Jahresumsatz FROM Umsatzdaten2
SELECT Count(*) FROM Umsatzdaten2
SELECT SUM(umsatzbetrag) AS Jahresumsatz FROM Umsatzdaten
SELECT * FROM Umsatzdaten2


INSERT INTO UmsatzdatenTMP3 VALUES('201910', '99', '1001', '18', 126.04, 89)
SELECT * FROM UmsatzdatenTMP3 
SELECT Land_ID FROM Geografie EXCEPT SELECT Land_ID FROM UmsatzdatenTMP3 --Ausgabe alle Zeilen, die beide nicht gemeinsam haben
SELECT Land_ID FROM Geografie INTERSECT SELECT Land_ID FROM UmsatzdatenTMP3 --Ausgabe alle Zeilen, die beide Tabellen in der LandID gemeinsam haben
SELECT * FROM Geografie
INSERT INTO Geografie VALUES('47','Ritterburg','Nord','Samoa') 

DELETE FROM BelegeTMP2
DELETE FROM UmsatzdatenTMP2
DELETE FROM Umsatzdaten2
DELETE FROM UmsatzdatenTMP_ERROR2

SELECT * FROM Umsatzdaten2 WHERE Produkt_ID = 8888
SELECT * FROM UmsatzdatenTMP_ERROR2

INSERT INTO Geografie VALUES('47','Ritterburg','Nord','Samoa') 
SELECT * FROM UmsatzdatenTMP3
