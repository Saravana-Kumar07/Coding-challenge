# <p align='center'> Crime Management System - Coding Challenge </p>
### Name: Saravana Kumar S
Superset ID: 5371342<br>
College: Saveetha Engineering college

## Database creation and Inserting the given values
```sql
CREATE TABLE Crime ( 
    CrimeID INT PRIMARY KEY, 
    IncidentType VARCHAR(255), 
    IncidentDate DATE, 
    Location VARCHAR(255), 
    Description TEXT, 
    Status VARCHAR(20) 
); 
 
CREATE TABLE Victim ( 
    VictimID INT PRIMARY KEY, 
    CrimeID INT, 
    Name VARCHAR(255), 
    ContactInfo VARCHAR(255), 
    Injuries VARCHAR(255), 
    FOREIGN KEY (CrimeID) REFERENCES Crime(CrimeID) 
); 
 
CREATE TABLE Suspect ( 
    SuspectID INT PRIMARY KEY, 
    CrimeID INT, 
    Name VARCHAR(255), 
    Description TEXT, 
    CriminalHistory TEXT, 
    FOREIGN KEY (CrimeID) REFERENCES Crime(CrimeID) 
); 
 -- Insert sample data 
INSERT INTO Crime (CrimeID, IncidentType, IncidentDate, Location, Description, Status) 
VALUES 
    (1, 'Robbery', '2023-09-15', '123 Main St, Cityville', 'Armed robbery at a convenience store', 'Open'), 
    (2, 'Homicide', '2023-09-20', '456 Elm St, Townsville', 'Investigation into a murder case', 'Under 
Investigation'), 
    (3, 'Theft', '2023-09-10', '789 Oak St, Villagetown', 'Shoplifting incident at a mall', 'Closed'); 
 
INSERT INTO Victim (VictimID, CrimeID, Name, ContactInfo, Injuries) 
VALUES 
    (1, 1, 'John Doe', 'johndoe@example.com', 'Minor injuries'), 
    (2, 2, 'Jane Smith', 'janesmith@example.com', 'Deceased'),
    (3, 3, 'Alice Johnson', 'alicejohnson@example.com', 'None'); 
INSERT INTO Suspect (SuspectID, CrimeID, Name, Description, CriminalHistory) 
VALUES 
(1, 1, 'Robber 1', 'Armed and masked robber', 'Previous robbery convictions'), 
(2, 2, 'Unknown', 'Investigation ongoing', NULL), 
(3, 3, 'Suspect 1', 'Shoplifting suspect', 'Prior shoplifting arrests');
```

## Quries to solve:
### 1. Select all open incidents
```sql
SELECT * FROM Crime WHERE Status = 'Open';
```
<img src="./outputs/o1.png" width="500" />

### 2. Find the total number of incidents
```sql
SELECT COUNT(*) AS TotalIncidents FROM Crime;
```
<img src="./outputs/o2.png" width="200" />

### 3. List all unique incident types
```sql
SELECT DISTINCT IncidentType FROM Crime
```
<img src="./outputs/o3.png" width="300" />

### 4. Retrieve incidents that occurred between '2023-09-01' and '2023-09-10'
```sql
SELECT * FROM Crime WHERE IncidentDate BETWEEN '2023-09-01' AND '2023-09-10';
```
<img src="./outputs/o4.png" width="500" />

### 5. List persons involved in incidents in descending order of age.
- Adding and inserting data for the column age: 
```sql
ALTER TABLE Victim ADD Age INT;

UPDATE Victim SET Age = 28 WHERE VictimId = 1;
UPDATE Victim SET Age = 35 WHERE VictimId = 2;
UPDATE Victim SET Age = 43 WHERE VictimId = 3;

ALTER TABLE Suspect ADD Age INT;

UPDATE Suspect SET Age = 28 WHERE SuspectId = 1;
UPDATE Suspect SET Age = 52 WHERE SuspectId = 2;
UPDATE Suspect SET Age = 39 WHERE SuspectId = 3;
```
- List persons involved in incidents in descending order of age.
```sql
SELECT Name, Age FROM Victim UNION
SELECT Name, Age FROM Suspect
ORDER BY Age DESC;
```
<img src="./outputs/o5.png" width="400" />

### 6. Find the average age of persons involved in incidents
```sql
SELECT AVG(Age) AS average_age FROM (
    SELECT Age FROM Victim UNION
    SELECT Age FROM Suspect
) AS avgage;
```
<img src="./outputs/o6.png" width="300" />

### 7. List incident types and their counts, only for open cases.
```sql
SELECT IncidentType, COUNT(*) AS Totalcount FROM Crime WHERE Status = 'Open' GROUP BY IncidentType;
```
<img src="./outputs/o7.png" width="300" />

### 8. Find persons with names containing 'Doe'.
```sql
SELECT Name FROM Victim WHERE Name LIKE '%Doe%'
```
<img src="./outputs/o8.png" width="200" />

### 9. Retrieve the names of persons involved in open cases and closed cases.
```sql
SELECT Name FROM Victim WHERE CrimeID IN (SELECT CrimeID FROM Crime WHERE Status = 'Open')
UNION
SELECT Name FROM Suspect WHERE CrimeID IN (SELECT CrimeID FROM Crime WHERE Status = 'Open')
UNION
SELECT Name FROM Victim WHERE CrimeID IN (SELECT CrimeID FROM Crime WHERE Status = 'Closed')
UNION
SELECT Name FROM Suspect WHERE CrimeID IN (SELECT CrimeID FROM Crime WHERE Status = 'Closed');
```
<img src="./outputs/o9.png" width="400" />

### 10. List incident types where there are persons aged 30 or 35 involved.
```sql
SELECT DISTINCT C.IncidentType from Crime C
LEFT JOIN Victim V on V.CrimeID=C.CrimeID
where V.age=30 or V.age=35
```
<img src="./outputs/o10.png" width="250" />

### 11. Find persons involved in incidents of the same type as 'Robbery'.
```sql
SELECT Name FROM Victim
WHERE CrimeID IN (SELECT CrimeID FROM Crime WHERE IncidentType = 'Robbery')
UNION
SELECT Name FROM Suspect
WHERE CrimeID IN (SELECT CrimeID FROM Crime WHERE IncidentType = 'Robbery');
```
<img src="./outputs/o11.png" width="300" />

### 12. List incident types with more than one open case.
```sql
SELECT IncidentType, COUNT(*) AS OpenCases FROM Crime
WHERE Status = 'Open' GROUP BY IncidentType
HAVING COUNT(*) > 1;
```
- No data as there are no more than 1 open cases

### 13. List all incidents with suspects whose names also appear as victims in other incidents.
```sql
SELECT C.*, V.Name AS VictimName, S.Name AS SuspectName FROM Crime C
JOIN Victim V ON C.CrimeID = V.CrimeID
JOIN Suspect S ON C.CrimeID = S.CrimeID AND V.Name = S.Name;
```
- No data as there are no such cases.

### 14. Retrieve all incidents along with victim and suspect details
```sql
SELECT C.*, V.Name, V.ContactInfo, V.Injuries, S.Name, S.Description, S.CriminalHistory FROM Crime C
LEFT JOIN Victim V ON C.CrimeID = V.CrimeID
LEFT JOIN Suspect S ON C.CrimeID = S.CrimeID;
```
<img src="./outputs/o12.png" width="600" />

### 15. Find incidents where the suspect is older than any victim.
```sql
SELECT C.* FROM Crime C
JOIN Suspect S ON C.CrimeID = S.CrimeID
WHERE S.Age > ANY (SELECT Age FROM Victim WHERE CrimeID = C.CrimeID);
```
<img src="./outputs/o13.png" width="600" />

### 16. Find suspects involved in multiple incidents:
```sql
SELECT SuspectID, Name, COUNT(CrimeID) AS IncidentCount FROM Suspect
GROUP BY SuspectID, Name
HAVING COUNT(CrimeID) > 1;
```
- No data as there are no such cases.

### 17. List incidents with no suspects involved.
```sql
SELECT C.* FROM Crime C
LEFT JOIN Suspect S ON C.CrimeID = S.CrimeID
WHERE S.Name = 'Unknown';
```
<img src="./outputs/o14.png" width="600" />

### 18. List all cases where at least one incident is of type 'Homicide' and all other incidents are of type 'Robbery'.
```sql
SELECT*FROM Crime C
WHERE C.IncidentType = 'Homicide'
   OR (C.IncidentType = 'Robbery' AND NOT EXISTS (
       SELECT 1 FROM Crime C2
       WHERE C2.CrimeID = C.CrimeID
         AND C2.IncidentType = 'Homicide')
);
```
<img src="./outputs/o15.png" width="600" />

### 19. Retrieve a list of all incidents and the associated suspects, showing suspects for each incident, or 'No Suspect' if there are none. 
```sql
SELECT C.*, IFNULL(S.Name, 'No Suspect') AS SuspectName FROM Crime C
LEFT JOIN Suspect S ON C.CrimeID = S.CrimeID AND S.Name <> 'Unknown';
```
<img src="./outputs/o16.png" width="600" />

### 20. List all suspects who have been involved in incidents with incident types 'Robbery' or 'Assault'  
```sql
SELECT S.* FROM Suspect S
JOIN Crime C ON S.CrimeID = C.CrimeID
WHERE C.IncidentType IN ('Robbery', 'Assault');
```
<img src="./outputs/o17.png" width="600" />


