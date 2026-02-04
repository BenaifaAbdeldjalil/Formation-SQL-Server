🚀 Formation  sql Server - Structure Complète et Organisée
📚 PLAN DE FORMATION STRUCTURÉ
PHASE 1 : LES BASES FONDAMENTALES (Jour 1-3)
PHASE 2 : MANIPULATION DES DONNÉES (Jour 4-6)
PHASE 3 : RELATIONS ET REQUÊTES AVANCÉES (Jour 7-9)
PHASE 4 : OPTIMISATION & INDEXATION (Jour 10-11)
PHASE 5 : ADMINISTRATION & MAINTENANCE (Jour 12-14)
📖 PHASE 1 : LES BASES FONDAMENTALES
🎯 Jour 1 : Installation & Premiers Pas ( sql Server)
1.1 Installation  sql Server 2022

-- 1. Télécharger  sql Server 2022 Developer Edition (gratuit)
-- https://www.microsoft.com/fr-fr/-sql Server/-sql Server-downloads

-- 2. Installer avec :
-- - Moteur de base de données
-- -  sql Server Management Studio (SSMS)
-- - Configuration : Mode d'authentification mixte (Windows + )
-- - Mot de passe SA : SaP@ssw0rd123!

-- 3. Installer SSMS séparément
-- https://aka.ms/ssmsfullsetup

-- 4. Vérification :
SELECT @@VERSION;
SELECT '✅  sql Server installé avec succès !' AS message;
1.2 Connexion et Première Base

-- 1. Ouvrir SSMS et se connecter
-- Serveur : (local) ou .\EXPRESS
-- Authentification :  sql Server
-- Login : sa
-- Password : votre mot de passe

-- 2. Créer notre première base
CREATE DATABASE Formationsql Server;
GO  -- Commande pour exécuter le lot précédent

USE Formationsql Server;
GO

-- 3. Créer une table simple
CREATE TABLE Personnes (
    Nom VARCHAR(50),
    Age INT
);
GO

-- 4. Première insertion
INSERT INTO Personnes (Nom, Age) VALUES ('Jean', 25);
GO

-- 5. Première lecture
SELECT * FROM Personnes;
GO
🏗️ Jour 2 : Création de Tables Structurées
2.1 Types de Données  sql Server

/*
Types NUMÉRIQUES :
- INT : -2,147,483,648 à 2,147,483,647
- BIGINT : très grands entiers
- DECIMAL(10,2) : nombre exact, 10 chiffres, 2 décimales
- FLOAT : nombre à virgule flottante
- MONEY : données monétaires

Types TEXTE :
- VARCHAR(100) : texte variable
- NVARCHAR(100) : texte Unicode (français, accents)
- CHAR(10) : texte fixe
- TEXT : ancien type, utiliser VARCHAR(MAX)

Types DATE :
- DATE : '2024-03-15'
- DATETIME : '2024-03-15 14:30:00.000'
- DATETIME2 : plus précis
- SMALLDATETIME : date/heure légère

Types SPÉCIAUX :
- BIT : 0 ou 1 (BOOLÉEN)
- UNIQUEIDENTIFIER : GUID
- XML : données XML
- VARBINARY(MAX) : fichiers binaires
*/
2.2 Création de Notre Base "Boutique"

-- 1. Créer la base avec paramètres
CREATE DATABASE Boutique
ON PRIMARY (
    NAME = Boutique_Data,
    FILENAME = 'C:\Data\Boutique.mdf',
    SIZE = 100MB,
    MAXSIZE = UNLIMITED,
    FILEGROWTH = 10%
)
LOG ON (
    NAME = Boutique_Log,
    FILENAME = 'C:\Log\Boutique.ldf',
    SIZE = 50MB,
    MAXSIZE = 2GB,
    FILEGROWTH = 5%
);
GO

USE Boutique;
GO

-- 2. Table Clients (version simple)
CREATE TABLE Clients (
    ClientID INT IDENTITY(1,1) PRIMARY KEY,  -- Auto-incrément
    Nom NVARCHAR(50) NOT NULL,
    Prenom NVARCHAR(50) NOT NULL,
    Email NVARCHAR(100),
    Ville NVARCHAR(50) DEFAULT 'Paris',
    DateInscription DATETIME DEFAULT GETDATE()
);
GO

-- 3. Table Produits (version simple)
CREATE TABLE Produits (
    ProduitID INT IDENTITY(1,1) PRIMARY KEY,
    Nom NVARCHAR(100) NOT NULL,
    Prix DECIMAL(10,2) NOT NULL,
    Categorie NVARCHAR(50),
    Stock INT DEFAULT 0,
    DateAjout DATETIME DEFAULT GETDATE()
);
GO

-- 4. Table Commandes (version simple)
CREATE TABLE Commandes (
    CommandeID INT IDENTITY(1,1) PRIMARY KEY,
    ClientID INT,
    ProduitID INT,
    Quantite INT,
    DateCommande DATE DEFAULT CAST(GETDATE() AS DATE)
);
GO

-- Vérification
SELECT name AS TableName 
FROM sys.tables 
ORDER BY name;
GO
✏️ Jour 3 : Insertion de Données Logiques
3.1 Insérer dans "Clients"

-- Insertion avec IDENTITY (auto-incrément)
INSERT INTO Clients (Nom, Prenom, Email, Ville) 
VALUES 
(N'Dupont', N'Jean', N'jean.dupont@email.com', N'Paris'),
(N'Martin', N'Sophie', N'sophie.martin@email.com', N'Lyon'),
(N'Bernard', N'Pierre', N'pierre.bernard@email.com', N'Marseille'),
(N'Dubois', N'Marie', N'marie.dubois@email.com', N'Paris'),
(N'Petit', N'Thomas', N'thomas.petit@email.com', N'Lille');
GO

-- Vérifier (les ID sont automatiques : 1, 2, 3...)
SELECT * FROM Clients;
GO
3.2 Insérer dans "Produits"

-- Insertion multiple en une commande
INSERT INTO Produits (Nom, Prix, Categorie, Stock) VALUES
(N'Ordinateur Portable', 799.99, N'Électronique', 10),
(N'Smartphone', 499.99, N'Électronique', 25),
(N'Livre  sql Server', 39.99, N'Livres', 50),
(N'Cafetière', 89.99, N'Maison', 15),
(N'Chaise de Bureau', 199.99, N'Mobilier', 8);
GO

-- Afficher avec formatage
SELECT 
    ProduitID,
    Nom,
    FORMAT(Prix, 'C', 'fr-FR') AS 'Prix',
    Categorie,
    Stock
FROM Produits;
GO
3.3 Insérer dans "Commandes" (avec logique)

-- Insérer des commandes cohérentes
INSERT INTO Commandes (ClientID, ProduitID, Quantite, DateCommande) VALUES
(1, 1, 1, '2024-03-01'),  -- Client 1 achète Produit 1
(1, 3, 2, '2024-03-02'),  -- Client 1 achète Produit 3
(2, 2, 1, '2024-03-03'),  -- Client 2 achète Produit 2
(3, 4, 1, '2024-03-04'),  -- Client 3 achète Produit 4
(4, 5, 1, '2024-03-05');  -- Client 4 achète Produit 5
GO

-- Vérifier
SELECT * FROM Commandes;
GO

-- Afficher avec informations détaillées
SELECT 
    c.CommandeID,
    cl.Nom + ' ' + cl.Prenom AS Client,
    p.Nom AS Produit,
    c.Quantite,
    p.Prix,
    (c.Quantite * p.Prix) AS Total,
    c.DateCommande
FROM Commandes c
INNER JOIN Clients cl ON c.ClientID = cl.ClientID
INNER JOIN Produits p ON c.ProduitID = p.ProduitID;
GO
3.4 INSERT avec SELECT (copie de données)

-- Créer une table d'archives
CREATE TABLE Clients_Archive (
    ClientID INT PRIMARY KEY,
    Nom NVARCHAR(50),
    Prenom NVARCHAR(50),
    Ville NVARCHAR(50),
    DateArchivage DATETIME DEFAULT GETDATE()
);
GO

-- Copier les clients de Paris dans l'archive
INSERT INTO Clients_Archive (ClientID, Nom, Prenom, Ville)
SELECT ClientID, Nom, Prenom, Ville
FROM Clients
WHERE Ville = N'Paris';
GO

-- Vérifier
SELECT * FROM Clients_Archive;
GO
🔍 PHASE 2 : MANIPULATION DES DONNÉES
📊 Jour 4 : Consultation (SELECT) Basique
4.1 SELECT Simple

-- 1. Toutes les colonnes
SELECT * FROM Clients;

-- 2. Colonnes spécifiques
SELECT Nom, Prenom, Ville FROM Clients;

-- 3. Avec alias
SELECT 
    Nom AS 'Nom de famille',
    Prenom AS Prénom,
    Ville AS 'Ville de résidence'
FROM Clients;

-- 4. Colonnes calculées
SELECT 
    Nom,
    Prenom,
    Nom + ' ' + Prenom AS 'Nom complet',
    LEN(Nom) AS 'Longueur du nom'
FROM Clients;
4.2 WHERE (Conditions) - Différences avec My

-- 1. Condition simple
SELECT * FROM Clients WHERE Ville = N'Paris';

-- 2. Conditions multiples
SELECT * FROM Clients 
WHERE Ville = N'Paris' AND Nom LIKE N'D%';

-- 3. Opérateurs de comparaison
SELECT * FROM Produits WHERE Prix > 100;
SELECT * FROM Produits WHERE Prix BETWEEN 50 AND 200;
SELECT * FROM Clients WHERE Ville IN (N'Paris', N'Lyon');

-- 4. LIKE avec  sql Server
SELECT * FROM Clients WHERE Nom LIKE N'M%';      -- Commence par M
SELECT * FROM Clients WHERE Nom LIKE N'%er%';    -- Contient "er"
SELECT * FROM Clients WHERE Nom LIKE N'_[ea]%';  -- 2e lettre e ou a

-- 5. IS NULL / IS NOT NULL
SELECT * FROM Clients WHERE Email IS NOT NULL;

-- 6. TOP (équivalent LIMIT My)
SELECT TOP 3 * FROM Clients;                    -- 3 premiers
SELECT TOP 50 PERCENT * FROM Clients;           -- 50% des lignes
4.3 ORDER BY et OFFSET/FETCH (Pagination)

-- 1. Trier simple
SELECT * FROM Clients ORDER BY Nom ASC;      -- A à Z
SELECT * FROM Produits ORDER BY Prix DESC;   -- Cher → Pas cher

-- 2. Trier multiple
SELECT * FROM Clients ORDER BY Ville, Nom;

-- 3. Pagination ( sql Server 2012+)
SELECT * FROM Clients 
ORDER BY Nom
OFFSET 0 ROWS FETCH NEXT 3 ROWS ONLY;  -- Page 1 (lignes 1-3)

SELECT * FROM Clients 
ORDER BY Nom
OFFSET 3 ROWS FETCH NEXT 3 ROWS ONLY;  -- Page 2 (lignes 4-6)

-- 4. Ancienne méthode (avant 2012)
SELECT TOP 3 * FROM Clients 
WHERE ClientID NOT IN (
    SELECT TOP 0 ClientID FROM Clients ORDER BY Nom
)
ORDER BY Nom;
✏️ Jour 5 : Modifications (UPDATE/DELETE)
5.1 UPDATE Simple

-- 1. Modifier une ligne
UPDATE Clients 
SET Ville = N'Lyon'
WHERE ClientID = 1;

-- 2. Modifier plusieurs colonnes
UPDATE Clients 
SET 
    Ville = N'Marseille',
    Email = N'nouveau@email.com'
WHERE Nom = N'Bernard';

-- 3. Modification avec calcul
UPDATE Produits 
SET Prix = Prix * 0.9 
WHERE Categorie = N'Électronique';

-- 4. UPDATE avec JOIN
UPDATE p
SET p.Stock = p.Stock - c.Quantite
FROM Produits p
INNER JOIN Commandes c ON p.ProduitID = c.ProduitID
WHERE c.CommandeID = 1;

-- 5. Vérifier avant/après
SELECT * FROM Clients WHERE ClientID = 1;
UPDATE Clients SET Ville = N'Lyon' WHERE ClientID = 1;
SELECT * FROM Clients WHERE ClientID = 1;
5.2 DELETE et TRUNCATE

-- 1. Supprimer une ligne
DELETE FROM Clients WHERE ClientID = 5;

-- 2. Supprimer avec condition
DELETE FROM Commandes 
WHERE DateCommande < '2024-01-01';

-- 3. DELETE avec OUTPUT (voir ce qu'on supprime)
DELETE FROM Clients_Archive
OUTPUT DELETED.*
WHERE Ville = N'Paris';

-- 4. TRUNCATE TABLE (vider complètement)
TRUNCATE TABLE Clients_Archive;
-- ATTENTION : ne peut pas être annulé, réinitialise IDENTITY

-- 5. Différence DELETE vs TRUNCATE
/*
DELETE : 
- Peut avoir WHERE
- Loggé ligne par ligne (plus lent)
- Ne réinitialise pas IDENTITY
- Peut être annulé (ROLLBACK)

TRUNCATE :
- Pas de WHERE
- Loggé par allocation (très rapide)
- Réinitialise IDENTITY
- Difficulté à annuler
*/
5.3 MERGE (Upsert) - Spécifique  sql Server

-- MERGE combine INSERT, UPDATE, DELETE
MERGE INTO Clients AS target
USING (VALUES 
    (N'Nouveau', N'Client', N'nouveau@email.com', N'Paris')
) AS source (Nom, Prenom, Email, Ville)
ON target.Email = source.Email
WHEN MATCHED THEN
    UPDATE SET 
        target.Nom = source.Nom,
        target.Prenom = source.Prenom,
        target.Ville = source.Ville
WHEN NOT MATCHED THEN
    INSERT (Nom, Prenom, Email, Ville)
    VALUES (source.Nom, source.Prenom, source.Email, source.Ville);
GO
📈 Jour 6 : Agrégations et Fonctions
6.1 Fonctions d'Agrégation

-- 1. COUNT
SELECT COUNT(*) AS 'Total Clients' FROM Clients;
SELECT COUNT(*) AS 'Clients Paris' FROM Clients WHERE Ville = N'Paris';
SELECT COUNT(DISTINCT Ville) AS 'Villes différentes' FROM Clients;

-- 2. SUM
SELECT SUM(Prix) AS 'Valeur totale stock' FROM Produits;
SELECT 
    SUM(p.Prix * c.Quantite) AS 'Chiffre d affaires'
FROM Commandes c
INNER JOIN Produits p ON c.ProduitID = p.ProduitID;

-- 3. AVG
SELECT AVG(Prix) AS 'Prix moyen' FROM Produits;

-- 4. MIN/MAX
SELECT MIN(Prix) AS 'Produit moins cher' FROM Produits;
SELECT MAX(Prix) AS 'Produit plus cher' FROM Produits;

-- 5. STRING_AGG ( sql Server 2017+)
SELECT 
    Ville,
    STRING_AGG(Nom + ' ' + Prenom, ', ') AS 'Clients'
FROM Clients
GROUP BY Ville;
6.2 GROUP BY et HAVING

-- 1. GROUP BY simple
SELECT 
    Ville,
    COUNT(*) AS 'Nombre de clients'
FROM Clients
GROUP BY Ville
ORDER BY COUNT(*) DESC;

-- 2. GROUP BY multiple
SELECT 
    Categorie,
    COUNT(*) AS 'Nombre de produits',
    AVG(Prix) AS 'Prix moyen',
    SUM(Stock) AS 'Stock total'
FROM Produits
GROUP BY Categorie;

-- 3. HAVING (filtre sur groupes)
SELECT 
    Ville,
    COUNT(*) AS 'nb_clients'
FROM Clients
GROUP BY Ville
HAVING COUNT(*) >= 2;  -- Villes avec 2+ clients

-- 4. GROUP BY avec ROLLUP (sous-totaux)
SELECT 
    Ville,
    Categorie,
    COUNT(*) AS 'Nombre'
FROM Clients c
LEFT JOIN Commandes cmd ON c.ClientID = cmd.ClientID
LEFT JOIN Produits p ON cmd.ProduitID = p.ProduitID
GROUP BY ROLLUP(Ville, Categorie);
6.3 Fonctions de Fenêtrage (Window Functions)

-- 1. ROW_NUMBER()
SELECT 
    Nom,
    Prenom,
    Ville,
    ROW_NUMBER() OVER (PARTITION BY Ville ORDER BY Nom) AS 'Rang dans la ville'
FROM Clients;

-- 2. RANK() et DENSE_RANK()
SELECT 
    Nom,
    Prix,
    RANK() OVER (ORDER BY Prix DESC) AS 'Rang',
    DENSE_RANK() OVER (ORDER BY Prix DESC) AS 'Rang dense'
FROM Produits;

-- 3. LAG() et LEAD()
SELECT 
    CommandeID,
    DateCommande,
    Quantite,
    LAG(Quantite) OVER (ORDER BY DateCommande) AS 'Quantité précédente',
    LEAD(Quantite) OVER (ORDER BY DateCommande) AS 'Quantité suivante'
FROM Commandes;

-- 4. SUM() cumulatif
SELECT 
    DateCommande,
    Quantite,
    SUM(Quantite) OVER (
        ORDER BY DateCommande 
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS 'Cumul des quantités'
FROM Commandes;
🔗 PHASE 3 : RELATIONS ET REQUÊTES AVANCÉES
🤝 Jour 7 : Jointures (JOIN)
7.1 Types de Jointures  sql Server

-- 1. INNER JOIN (jointure interne)
SELECT 
    c.Nom,
    c.Prenom,
    cmd.DateCommande,
    cmd.Quantite
FROM Commandes cmd
INNER JOIN Clients c ON cmd.ClientID = c.ClientID;

-- 2. LEFT JOIN (jointure gauche)
SELECT 
    c.Nom,
    c.Prenom,
    COUNT(cmd.CommandeID) AS 'Nombre de commandes'
FROM Clients c
LEFT JOIN Commandes cmd ON c.ClientID = cmd.ClientID
GROUP BY c.ClientID, c.Nom, c.Prenom;

-- 3. RIGHT JOIN (jointure droite)
SELECT 
    cmd.CommandeID,
    c.Nom
FROM Commandes cmd
RIGHT JOIN Clients c ON cmd.ClientID = c.ClientID
WHERE cmd.CommandeID IS NULL;  -- Clients sans commande

-- 4. FULL OUTER JOIN
SELECT 
    c.Nom,
    cmd.CommandeID
FROM Clients c
FULL OUTER JOIN Commandes cmd ON c.ClientID = cmd.ClientID;

-- 5. CROSS JOIN (produit cartésien)
SELECT c.Nom, p.Nom
FROM Clients c
CROSS JOIN Produits p;  -- Toutes combinaisons clients/produits
7.2 Jointures Avancées

-- 1. SELF JOIN (jointure sur elle-même)
CREATE TABLE Employes (
    EmployeID INT PRIMARY KEY,
    Nom NVARCHAR(50),
    ManagerID INT
);

SELECT 
    e.Nom AS 'Employé',
    m.Nom AS 'Manager'
FROM Employes e
LEFT JOIN Employes m ON e.ManagerID = m.EmployeID;

-- 2. JOIN avec plusieurs tables
SELECT 
    cl.Nom + ' ' + cl.Prenom AS 'Client',
    p.Nom AS 'Produit',
    c.Quantite,
    p.Prix,
    (c.Quantite * p.Prix) AS 'Total',
    FORMAT(c.DateCommande, 'dd/MM/yyyy') AS 'Date'
FROM Commandes c
INNER JOIN Clients cl ON c.ClientID = cl.ClientID
INNER JOIN Produits p ON c.ProduitID = p.ProduitID
ORDER BY c.DateCommande DESC;

-- 3. JOIN avec conditions complexes
SELECT 
    c.*,
    p.Nom
FROM Commandes c
JOIN Produits p ON c.ProduitID = p.ProduitID
    AND p.Prix > 100  -- Condition dans le JOIN
    AND p.Stock > 0;
🎯 Jour 8 : Sous-requêtes et CTE
8.1 Sous-requêtes dans WHERE

-- 1. IN
SELECT Nom, Prenom 
FROM Clients 
WHERE ClientID IN (
    SELECT DISTINCT ClientID 
    FROM Commandes
);

-- 2. Comparaison
SELECT Nom, Prix 
FROM Produits 
WHERE Prix > (
    SELECT AVG(Prix) 
    FROM Produits
);

-- 3. EXISTS
SELECT Nom, Prenom 
FROM Clients c
WHERE EXISTS (
    SELECT 1 
    FROM Commandes cmd 
    WHERE cmd.ClientID = c.ClientID
    AND cmd.DateCommande > DATEADD(month, -1, GETDATE())
);

-- 4. ANY/ALL
SELECT Nom, Prix 
FROM Produits 
WHERE Prix > ALL (
    SELECT Prix 
    FROM Produits 
    WHERE Categorie = N'Livres'
);
8.2 Sous-requêtes dans SELECT

-- Nombre de commandes par client
SELECT 
    Nom,
    Prenom,
    (SELECT COUNT(*) 
     FROM Commandes c 
     WHERE c.ClientID = cl.ClientID) AS 'Nombre de commandes',
    (SELECT SUM(c.Quantite * p.Prix)
     FROM Commandes c
     JOIN Produits p ON c.ProduitID = p.ProduitID
     WHERE c.ClientID = cl.ClientID) AS 'Total dépensé'
FROM Clients cl;
8.3 CTE (Common Table Expressions)

-- 1. CTE simple
WITH ClientsParis AS (
    SELECT ClientID, Nom, Prenom
    FROM Clients
    WHERE Ville = N'Paris'
)
SELECT * FROM ClientsParis;

-- 2. CTE multiple
WITH 
CommandesDetails AS (
    SELECT 
        c.ClientID,
        p.ProduitID,
        c.Quantite * p.Prix AS Montant
    FROM Commandes c
    JOIN Produits p ON c.ProduitID = p.ProduitID
),
StatsClient AS (
    SELECT 
        ClientID,
        COUNT(*) AS NbCommandes,
        SUM(Montant) AS TotalDepense
    FROM CommandesDetails
    GROUP BY ClientID
)
SELECT 
    cl.Nom,
    cl.Prenom,
    sc.NbCommandes,
    sc.TotalDepense
FROM StatsClient sc
JOIN Clients cl ON sc.ClientID = cl.ClientID
ORDER BY sc.TotalDepense DESC;

-- 3. CTE récursif (pour hiérarchies)
WITH HierarchieEmployes AS (
    -- Ancrage
    SELECT EmployeID, Nom, ManagerID, 1 AS Niveau
    FROM Employes
    WHERE ManagerID IS NULL
    
    UNION ALL
    
    -- Récursion
    SELECT e.EmployeID, e.Nom, e.ManagerID, he.Niveau + 1
    FROM Employes e
    INNER JOIN HierarchieEmployes he ON e.ManagerID = he.EmployeID
)
SELECT * FROM HierarchieEmployes ORDER BY Niveau, Nom;
⚡ Jour 9 : Performance et Indexation
9.1 EXPLAIN et Execution Plan

-- 1. Afficher le plan d'exécution estimé
SET SHOWPLAN_ALL ON;
GO
SELECT * FROM Clients WHERE Ville = N'Paris';
GO
SET SHOWPLAN_ALL OFF;
GO

-- 2. Voir le plan d'exécution réel
SET STATISTICS PROFILE ON;
GO
SELECT * FROM Clients WHERE Ville = N'Paris';
GO
SET STATISTICS PROFILE OFF;
GO

-- 3. Dans SSMS : 
-- - Cliquer "Display Estimated Execution Plan" (Ctrl+L)
-- - Ou "Include Actual Execution Plan" (Ctrl+M)
9.2 Création d'Index

-- 1. Index non-cluster
CREATE INDEX IX_Clients_Ville ON Clients(Ville);

-- 2. Index composite
CREATE INDEX IX_Clients_Nom_Ville ON Clients(Nom, Ville);

-- 3. Index unique
CREATE UNIQUE INDEX IX_Clients_Email ON Clients(Email);

-- 4. Index avec INCLUDE
CREATE INDEX IX_Commandes_Date 
ON Commandes(DateCommande)
INCLUDE (ClientID, Quantite);

-- 5. Index filtré ( sql Server 2008+)
CREATE INDEX IX_Clients_Paris 
ON Clients(Ville)
WHERE Ville = N'Paris';

-- 6. Voir les index d'une table
EXEC sp_helpindex 'Clients';

-- 7. Supprimer un index
DROP INDEX IX_Clients_Ville ON Clients;
9.3 Statistiques et Maintenance

-- 1. Mettre à jour les statistiques
UPDATE STATISTICS Clients;
UPDATE STATISTICS Clients IX_Clients_Ville;

-- 2. Voir les statistiques
DBCC SHOW_STATISTICS ('Clients', 'IX_Clients_Ville');

-- 3. Reconstruire un index
ALTER INDEX IX_Clients_Ville ON Clients REBUILD;

-- 4. Réorganiser un index
ALTER INDEX IX_Clients_Ville ON Clients REORGANIZE;

-- 5. Vérifier la fragmentation
SELECT 
    object_name(object_id) AS TableName,
    index_id,
    avg_fragmentation_in_percent
FROM sys.dm_db_index_physical_stats(
    DB_ID(), 
    OBJECT_ID('Clients'), 
    NULL, 
    NULL, 
    'LIMITED'
);
🔐 PHASE 4 : INTÉGRITÉ ET SÉCURITÉ
🎯 Jour 10 : Contraintes d'Intégrité
10.1 Clés Primaires et Étrangères

-- 1. Clé primaire avec contrainte nommée
CREATE TABLE Produits_Avances (
    ProduitID INT IDENTITY(1,1),
    Nom NVARCHAR(100) NOT NULL,
    CONSTRAINT PK_Produits PRIMARY KEY (ProduitID)
);

-- 2. Clé étrangère avec actions
ALTER TABLE Commandes
ADD CONSTRAINT FK_Commandes_Clients
FOREIGN KEY (ClientID) 
REFERENCES Clients(ClientID)
ON DELETE CASCADE    -- Supprime commandes si client supprimé
ON UPDATE NO ACTION; -- Empêche modification ClientID

ALTER TABLE Commandes
ADD CONSTRAINT FK_Commandes_Produits
FOREIGN KEY (ProduitID) 
REFERENCES Produits(ProduitID)
ON DELETE NO ACTION   -- Empêche suppression produit commandé
ON UPDATE CASCADE;    -- Met à jour si ProduitID changé

-- 3. Clé étrangère lors de création
CREATE TABLE DetailsCommande (
    DetailID INT IDENTITY(1,1) PRIMARY KEY,
    CommandeID INT NOT NULL,
    ProduitID INT NOT NULL,
    Quantite INT,
    CONSTRAINT FK_Details_Commandes 
        FOREIGN KEY (CommandeID) REFERENCES Commandes(CommandeID),
    CONSTRAINT FK_Details_Produits 
        FOREIGN KEY (ProduitID) REFERENCES Produits(ProduitID)
);
10.2 Contraintes CHECK et DEFAULT

-- 1. Contrainte CHECK
ALTER TABLE Produits
ADD CONSTRAINT CHK_Prix_Positif 
CHECK (Prix > 0);

ALTER TABLE Commandes
ADD CONSTRAINT CHK_Quantite_Positive 
CHECK (Quantite > 0);

ALTER TABLE Clients
ADD CONSTRAINT CHK_Email_Format 
CHECK (Email LIKE '%@%.%');

-- 2. Valeur par défaut
ALTER TABLE Clients
ADD CONSTRAINT DF_Clients_DateInscription 
DEFAULT GETDATE() FOR DateInscription;

ALTER TABLE Produits
ADD CONSTRAINT DF_Produits_DateAjout 
DEFAULT GETDATE() FOR DateAjout;

-- 3. Contrainte UNIQUE
ALTER TABLE Clients
ADD CONSTRAINT UC_Clients_Email 
UNIQUE (Email);

-- 4. NOT NULL
ALTER TABLE Clients
ALTER COLUMN Email NVARCHAR(100) NOT NULL;
10.3 Triggers (Déclencheurs)

-- 1. Trigger AFTER INSERT
CREATE TRIGGER TR_Commandes_AfterInsert
ON Commandes
AFTER INSERT
AS
BEGIN
    -- Mettre à jour le stock
    UPDATE p
    SET p.Stock = p.Stock - i.Quantite
    FROM Produits p
    INNER JOIN inserted i ON p.ProduitID = i.ProduitID;
    
    PRINT 'Stock mis à jour après insertion commande';
END;
GO

-- 2. Trigger INSTEAD OF
CREATE TRIGGER TR_Clients_InsteadOfDelete
ON Clients
INSTEAD OF DELETE
AS
BEGIN
    PRINT 'Suppression interdite, archivage à la place';
    
    -- Archiver au lieu de supprimer
    INSERT INTO Clients_Archive (ClientID, Nom, Prenom, Ville)
    SELECT ClientID, Nom, Prenom, Ville
    FROM deleted;
END;
GO

-- 3. Désactiver/Activer trigger
DISABLE TRIGGER TR_Commandes_AfterInsert ON Commandes;
ENABLE TRIGGER TR_Commandes_AfterInsert ON Commandes;

-- 4. Voir les triggers
SELECT 
    name,
    object_name(parent_id) AS TableName,
    type_desc
FROM sys.triggers;
👥 Jour 11 : Sécurité et Permissions
11.1 Création d'Utilisateurs et Logins

-- 1. Créer un login (connexion au serveur)
CREATE LOGIN AdminBoutique 
WITH PASSWORD = 'AdminP@ss123!',
     DEFAULT_DATABASE = Boutique,
     CHECK_EXPIRATION = OFF,
     CHECK_POLICY = ON;
GO

CREATE LOGIN Gestionnaire 
WITH PASSWORD = 'GestP@ss456!',
     DEFAULT_DATABASE = Boutique;
GO

-- 2. Créer des utilisateurs dans la base
USE Boutique;
GO

CREATE USER AdminBoutique FOR LOGIN AdminBoutique;
CREATE USER Gestionnaire FOR LOGIN Gestionnaire;
GO
11.2 Attribution de Rôles et Permissions

-- 1. Rôles de base de données
ALTER ROLE db_owner ADD MEMBER AdminBoutique;
ALTER ROLE db_datareader ADD MEMBER Gestionnaire;
ALTER ROLE db_datawriter ADD MEMBER Gestionnaire;

-- 2. Permissions spécifiques
-- Lecture seule sur toutes tables
GRANT SELECT ON DATABASE::Boutique TO Gestionnaire;

-- Permissions sur table spécifique
GRANT SELECT, INSERT, UPDATE ON Clients TO Gestionnaire;
GRANT SELECT ON Produits TO Gestionnaire;

-- Permission d'exécuter procédures
GRANT EXECUTE ON OBJECT::usp_AjouterClient TO Gestionnaire;

-- 3. Rôles personnalisés
CREATE ROLE Role_Gestion_Clients;
GO

GRANT SELECT, INSERT, UPDATE, DELETE ON Clients TO Role_Gestion_Clients;
GRANT SELECT ON Commandes TO Role_Gestion_Clients;
GO

ALTER ROLE Role_Gestion_Clients ADD MEMBER Gestionnaire;
GO

-- 4. Voir les permissions
EXEC sp_helprotect NULL, 'Gestionnaire';
EXEC sp_helprolemember 'Role_Gestion_Clients';

-- 5. Révoquer permissions
REVOKE DELETE ON Clients FROM Gestionnaire;
REVOKE SELECT ON Produits FROM Gestionnaire;

-- 6. Supprimer utilisateurs
DROP USER Gestionnaire;
DROP LOGIN Gestionnaire;
11.3 Vues pour la Sécurité

-- 1. Créer une vue sécurisée
CREATE VIEW Vue_Clients_Public
AS
SELECT 
    ClientID,
    Nom,
    Prenom,
    LEFT(Email, CHARINDEX('@', Email) - 1) + '@***' AS EmailMasque,
    Ville
FROM Clients;
GO

-- 2. Donner accès à la vue
GRANT SELECT ON Vue_Clients_Public TO Gestionnaire;

-- 3. Vue avec vérification d'utilisateur
CREATE VIEW Vue_Mes_Commandes
WITH SCHEMABINDING
AS
SELECT 
    c.CommandeID,
    p.Nom AS Produit,
    c.Quantite,
    c.DateCommande
FROM dbo.Commandes c
JOIN dbo.Produits p ON c.ProduitID = p.ProduitID
WHERE c.ClientID = USER_ID();  -- Seulement les commandes de l'utilisateur
GO
