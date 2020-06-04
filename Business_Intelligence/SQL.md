# SQL BEFEHLE 
Verwendete SQL-Befehle für die Erstellung einer Data Warehouse Datenbank und Abfrage von Daten im Microsoft-SQL-Server.
```SQL
CREATE DATABASE Umsatz
USE Umsatz
ALTER DATABASE Umsatz SET RECOVERY SIMPLE
-- es wird mit dem Befehl kein log Datei angelegt

CREATE TABLE Geografie (
	Land_ID VARCHAR(2),
	Bundesland VARCHAR(25),
	Region VARCHAR(25),
	Staat VARCHAR(25),
	PRIMARY KEY (Land_ID)
)

CREATE TABLE Mitarbeiter (
	Mitarbeiter_ID VARCHAR(2) PRIMARY KEY,
	Name VARCHAR(25)
)

CREATE TABLE Produktkategorie (
	Kategorie_ID VARCHAR(2) PRIMARY KEY,
	Kategorie VARCHAR(25),
	Kategorie_Manager VARCHAR(25)
)

CREATE TABLE Produktsubkategorie (
	Subkategorie_ID VARCHAR(3) PRIMARY KEY,
	Subkategorie VARCHAR(25),
	Subkategorie_Manager VARCHAR(25),
	Kategorie_ID VARCHAR(2),
	FOREIGN KEY (Kategorie_ID) REFERENCES Produktkategorie(Kategorie_ID)
)

CREATE TABLE Produkt (
	Produkt_ID VARCHAR(4) PRIMARY KEY,
	Markenname VARCHAR(25),
	Produktname VARCHAR(40),
	Preis MONEY,
	Subkategorie_ID VARCHAR(3),
	FOREIGN KEY (Subkategorie_ID) REFERENCES Produktsubkategorie(Subkategorie_ID)
)

CREATE TABLE Zeit (
	Mon_ID VARCHAR(6) PRIMARY KEY,
	Monatsnamen VARCHAR(20),
	Q_ID VARCHAR(6),
	Quartal VARCHAR(10),
	Jahr VARCHAR(4)
)

CREATE TABLE Umsatzdaten (
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

--inserts geografie
BEGIN
INSERT INTO Geografie VALUES ('01','Sachsen','Ost','Deutschland')
INSERT INTO Geografie VALUES ('02','Bayern','Sued','Deutschland')
INSERT INTO Geografie VALUES ('03','Saarland','West','Deutschland')
INSERT INTO Geografie VALUES ('04','Nordrhein-Westfalen','West','Deutschland')
INSERT INTO Geografie VALUES ('05','Baden-Wuerttemberg','Sued','Deutschland')
INSERT INTO Geografie VALUES ('06','Rheinland-Pfalz','West','Deutschland')
INSERT INTO Geografie VALUES ('07','Niedersachsen','West','Deutschland')
INSERT INTO Geografie VALUES ('08','Schleswig-Holstein','Nord','Deutschland')
INSERT INTO Geografie VALUES ('09','Hamburg','Nord','Deutschland')
INSERT INTO Geografie VALUES ('10','Bremen','Nord','Deutschland')
INSERT INTO Geografie VALUES ('11','Mecklenburg-Vorpommern','Nord','Deutschland')
INSERT INTO Geografie VALUES ('12','Brandenburg','Ost','Deutschland')
INSERT INTO Geografie VALUES ('13','Berlin','Ost','Deutschland')
INSERT INTO Geografie VALUES ('14','Sachsen-Anhalt','Ost','Deutschland')
INSERT INTO Geografie VALUES ('15','Thueringen','Ost','Deutschland')
INSERT INTO Geografie VALUES ('16','Hessen','West','Deutschland')
INSERT INTO Geografie VALUES ('17','Valais','Sued','Schweiz')
INSERT INTO Geografie VALUES ('18','Ticino','Sued','Schweiz')
INSERT INTO Geografie VALUES ('19','Graubuenden','Ost','Schweiz')
INSERT INTO Geografie VALUES ('20','Geneva','Sued','Schweiz')
INSERT INTO Geografie VALUES ('21','Vaud','West','Schweiz')
INSERT INTO Geografie VALUES ('22','Fribourg','West','Schweiz')
INSERT INTO Geografie VALUES ('23','Bern','West','Schweiz')
INSERT INTO Geografie VALUES ('24','Neuchuetel','West','Schweiz')
INSERT INTO Geografie VALUES ('25','Jura','Nord','Schweiz')
INSERT INTO Geografie VALUES ('26','Basel','Nord','Schweiz')
INSERT INTO Geografie VALUES ('27','Solothurn','Nord','Schweiz')
INSERT INTO Geografie VALUES ('28','Aargau','Nord','Schweiz')
INSERT INTO Geografie VALUES ('29','Schaffhausen','Nord','Schweiz')
INSERT INTO Geografie VALUES ('30','Zuerich','Nord','Schweiz')
INSERT INTO Geografie VALUES ('31','Luzern','West','Schweiz')
INSERT INTO Geografie VALUES ('32','Unterwalden','West','Schweiz')
INSERT INTO Geografie VALUES ('33','Appenzell','Ost','Schweiz')
INSERT INTO Geografie VALUES ('34','Sankt Gallen','Ost','Schweiz')
INSERT INTO Geografie VALUES ('35','Zug','Ost','Schweiz')
INSERT INTO Geografie VALUES ('36','Schwyz','Ost','Schweiz')
INSERT INTO Geografie VALUES ('37','Glarus','Ost','Schweiz')
INSERT INTO Geografie VALUES ('38','Uri','Ost','Schweiz')
INSERT INTO Geografie VALUES ('39','Vorarlberg','uesterreich','uesterreich')
INSERT INTO Geografie VALUES ('40','Tirol','uesterreich','uesterreich')
INSERT INTO Geografie VALUES ('41','Salzburg','uesterreich','uesterreich')
INSERT INTO Geografie VALUES ('42','Oberuesterreich','uesterreich','uesterreich')
INSERT INTO Geografie VALUES ('43','Niederuesterreich','uesterreich','uesterreich')
INSERT INTO Geografie VALUES ('44','Burgenland','uesterreich','uesterreich')
INSERT INTO Geografie VALUES ('45','Steiermark','uesterreich','uesterreich')
INSERT INTO Geografie VALUES ('46','Kuernten','uesterreich','uesterreich')
END

--inserts mitarbeiter
BEGIN
INSERT INTO Mitarbeiter VALUES ('01','Sybille Neubert')
INSERT INTO Mitarbeiter VALUES ('02','NULL')
INSERT INTO Mitarbeiter VALUES ('03','Maja Guenther')
INSERT INTO Mitarbeiter VALUES ('04','Buerbel Blumberg')
INSERT INTO Mitarbeiter VALUES ('05','Jonas Mueller')
INSERT INTO Mitarbeiter VALUES ('06','Rebecca Kunze')
INSERT INTO Mitarbeiter VALUES ('08','Horst Lehmann')
INSERT INTO Mitarbeiter VALUES ('09','Ralf Fuerster')
INSERT INTO Mitarbeiter VALUES ('10','Heidemarie Fluegel')
INSERT INTO Mitarbeiter VALUES ('11','Lucie Heinrich')
INSERT INTO Mitarbeiter VALUES ('12','Gudrun Dammeier')
INSERT INTO Mitarbeiter VALUES ('14','Heinrich Gans')
INSERT INTO Mitarbeiter VALUES ('16','Lutz ueresund')
INSERT INTO Mitarbeiter VALUES ('17','Sven Klaus')
INSERT INTO Mitarbeiter VALUES ('18','Jens Meier')
INSERT INTO Mitarbeiter VALUES ('20','Dorit Gille')
END

--inserts produktkategorie
BEGIN
INSERT INTO Produktkategorie VALUES ('01','Backwaren','NULL')
INSERT INTO Produktkategorie VALUES ('02','Milchprodukte','Horst Lehmann')
INSERT INTO Produktkategorie VALUES ('03','Fleisch','Maja Guenther')
END

--inserts produktsubkategorie
BEGIN
INSERT INTO Produktsubkategorie VALUES ('001','Bagels','Heidemarie Fluegel','01')
INSERT INTO Produktsubkategorie VALUES ('002','Kuese','Ralf Fuerster','02')
INSERT INTO Produktsubkategorie VALUES ('003','Feinkostfleisch','Buerbel Blumberg','03')
INSERT INTO Produktsubkategorie VALUES ('004','frisches Schweinefleisch','Jonas Mueller','03')
INSERT INTO Produktsubkategorie VALUES ('005','gefrorenes Huehnchen','Lucie Heinrich','03')
INSERT INTO Produktsubkategorie VALUES ('006','Hamburger','Rebecca Kunze','03')
INSERT INTO Produktsubkategorie VALUES ('007','Hot Dogs','Jens Meier','03')
INSERT INTO Produktsubkategorie VALUES ('008','Milch','Gudrun Dammeier','02')
INSERT INTO Produktsubkategorie VALUES ('009','Muffins','Lutz ueresund','01')
INSERT INTO Produktsubkategorie VALUES ('010','Brot','Heinrich Gans','01')
INSERT INTO Produktsubkategorie VALUES ('011','Sauerrahm','Sven Klaus','02')
INSERT INTO Produktsubkategorie VALUES ('012','Joghurt','Dorit Gille','02')
END

--inserts produkt
BEGIN
INSERT INTO Produkt VALUES ('1001','American','Huehnchen Hot Dogss','2,45','007')
INSERT INTO Produkt VALUES ('1002','American','extra magerer Hamburger','1,2','006')
INSERT INTO Produkt VALUES ('1003','American','gefrorene Huehnchenfluegel','3','005')
INSERT INTO Produkt VALUES ('1004','American','gefrorene Huehnchenschenkel','2,05','005')
INSERT INTO Produkt VALUES ('1005','American','gefrorene Huehnchenbrust','1,1','005')
INSERT INTO Produkt VALUES ('1006','American','geschnittenes Huehnchen','0,26','003')
INSERT INTO Produkt VALUES ('1007','American','geschnittener Schinken','1,48','003')
INSERT INTO Produkt VALUES ('1008','American','geschnittener Truthahn','3,05','003')
INSERT INTO Produkt VALUES ('1009','American','Ochsenfleisch','2,49','003')
INSERT INTO Produkt VALUES ('1010','American','Kammsteak','2,04','004')
INSERT INTO Produkt VALUES ('1011','American','lange Hot Dogs','2,15','007')
INSERT INTO Produkt VALUES ('1012','American','gewuerzter Hamburger','0,97','006')
INSERT INTO Produkt VALUES ('1013','American','Truthahn Hot Dogs','2,21','007')
INSERT INTO Produkt VALUES ('1101','Aoste','lange Hot Dogs','1,4','007')
INSERT INTO Produkt VALUES ('1102','Aoste','extra magerer Hamburger','0,79','006')
INSERT INTO Produkt VALUES ('1103','Aoste','gefrorene Huehnchenfluegel','1,15','005')
INSERT INTO Produkt VALUES ('1104','Aoste','gefrorene Huehnchenschenkel','0,86','005')
INSERT INTO Produkt VALUES ('1105','Aoste','gefrorene Huehnchenbrust','2,29','005')
INSERT INTO Produkt VALUES ('1106','Aoste','geschnittenes Huehnchen','1,68','003')
INSERT INTO Produkt VALUES ('1107','Aoste','geschnittener Schinken','0,66','003')
INSERT INTO Produkt VALUES ('1108','Aoste','geschnittener Truthahn','2,51','003')
INSERT INTO Produkt VALUES ('1109','Aoste','Ochsenfleisch','1,23','003')
INSERT INTO Produkt VALUES ('1110','Aoste','Kammsteak','1,02','004')
INSERT INTO Produkt VALUES ('1111','Aoste','Truthahn Hot Dogs','1,47','007')
INSERT INTO Produkt VALUES ('1112','Aoste','gewuerzter Hamburger','1,31','006')
INSERT INTO Produkt VALUES ('1113','Aoste','Huehnchen Hot Dogs','1,14','007')
INSERT INTO Produkt VALUES ('1201','Colonial','Weiuebrot','1,36','010')
INSERT INTO Produkt VALUES ('1202','Colonial','Vollkornbrot','1,65','010')
INSERT INTO Produkt VALUES ('1203','Colonial','Heidelbeer-Muffins','1,96','009')
INSERT INTO Produkt VALUES ('1204','Colonial','Bagels','1,09','001')
INSERT INTO Produkt VALUES ('1205','Colonial','Muffins','1,58','009')
INSERT INTO Produkt VALUES ('1206','Colonial','Preiselbeer-Muffins','1,5','009')
INSERT INTO Produkt VALUES ('1207','Colonial','Roggenbrot','3,49','010')
INSERT INTO Produkt VALUES ('1208','Colonial','Pumpernickel Brot','2,01','010')
INSERT INTO Produkt VALUES ('1209','Colonial','engl. Muffins','1,42','009')
INSERT INTO Produkt VALUES ('1301','Danone','Heidelbeer Joghurt','2,58','012')
INSERT INTO Produkt VALUES ('1302','Danone','Schoko-Milch','0,85','008')
INSERT INTO Produkt VALUES ('1303','Danone','Buttermilch','1,03','008')
INSERT INTO Produkt VALUES ('1304','Danone','haltbare Milch','0,6','008')
INSERT INTO Produkt VALUES ('1305','Danone','Cheddar, mild','1,74','002')
INSERT INTO Produkt VALUES ('1306','Danone','Vollmilch','1,02','008')
INSERT INTO Produkt VALUES ('1307','Danone','Erdbeer Joghurt','1,61','012')
INSERT INTO Produkt VALUES ('1308','Danone','fettarme Milch','3,05','008')
INSERT INTO Produkt VALUES ('1309','Danone','Creme fraiche','0,52','011')
INSERT INTO Produkt VALUES ('1310','Danone','Creme legere','2,22','011')
INSERT INTO Produkt VALUES ('1311','Danone','Leerdammer','0,71','002')
INSERT INTO Produkt VALUES ('1312','Danone','Muenster Kuese','0,5','002')
INSERT INTO Produkt VALUES ('1313','Danone','Cheddar, scharf','1,35','002')
INSERT INTO Produkt VALUES ('1314','Danone','Appenzeller','1,64','002')
INSERT INTO Produkt VALUES ('1315','Danone','Old Amsterdam','0,78','002')
INSERT INTO Produkt VALUES ('1316','Danone','Havarti Kuese','3,39','002')
INSERT INTO Produkt VALUES ('1317','Danone','Leerdammer charactere','1,83','002')
INSERT INTO Produkt VALUES ('1318','Danone','Leerdammer lite','3,55','002')
INSERT INTO Produkt VALUES ('1401','Du Darfst','gefrorene Huehnchenschenkel','1,13','005')
INSERT INTO Produkt VALUES ('1402','Du Darfst','gefrorene Huehnchenfluegel','1,81','005')
INSERT INTO Produkt VALUES ('1403','Du Darfst','gewuerzter Hamburger','1,22','006')
INSERT INTO Produkt VALUES ('1404','Du Darfst','gefrorene Huehnchenbrust','0,9','005')
INSERT INTO Produkt VALUES ('1405','Du Darfst','Kammsteak','2,34','004')
INSERT INTO Produkt VALUES ('1406','Du Darfst','extra magerer Hamburger','2,68','006')
INSERT INTO Produkt VALUES ('1407','Du Darfst','Huehnchen Hot Dogs','1,46','007')
INSERT INTO Produkt VALUES ('1408','Du Darfst','geschnittener Schinken','2,39','003')
INSERT INTO Produkt VALUES ('1409','Du Darfst','geschnittener Truthahn','1,79','003')
INSERT INTO Produkt VALUES ('1410','Du Darfst','Ochsenfleisch','2,3','003')
INSERT INTO Produkt VALUES ('1411','Du Darfst','lange Hot Dogs','1,37','007')
INSERT INTO Produkt VALUES ('1412','Du Darfst','Truthahn Hot Dogs','0,53','007')
INSERT INTO Produkt VALUES ('1413','Du Darfst','geschnittenes Huehnchen','1,31','003')
INSERT INTO Produkt VALUES ('1501','Frico','Old Amsterdam','0,96','002')
INSERT INTO Produkt VALUES ('1502','Frico','Appenzeller','0,41','002')
INSERT INTO Produkt VALUES ('1503','Frico','Cheddar, scharf','0,99','002')
INSERT INTO Produkt VALUES ('1504','Frico','Cheddar, mild','0,62','002')
INSERT INTO Produkt VALUES ('1505','Frico','Havarti Kuese','2,55','002')
INSERT INTO Produkt VALUES ('1506','Frico','haltbare Milch','0,41','008')
INSERT INTO Produkt VALUES ('1507','Frico','Leerdammer','1,8','002')
INSERT INTO Produkt VALUES ('1508','Frico','Buttermilch','2,16','008')
INSERT INTO Produkt VALUES ('1509','Frico','Schoko-Milch','1,03','008')
INSERT INTO Produkt VALUES ('1510','Frico','fettarme Milch','2,53','008')
INSERT INTO Produkt VALUES ('1511','Frico','Leerdammer charactere','1,11','002')
INSERT INTO Produkt VALUES ('1512','Frico','Muenster Kuese','1,43','002')
INSERT INTO Produkt VALUES ('1513','Frico','Creme fraiche','1,85','011')
INSERT INTO Produkt VALUES ('1514','Frico','Creme legere','1,41','011')
INSERT INTO Produkt VALUES ('1515','Frico','Vollmilch','2,93','008')
INSERT INTO Produkt VALUES ('1516','Frico','Erdbeer Joghurt','2,97','012')
INSERT INTO Produkt VALUES ('1517','Frico','Heidelbeer Joghurt','3,12','012')
INSERT INTO Produkt VALUES ('1518','Frico','Leerdammer lite','1,65','002')
INSERT INTO Produkt VALUES ('1601','Friedrichs','Vollkornbrot','1,3','010')
INSERT INTO Produkt VALUES ('1602','Friedrichs','Weiuebrot','0,75','010')
INSERT INTO Produkt VALUES ('1603','Friedrichs','Pumpernickel Brot','2,69','010')
INSERT INTO Produkt VALUES ('1604','Friedrichs','engl. Muffins','1,23','009')
INSERT INTO Produkt VALUES ('1605','Friedrichs','Preiselbeer-Muffins','1,38','009')
INSERT INTO Produkt VALUES ('1606','Friedrichs','Muffins','0,59','009')
INSERT INTO Produkt VALUES ('1607','Friedrichs','Roggenbrot','1,53','010')
INSERT INTO Produkt VALUES ('1608','Friedrichs','Bagels','2,58','001')
INSERT INTO Produkt VALUES ('1609','Friedrichs','Heidelbeer-Muffins','0,61','009')
INSERT INTO Produkt VALUES ('1701','Golden Toast','engl. Muffins','0,96','009')
INSERT INTO Produkt VALUES ('1702','Golden Toast','Pumpernickel Brot','0,22','010')
INSERT INTO Produkt VALUES ('1703','Golden Toast','Roggenbrot','0,67','010')
INSERT INTO Produkt VALUES ('1704','Golden Toast','Preiselbeer-Muffins','2,05','009')
INSERT INTO Produkt VALUES ('1705','Golden Toast','Muffins','1,22','009')
INSERT INTO Produkt VALUES ('1706','Golden Toast','Bagels','1,26','001')
INSERT INTO Produkt VALUES ('1707','Golden Toast','Heidelbeer-Muffins','2,3','009')
INSERT INTO Produkt VALUES ('1708','Golden Toast','Weiuebrot','1,69','010')
INSERT INTO Produkt VALUES ('1709','Golden Toast','Vollkornbrot','2,75','010')
INSERT INTO Produkt VALUES ('1801','Harry','engl. Muffins','2,21','009')
INSERT INTO Produkt VALUES ('1802','Harry','Weiuebrot','0,73','010')
INSERT INTO Produkt VALUES ('1803','Harry','Heidelbeer-Muffins','2,5','009')
INSERT INTO Produkt VALUES ('1804','Harry','Bagels','1,75','001')
INSERT INTO Produkt VALUES ('1805','Harry','Muffins','2,6','009')
INSERT INTO Produkt VALUES ('1806','Harry','Preiselbeer-Muffins','2,58','009')
INSERT INTO Produkt VALUES ('1807','Harry','Vollkornbrot','3,03','010')
INSERT INTO Produkt VALUES ('1808','Harry','Pumpernickel Brot','3,07','010')
INSERT INTO Produkt VALUES ('1809','Harry','Roggenbrot','2,31','010')
INSERT INTO Produkt VALUES ('1901','Herta','Huehnchen Hot Dogs','1,4','007')
INSERT INTO Produkt VALUES ('1902','Herta','Ochsenfleisch','1,75','003')
INSERT INTO Produkt VALUES ('1903','Herta','gewuerzter Hamburger','2,6','006')
INSERT INTO Produkt VALUES ('1904','Herta','extra magerer Hamburger','2,35','006')
INSERT INTO Produkt VALUES ('1905','Herta','gefrorene Huehnchenfluegel','2,61','005')
INSERT INTO Produkt VALUES ('1906','Herta','Kammsteak','0,66','004')
INSERT INTO Produkt VALUES ('1907','Herta','Truthahn Hot Dogs','0,59','007')
INSERT INTO Produkt VALUES ('1908','Herta','gefrorene Huehnchenschenkel','2,76','005')
INSERT INTO Produkt VALUES ('1909','Herta','gefrorene Huehnchenbrust','2,07','005')
INSERT INTO Produkt VALUES ('1910','Herta','geschnittenes Huehnchen','1,72','003')
INSERT INTO Produkt VALUES ('1911','Herta','geschnittener Schinken','1,54','003')
INSERT INTO Produkt VALUES ('1912','Herta','geschnittener Truthahn','0,46','003')
INSERT INTO Produkt VALUES ('1913','Herta','lange Hot Dogs','3,66','007')
INSERT INTO Produkt VALUES ('2001','Meica','Huehnchen Hot Dogs','2,68','007')
INSERT INTO Produkt VALUES ('2002','Meica','geschnittener Truthahn','1,83','003')
INSERT INTO Produkt VALUES ('2003','Meica','Truthahn Hot Dogs','1,69','007')
INSERT INTO Produkt VALUES ('2004','Meica','lange Hot Dogs','3,68','007')
INSERT INTO Produkt VALUES ('2005','Meica','Ochsenfleisch','2,34','003')
INSERT INTO Produkt VALUES ('2006','Meica','geschnittener Schinken','1,26','003')
INSERT INTO Produkt VALUES ('2007','Meica','geschnittenes Huehnchen','1,52','003')
INSERT INTO Produkt VALUES ('2008','Meica','gefrorene Huehnchenbrust','2,35','005')
INSERT INTO Produkt VALUES ('2009','Meica','gefrorene Huehnchenschenkel','1,32','005')
INSERT INTO Produkt VALUES ('2010','Meica','gefrorene Huehnchenfluegel','3,38','005')
INSERT INTO Produkt VALUES ('2011','Meica','extra magerer Hamburger','1,78','006')
INSERT INTO Produkt VALUES ('2012','Meica','gewuerzter Hamburger','1,29','006')
INSERT INTO Produkt VALUES ('2013','Meica','Kammsteak','2,37','004')
INSERT INTO Produkt VALUES ('2101','Milram','Appenzeller','2,24','002')
INSERT INTO Produkt VALUES ('2102','Milram','fettarme Milch','0,95','008')
INSERT INTO Produkt VALUES ('2103','Milram','Heidelbeer Joghurt','0,38','012')
INSERT INTO Produkt VALUES ('2104','Milram','Havarti Kuese','3,07','002')
INSERT INTO Produkt VALUES ('2105','Milram','Vollmilch','0,54','008')
INSERT INTO Produkt VALUES ('2106','Milram','Creme legere','0,67','011')
INSERT INTO Produkt VALUES ('2107','Milram','Schoko-Milch','1,38','008')
INSERT INTO Produkt VALUES ('2108','Milram','Buttermilch','1,25','008')
INSERT INTO Produkt VALUES ('2109','Milram','haltbare Milch','3,75','008')
INSERT INTO Produkt VALUES ('2110','Milram','Cheddar, mild','2,96','002')
INSERT INTO Produkt VALUES ('2111','Milram','Cheddar, scharf','2,79','002')
INSERT INTO Produkt VALUES ('2112','Milram','Old Amsterdam','2,47','002')
INSERT INTO Produkt VALUES ('2113','Milram','Leerdammer charactere','2,95','002')
INSERT INTO Produkt VALUES ('2114','Milram','Leerdammer lite','1,54','002')
INSERT INTO Produkt VALUES ('2115','Milram','Leerdammer','3,12','002')
INSERT INTO Produkt VALUES ('2116','Milram','Erdbeer Joghurt','2,06','012')
INSERT INTO Produkt VALUES ('2117','Milram','Creme fraiche','2,26','011')
INSERT INTO Produkt VALUES ('2118','Milram','Muenster Kuese','1,1','002')
INSERT INTO Produkt VALUES ('2201','Mueller Milch','Erdbeer Joghurt','3,94','012')
INSERT INTO Produkt VALUES ('2202','Mueller Milch','Old Amsterdam','2,6','002')
INSERT INTO Produkt VALUES ('2203','Mueller Milch','Leerdammer lite','0,95','002')
INSERT INTO Produkt VALUES ('2204','Mueller Milch','Havarti Kuese','1,04','002')
INSERT INTO Produkt VALUES ('2205','Mueller Milch','Heidelbeer Joghurt','1,82','012')
INSERT INTO Produkt VALUES ('2206','Mueller Milch','Appenzeller','0,74','002')
INSERT INTO Produkt VALUES ('2207','Mueller Milch','Vollmilch','0,35','008')
INSERT INTO Produkt VALUES ('2208','Mueller Milch','fettarme Milch','0,89','008')
INSERT INTO Produkt VALUES ('2209','Mueller Milch','Schoko-Milch','2,08','008')

INSERT INTO Produkt VALUES ('2210','Mueller Milch','Leerdammer','1,74','002')
INSERT INTO Produkt VALUES ('2211','Mueller Milch','haltbare Milch','3,19','008')
INSERT INTO Produkt VALUES ('2212','Mueller Milch','Cheddar, mild','3,17','002')
INSERT INTO Produkt VALUES ('2213','Mueller Milch','Cheddar, scharf','2,02','002')
INSERT INTO Produkt VALUES ('2214','Mueller Milch','Creme legere','0,78','011')
INSERT INTO Produkt VALUES ('2215','Mueller Milch','Creme fraiche','2,04','011')
INSERT INTO Produkt VALUES ('2216','Mueller Milch','Buttermilch','0,9','008')
INSERT INTO Produkt VALUES ('2217','Mueller Milch','Leerdammer charactere','1,27','002')
INSERT INTO Produkt VALUES ('2218','Mueller Milch','Muenster Kuese','3,74','002')
INSERT INTO Produkt VALUES ('2301','Sachsen Milch','Erdbeer Joghurt','0,62','012')
INSERT INTO Produkt VALUES ('2302','Sachsen Milch','Vollmilch','2,58','008')
INSERT INTO Produkt VALUES ('2303','Sachsen Milch','fettarme Milch','1,18','008')
INSERT INTO Produkt VALUES ('2304','Sachsen Milch','Schoko-Milch','1,78','008')
INSERT INTO Produkt VALUES ('2305','Sachsen Milch','Buttermilch','0,62','008')
INSERT INTO Produkt VALUES ('2306','Sachsen Milch','Cheddar, mild','2,51','002')
INSERT INTO Produkt VALUES ('2307','Sachsen Milch','Cheddar, scharf','2,72','002')
INSERT INTO Produkt VALUES ('2308','Sachsen Milch','Heidelbeer Joghurt','3,5','012')
INSERT INTO Produkt VALUES ('2309','Sachsen Milch','haltbare Milch','0,68','008')
INSERT INTO Produkt VALUES ('2310','Sachsen Milch','Old Amsterdam','1,24','002')
INSERT INTO Produkt VALUES ('2311','Sachsen Milch','Havarti Kuese','3,1','002')
INSERT INTO Produkt VALUES ('2312','Sachsen Milch','Leerdammer charactere','3,28','002')
INSERT INTO Produkt VALUES ('2313','Sachsen Milch','Leerdammer lite','1,48','002')
INSERT INTO Produkt VALUES ('2314','Sachsen Milch','Muenster Kuese','1,56','002')
INSERT INTO Produkt VALUES ('2315','Sachsen Milch','Leerdammer','3,22','002')
INSERT INTO Produkt VALUES ('2316','Sachsen Milch','Creme legere','1,93','011')
INSERT INTO Produkt VALUES ('2317','Sachsen Milch','Appenzeller','1,58','002')
INSERT INTO Produkt VALUES ('2318','Sachsen Milch','Creme fraiche','0,47','011')
INSERT INTO Produkt VALUES ('2401','Wendels Bestes','Weiuebrot','0,36','010')
INSERT INTO Produkt VALUES ('2402','Wendels Bestes','Pumpernickel Brot','0,67','010')
INSERT INTO Produkt VALUES ('2403','Wendels Bestes','Vollkornbrot','0,65','010')
INSERT INTO Produkt VALUES ('2404','Wendels Bestes','Roggenbrot','3,57','010')
INSERT INTO Produkt VALUES ('2405','Wendels Bestes','engl. Muffins','1,5','009')
INSERT INTO Produkt VALUES ('2406','Wendels Bestes','Preiselbeer-Muffins','2,45','009')
INSERT INTO Produkt VALUES ('2407','Wendels Bestes','Bagels','3,2','001')
INSERT INTO Produkt VALUES ('2408','Wendels Bestes','Heidelbeer-Muffins','1,1','009')
INSERT INTO Produkt VALUES ('2409','Wendels Bestes','Muffins','1,57','009')
END

--inserts Zeit
BEGIN
insert into Zeit values ('201501','Januar','201501','1.Quartal','2015')
insert into Zeit values ('201502','Februar','201501','1.Quartal','2015')
insert into Zeit values ('201503','Muerz','201501','1.Quartal','2015')
insert into Zeit values ('201504','April','201502','2.Quartal','2015')
insert into Zeit values ('201505','Mai','201502','2.Quartal','2015')
insert into Zeit values ('201506','Juni','201502','2.Quartal','2015')
insert into Zeit values ('201507','Juli','201503','3.Quartal','2015')
insert into Zeit values ('201508','August','201503','3.Quartal','2015')
insert into Zeit values ('201509','September','201503','3.Quartal','2015')
insert into Zeit values ('201510','Oktober','201504','4.Quartal','2015')
insert into Zeit values ('201511','November','201504','4.Quartal','2015')
insert into Zeit values ('201512','Dezember','201504','4.Quartal','2015')
insert into Zeit values ('201601','Januar','201601','1.Quartal','2016')
insert into Zeit values ('201602','Februar','201601','1.Quartal','2016')
insert into Zeit values ('201603','Muerz','201601','1.Quartal','2016')
insert into Zeit values ('201604','April','201602','2.Quartal','2016')
insert into Zeit values ('201605','Mai','201602','2.Quartal','2016')
insert into Zeit values ('201606','Juni','201602','2.Quartal','2016')
insert into Zeit values ('201607','Juli','201603','3.Quartal','2016')
insert into Zeit values ('201608','August','201603','3.Quartal','2016')
insert into Zeit values ('201609','September','201603','3.Quartal','2016')
insert into Zeit values ('201610','Oktober','201604','4.Quartal','2016')
insert into Zeit values ('201611','November','201604','4.Quartal','2016')
insert into Zeit values ('201612','Dezember','201604','4.Quartal','2016')
END

SELECT * FROM Mitarbeiter
SELECT * FROM Produktkategorie

UPDATE Mitarbeiter SET Name='Fricke' WHERE Mitarbeiter_ID=02
UPDATE Produktkategorie SET Kategorie_Manager='Fricke' WHERE Kategorie_ID=01
ALTER TABLE Mitarbeiter ADD Manager_ID varchar(2) FOREIGN KEY (Manager_ID) REFERENCES Mitarbeiter(Mitarbeiter_ID)


UPDATE Mitarbeiter SET Manager_ID='02' WHERE Mitarbeiter_ID IN (10,16,14)
UPDATE Mitarbeiter SET Manager_ID='08' WHERE Mitarbeiter_ID IN (09,12,17,20)
UPDATE Mitarbeiter SET Manager_ID='03' WHERE Mitarbeiter_ID IN (04,05,11,06,18)
UPDATE Mitarbeiter SET Manager_ID='01' WHERE Mitarbeiter_ID IN (02,03,08)
UPDATE Mitarbeiter SET Manager_ID='01' WHERE Mitarbeiter_ID ='01'

--1.11
CREATE TABLE BelegeTMP (
	Bon_ID VARCHAR(4),
	Fil_ID VARCHAR(5),
	Datum DATE,
	Prod_ID VARCHAR(4),
	Preis float,
	Anzahl int
)

CREATE TABLE UmsatzdatenTMP (
	Mon_ID VARCHAR(6),
	Land_ID VARCHAR(2),
	Produkt_ID VARCHAR(4),
	Mitarbeiter_ID VARCHAR(2),
	Umsatzbetrag MONEY,
	Umsatzmenge INTEGER
)


BULK INSERT BelegeTMP FROM 'T:\Temp\Belege_2016_4.csv' WITH (FIELDTERMINATOR = ',', FIRSTROW = 2)
--firstrow, ab wo er einlesen soll, FIELDTERMINATOR = Feldtrennerzeichen
SELECT * FROM UmsatzdatenTMP
SELECT * FROM BelegeTMP

INSERT INTO UmsatzdatenTMP (Mon_ID,Land_ID,Produkt_ID,Umsatzbetrag,Umsatzmenge)
SELECT 
CASE
WHEN  (MONTH(Datum) < 10) THEN CONCAT (YEAR(Datum),'0', MONTH(Datum)) --concat: String anfuegen und ausgeben
ELSE CONCAT (YEAR(Datum), MONTH(Datum))
END,
LEFT(Fil_ID,2), --left: abspalten der ersten 2 Zeichen
Prod_ID,
Preis*Anzahl,
Anzahl
FROM BelegeTMP

ALTER TABLE Produktsubkategorie ADD Mitarbeiter_ID varchar(2)
SELECT * FROM Produktsubkategorie
UPDATE Produktsubkategorie SET Mitarbeiter_ID = (SELECT m.Mitarbeiter_ID FROM Mitarbeiter m WHERE Subkategorie_Manager = Name)
--spalte angefuegt, in der die MitID der Manager steht

UPDATE UmsatzdatenTMP SET Mitarbeiter_ID  = (SELECT Mitarbeiter_ID FROM Produktsubkategorie ps JOIN Produkt p ON p.Subkategorie_ID = ps.Subkategorie_ID)
-- falsch

UPDATE UmsatzdatenTMP SET Mitarbeiter_ID = ps.Mitarbeiter_ID
FROM Produkt p, UmsatzdatenTMP u, Produktsubkategorie ps WHERE p.Subkategorie_ID = ps.Subkategorie_ID AND p.Produkt_ID = u.Produkt_ID
AND p.Produkt_ID = u.Produkt_ID 

INSERT INTO Umsatzdaten
SELECT Mon_ID, Land_ID, Produkt_ID, Mitarbeiter_ID, SUM(Umsatzbetrag), SUM(Umsatzmenge) 
FROM UmsatzdatenTMP GROUP BY Mon_ID, Land_ID, Produkt_ID, Mitarbeiter_ID

SELECT * From Umsatzdaten
SELECT SUM(umsatzbetrag) AS Jahresumsatz FROM Umsatzdaten

--Aufgabe A.6
CREATE TABLE Plandaten (
	Mon_ID VARCHAR(6),
	Land_ID VARCHAR(2),
	Produkt_ID VARCHAR(4),
	Umsatzplan MONEY,
	PRIMARY KEY (Mon_ID,Land_ID,Produkt_ID),
	FOREIGN KEY (Mon_ID) REFERENCES Zeit(Mon_ID),
	FOREIGN KEY (Land_ID) REFERENCES Geografie(Land_ID),
	FOREIGN KEY (Produkt_ID) REFERENCES Produkt(Produkt_ID),
)
BULK INSERT Plandaten FROM 'T:\Temp\Plandaten.csv' WITH (FIELDTERMINATOR = ';', FIRSTROW = 2)
SELECT * FROM Plandaten

--A.7.1
--normaler JOIN
SELECT Monatsname, Name, Produktname, Bundesland, Umsatzbetrag FROM Umsatzdaten u
JOIN Geografie g ON u.Land_ID = g.Land_ID
JOIN Zeit z ON u.Mon_ID = z.Mon_ID
JOIN Produkt p ON u.Produkt_ID = p.Produkt_ID
JOIN Mitarbeiter m ON u.Mitarbeiter_ID = m.Mitarbeiter_ID
WHERE Bundesland IN ('Sachsen', 'Thueringen')

--A.7.2
SELECT Bundesland, SUM(Umsatzbetrag) AS Umsatzbetrag, SUM(Umsatzmenge) AS Umsatzmenge FROM Umsatzdaten u
JOIN Geografie g ON g.Land_ID = u.Land_ID
WHERE Bundesland IN ('Sachsen', 'Thueringen')
GROUP BY Bundesland

--A.7.3
SELECT Bundesland, Q_ID, SUM(Umsatzbetrag) AS Umsatzbetrag, SUM(Umsatzmenge) AS Umsatzmenge FROM Umsatzdaten u
JOIN Geografie g ON g.Land_ID = u.Land_ID
JOIN Zeit z ON u.Mon_ID = z.Mon_ID
WHERE Bundesland IN ('Sachsen', 'Thueringen')
GROUP BY Bundesland, Q_ID

--A.7.4
SELECT Umsatzbetrag, Staat, Subkategorie FROM Umsatzdaten u
JOIN Geografie g ON u.Land_ID = g.Land_ID
JOIN Produkt p ON u.Produkt_ID = p.Produkt_ID
JOIN Produktsubkategorie pk ON p.Subkategorie_ID = pk.Subkategorie_ID

--A.7.5
SELECT SUM(Umsatzbetrag) AS Umsatzbetrag, Staat, Subkategorie FROM Umsatzdaten u
JOIN Geografie g ON u.Land_ID = g.Land_ID
JOIN Produkt p ON u.Produkt_ID = p.Produkt_ID
JOIN Produktsubkategorie pk ON p.Subkategorie_ID = pk.Subkategorie_ID
GROUP BY CUBE (Staat, Subkategorie)

--A.7.6
SELECT SUM(Umsatzbetrag) AS Umsatzbetrag, Staat, Subkategorie, Jahr FROM Umsatzdaten u
JOIN Geografie g ON u.Land_ID = g.Land_ID
JOIN Produkt p ON u.Produkt_ID = p.Produkt_ID
JOIN Produktsubkategorie pk ON p.Subkategorie_ID = pk.Subkategorie_ID
JOIN Zeit z ON u.Mon_ID = z.Mon_ID
GROUP BY CUBE(Staat, Subkategorie, Jahr)

--A.7.7
SELECT SUM(Umsatzbetrag) AS Umsatzbetrag, Staat, Subkategorie, Jahr FROM Umsatzdaten u
JOIN Geografie g ON u.Land_ID = g.Land_ID
JOIN Produkt p ON u.Produkt_ID = p.Produkt_ID
JOIN Produktsubkategorie pk ON p.Subkategorie_ID = pk.Subkategorie_ID
JOIN Zeit z ON u.Mon_ID = z.Mon_ID
GROUP BY ROLLUP(Staat, Subkategorie, Jahr)

--A.7.8
SELECT SUM(Umsatzbetrag) AS Umsatzbetrag, Kategorie, Subkategorie, Produktname FROM Umsatzdaten u
JOIN Produkt p ON u.Produkt_ID = p.Produkt_ID
JOIN Produktsubkategorie psk ON p.Subkategorie_ID = psk.Subkategorie_ID
JOIN Produktkategorie pk ON psk.Kategorie_ID = pk.Kategorie_ID
GROUP BY ROLLUP(Kategorie, Subkategorie, Produktname)

--A.7.9
SELECT SUM(Umsatzbetrag) AS Umsatzbetrag, Kategorie, Subkategorie, Produktname + ' ' + Markenname AS Produkt_mit_Markenname FROM Umsatzdaten u
JOIN Produkt p ON u.Produkt_ID = p.Produkt_ID
JOIN Produktsubkategorie psk ON p.Subkategorie_ID = psk.Subkategorie_ID
JOIN Produktkategorie pk ON psk.Kategorie_ID = pk.Kategorie_ID
GROUP BY ROLLUP(Kategorie, Subkategorie, Produktname, Markenname)



