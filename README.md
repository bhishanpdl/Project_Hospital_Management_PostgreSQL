Table of Contents
=================
   * [Database Creation](#database-creation)
   * [Using SQL Queries in jupyter Notebook](#using-sql-queries-in-jupyter-notebook)
   * [SQL Queries](#sql-queries)
   * [Script to create tables](#script-to-create-tables)
   * [SQL Queries](#sql-queries-1)
      * [Obtain the names of all physicians that have performed a medical procedure they have never been certified to perform](#obtain-the-names-of-all-physicians-that-have-performed-a-medical-procedure-they-have-never-been-certified-to-perform)
      * [Same as the previous query, but include the following information in the results: Physician name, name of procedure, date when the procedure was carried out, name of the patient the procedure was carried out on.](#same-as-the-previous-query-but-include-the-following-information-in-the-results-physician-name-name-of-procedure-date-when-the-procedure-was-carried-out-name-of-the-patient-the-procedure-was-carried-out-on)
      * [Obtain the names of all physicians that have performed a medical procedure that they are certified to perform, but such that the procedure was done at a date (Undergoes.Date) after the physician's certification expired (Trained_In.CertificationExpires)](#obtain-the-names-of-all-physicians-that-have-performed-a-medical-procedure-that-they-are-certified-to-perform-but-such-that-the-procedure-was-done-at-a-date-undergoesdate-after-the-physicians-certification-expired-trained_incertificationexpires)
      * [Same as the previous query, but include the following information in the results: Physician name, name of procedure, date when the procedure was carried out, name of the patient the procedure was carried out on, and date when the certification expired.](#same-as-the-previous-query-but-include-the-following-information-in-the-results-physician-name-name-of-procedure-date-when-the-procedure-was-carried-out-name-of-the-patient-the-procedure-was-carried-out-on-and-date-when-the-certification-expired)
      * [Obtain the information for appointments where a patient met with a physician other than his/her primary care physician. Show the following information: Patient name, physician name, nurse name (if any), start and end time of appointment, examination room, and the name of the patient's primary care physician.](#obtain-the-information-for-appointments-where-a-patient-met-with-a-physician-other-than-hisher-primary-care-physician-show-the-following-information-patient-name-physician-name-nurse-name-if-any-start-and-end-time-of-appointment-examination-room-and-the-name-of-the-patients-primary-care-physician)
      * [The Patient field in Undergoes is redundant, since we can obtain it from the Stay table. There are no constraints in force to prevent inconsistencies between these two tables. More specifically, the Undergoes table may include a row where the patient ID does not match the one we would obtain from the Stay table through the Undergoes.Stay foreign key. Select all rows from Undergoes that exhibit this inconsistency.](#the-patient-field-in-undergoes-is-redundant-since-we-can-obtain-it-from-the-stay-table-there-are-no-constraints-in-force-to-prevent-inconsistencies-between-these-two-tables-more-specifically-the-undergoes-table-may-include-a-row-where-the-patient-id-does-not-match-the-one-we-would-obtain-from-the-stay-table-through-the-undergoesstay-foreign-key-select-all-rows-from-undergoes-that-exhibit-this-inconsistency)
      * [Obtain the names of all the nurses who have ever been on call for room 123.](#obtain-the-names-of-all-the-nurses-who-have-ever-been-on-call-for-room-123)
      * [The hospital has several examination rooms where appointments take place. Obtain the number of appointments that have taken place in each examination room.](#the-hospital-has-several-examination-rooms-where-appointments-take-place-obtain-the-number-of-appointments-that-have-taken-place-in-each-examination-room)
      * [Obtain the names of all patients (also include, for each patient, the name of the patient's primary care physician), such that all the following are true](#obtain-the-names-of-all-patients-also-include-for-each-patient-the-name-of-the-patients-primary-care-physician-such-that-all-the-following-are-true)


# Database Creation
In this project, I have recreated the hospital management database in postgresql
flavor of SQL from mySQL flavor of [wikibooks](https://en.wikibooks.org/wiki/SQL_Exercises/The_Hospital). The database has following 15 tables:

1. Physician
1. Department
1. Affiliated_With
1. Procedure
1. Trained_In
1. Patient
1. Nurse
1. Appointment
1. Medication
1. Prescribes
1. Block
1. Room
1. On_Call
1. Stay
1. Undergoes

The entity relation is given below:
![](images/ER_the_hospital.png)

# Using SQL Queries in jupyter Notebook
- First we need to install postgresql and its official app [pgAdmin](https://www.pgadmin.org).
- Then we need to install sql magics in one of the conda environment. I have a
conda environment `spk` for that purpose and I have installed `sqlachemy` module there.

# SQL Queries

1. Obtain the names of all physicians that have performed a medical procedure they have never been certified to perform.

1. Same as the previous query, but include the following information in the results: Physician name, name of procedure, date when the procedure was carried out, name of the patient the procedure was carried out on.

1. Obtain the names of all physicians that have performed a medical procedure that they are certified to perform, but such that the procedure was done at a date (Undergoes.Date) after the physician's certification expired (Trained_In.CertificationExpires).

1. Same as the previous query, but include the following information in the results: Physician name, name of procedure, date when the procedure was carried out, name of the patient the procedure was carried out on, and date when the certification expired.

1. Obtain the information for appointments where a patient met with a physician other than his/her primary care physician. Show the following information: Patient name, physician name, nurse name (if any), start and end time of appointment, examination room, and the name of the patient's primary care physician.

1. The Patient field in Undergoes is redundant, since we can obtain it from the Stay table. There are no constraints in force to prevent inconsistencies between these two tables. More specifically, the Undergoes table may include a row where the patient ID does not match the one we would obtain from the Stay table through the Undergoes.Stay foreign key. Select all rows from Undergoes that exhibit this inconsistency.

1. The hospital has several examination rooms where appointments take place. Obtain the number of appointments that have taken place in each examination room.

1. Obtain the names of all patients (also include, for each patient, the name of the patient's primary care physician), such that all the following are true.

# Script to create tables
```sql
CREATE TABLE Physician (
  EmployeeID INTEGER PRIMARY KEY NOT NULL,
  Name TEXT NOT NULL,
  Position TEXT NOT NULL,
  SSN INTEGER NOT NULL
);

CREATE TABLE Department (
  DepartmentID INTEGER PRIMARY KEY NOT NULL,
  Name TEXT NOT NULL,
  Head INTEGER NOT NULL
    CONSTRAINT fk_Physician_EmployeeID REFERENCES Physician(EmployeeID)
);

CREATE TABLE Affiliated_With (
  Physician INTEGER NOT NULL
    CONSTRAINT fk_Physician_EmployeeID REFERENCES Physician(EmployeeID),
  Department INTEGER NOT NULL
    CONSTRAINT fk_Department_DepartmentID REFERENCES Department(DepartmentID),
  PrimaryAffiliation BOOLEAN NOT NULL,
  PRIMARY KEY(Physician, Department)
);

CREATE TABLE Procedure (
  Code INTEGER PRIMARY KEY NOT NULL,
  Name TEXT NOT NULL,
  Cost REAL NOT NULL
);

CREATE TABLE Trained_In (
  Physician INTEGER NOT NULL
    CONSTRAINT fk_Physician_EmployeeID REFERENCES Physician(EmployeeID),
  Treatment INTEGER NOT NULL
    CONSTRAINT fk_Procedure_Code REFERENCES Procedure(Code),
  CertificationDate DATETIME NOT NULL,
  CertificationExpires DATETIME NOT NULL,
  PRIMARY KEY(Physician, Treatment)
);

CREATE TABLE Patient (
  SSN INTEGER PRIMARY KEY NOT NULL,
  Name TEXT NOT NULL,
  Address TEXT NOT NULL,
  Phone TEXT NOT NULL,
  InsuranceID INTEGER NOT NULL,
  PCP INTEGER NOT NULL
    CONSTRAINT fk_Physician_EmployeeID REFERENCES Physician(EmployeeID)
);

CREATE TABLE Nurse (
  EmployeeID INTEGER PRIMARY KEY NOT NULL,
  Name TEXT NOT NULL,
  Position TEXT NOT NULL,
  Registered BOOLEAN NOT NULL,
  SSN INTEGER NOT NULL
);

CREATE TABLE Appointment (
  AppointmentID INTEGER PRIMARY KEY NOT NULL,
  Patient INTEGER NOT NULL
    CONSTRAINT fk_Patient_SSN REFERENCES Patient(SSN),
  PrepNurse INTEGER
    CONSTRAINT fk_Nurse_EmployeeID REFERENCES Nurse(EmployeeID),
  Physician INTEGER NOT NULL
    CONSTRAINT fk_Physician_EmployeeID REFERENCES Physician(EmployeeID),
  Start DATETIME NOT NULL,
  End DATETIME NOT NULL,
  ExaminationRoom TEXT NOT NULL
);

CREATE TABLE Medication (
  Code INTEGER PRIMARY KEY NOT NULL,
  Name TEXT NOT NULL,
  Brand TEXT NOT NULL,
  Description TEXT NOT NULL
);

CREATE TABLE Prescribes (
  Physician INTEGER NOT NULL
    CONSTRAINT fk_Physician_EmployeeID REFERENCES Physician(EmployeeID),
  Patient INTEGER NOT NULL
    CONSTRAINT fk_Patient_SSN REFERENCES Patient(SSN),
  Medication INTEGER NOT NULL
    CONSTRAINT fk_Medication_Code REFERENCES Medication(Code),
  Date DATETIME NOT NULL,
  Appointment INTEGER
    CONSTRAINT fk_Appointment_AppointmentID REFERENCES Appointment(AppointmentID),
  Dose TEXT NOT NULL,
  PRIMARY KEY(Physician, Patient, Medication, Date)
);

CREATE TABLE Block (
  Floor INTEGER NOT NULL,
  Code INTEGER NOT NULL,
  PRIMARY KEY(Floor, Code)
);

CREATE TABLE Room (
  Number INTEGER PRIMARY KEY NOT NULL,
  Type TEXT NOT NULL,
  BlockFloor INTEGER NOT NULL
    CONSTRAINT fk_Block_Floor REFERENCES Block(Floor),
  BlockCode INTEGER NOT NULL
    CONSTRAINT fk_Block_Code REFERENCES Block(Code),
  Unavailable BOOLEAN NOT NULL
);

CREATE TABLE On_Call (
  Nurse INTEGER NOT NULL
    CONSTRAINT fk_Nurse_EmployeeID REFERENCES Nurse(EmployeeID),
  BlockFloor INTEGER NOT NULL
    CONSTRAINT fk_Block_Floor REFERENCES Block(Floor),
  BlockCode INTEGER NOT NULL
    CONSTRAINT fk_Block_Code REFERENCES Block(Code),
  Start DATETIME NOT NULL,
  End DATETIME NOT NULL,
  PRIMARY KEY(Nurse, BlockFloor, BlockCode, Start, End)
);

CREATE TABLE Stay (
  StayID INTEGER PRIMARY KEY NOT NULL,
  Patient INTEGER NOT NULL
    CONSTRAINT fk_Patient_SSN REFERENCES Patient(SSN),
  Room INTEGER NOT NULL
    CONSTRAINT fk_Room_Number REFERENCES Room(Number),
  Start DATETIME NOT NULL,
  End DATETIME NOT NULL
);

CREATE TABLE Undergoes (
  Patient INTEGER NOT NULL
    CONSTRAINT fk_Patient_SSN REFERENCES Patient(SSN),
  Procedure INTEGER NOT NULL
    CONSTRAINT fk_Procedure_Code REFERENCES Procedure(Code),
  Stay INTEGER NOT NULL
    CONSTRAINT fk_Stay_StayID REFERENCES Stay(StayID),
  Date DATETIME NOT NULL,
  Physician INTEGER NOT NULL
    CONSTRAINT fk_Physician_EmployeeID REFERENCES Physician(EmployeeID),
  AssistingNurse INTEGER
    CONSTRAINT fk_Nurse_EmployeeID REFERENCES Nurse(EmployeeID),
  PRIMARY KEY(Patient, Procedure, Stay, Date)
);
```

# SQL Queries

## Obtain the names of all physicians that have performed a medical procedure they have never been certified to perform

```sql
-- method 01
SELECT Name
  FROM Physician
 WHERE EmployeeID IN
       (
         SELECT Physician FROM Undergoes U WHERE NOT EXISTS
            (
               SELECT * FROM Trained_In
                WHERE Treatment = Procedure
                  AND Physician = U.Physician
            )
       );

-- another method
SELECT P.Name FROM
 Physician AS P,
 (SELECT Physician, Procedure FROM Undergoes
    EXCEPT
    SELECT Physician, Treatment FROM Trained_in) AS Pe
 WHERE P.EmployeeID=Pe.Physician

-- another method
SELECT Name
  FROM Physician
 WHERE EmployeeID IN
   (
      SELECT Undergoes.Physician
        FROM Undergoes
             LEFT JOIN Trained_In
             ON Undergoes.Physician=Trained_In.Physician
                 AND Undergoes.Procedure=Trained_In.Treatment
       WHERE Treatment IS NULL
   )
```

## Same as the previous query, but include the following information in the results: Physician name, name of procedure, date when the procedure was carried out, name of the patient the procedure was carried out on.

```sql
SELECT P.Name AS Physician, Pr.Name AS Procedure, U.Date, Pt.Name AS Patient
  FROM Physician P, Undergoes U, Patient Pt, Procedure Pr
  WHERE U.Patient = Pt.SSN
    AND U.Procedure = Pr.Code
    AND U.Physician = P.EmployeeID
    AND NOT EXISTS
              (
                SELECT * FROM Trained_In T
                WHERE T.Treatment = U.Procedure
                AND T.Physician = U.Physician
              );

-- another method
SELECT P.Name,Pr.Name,U.Date,Pt.Name FROM
 Physician AS P,
 Procedure AS Pr,
 Undergoes AS U,
 Patient AS Pt,
 (SELECT Physician, Procedure FROM Undergoes
    EXCEPT
    SELECT Physician, Treatment FROM Trained_in) AS Pe
 WHERE P.EmployeeID=Pe.Physician AND Pe.Procedure=Pr.Code AND Pe.Physician=U.Physician AND Pe.Procedure=U.Procedure AND U.Patient=Pt.SSN
```

## Obtain the names of all physicians that have performed a medical procedure that they are certified to perform, but such that the procedure was done at a date (Undergoes.Date) after the physician's certification expired (Trained_In.CertificationExpires)

```sql
SELECT Name
  FROM Physician
 WHERE EmployeeID IN
       (
         SELECT Physician FROM Undergoes U
          WHERE Date >
               (
                  SELECT CertificationExpires
                    FROM Trained_In T
                   WHERE T.Physician = U.Physician
                     AND T.Treatment = U.Procedure
               )
       );

-- another method
SELECT P.Name FROM
 Physician AS P,
 Trained_In T,
 Undergoes AS U
 WHERE T.Physician=U.Physician AND T.Treatment=U.Procedure AND U.Date>T.CertificationExpires AND P.EmployeeID=U.Physician

```

## Same as the previous query, but include the following information in the results: Physician name, name of procedure, date when the procedure was carried out, name of the patient the procedure was carried out on, and date when the certification expired.

```sql
SELECT P.Name AS Physician, Pr.Name AS Procedure, U.Date, Pt.Name AS Patient, T.CertificationExpires
  FROM Physician P, Undergoes U, Patient Pt, Procedure Pr, Trained_In T
  WHERE U.Patient = Pt.SSN
    AND U.Procedure = Pr.Code
    AND U.Physician = P.EmployeeID
    AND Pr.Code = T.Treatment
    AND P.EmployeeID = T.Physician
    AND U.Date > T.CertificationExpires
```

## Obtain the information for appointments where a patient met with a physician other than his/her primary care physician. Show the following information: Patient name, physician name, nurse name (if any), start and end time of appointment, examination room, and the name of the patient's primary care physician.

```sql
SELECT Pt.Name AS Patient, Ph.Name AS Physician, N.Name AS Nurse, A.Start, A.End, A.ExaminationRoom, PhPCP.Name AS PCP
  FROM Patient Pt, Physician Ph, Physician PhPCP, Appointment A LEFT JOIN Nurse N ON A.PrepNurse = N.EmployeeID
 WHERE A.Patient = Pt.SSN
   AND A.Physician = Ph.EmployeeID
   AND Pt.PCP = PhPCP.EmployeeID
   AND A.Physician <> Pt.PCP;
```

## The Patient field in Undergoes is redundant, since we can obtain it from the Stay table. There are no constraints in force to prevent inconsistencies between these two tables. More specifically, the Undergoes table may include a row where the patient ID does not match the one we would obtain from the Stay table through the Undergoes.Stay foreign key. Select all rows from Undergoes that exhibit this inconsistency.

```sql
SELECT * FROM Undergoes U
 WHERE Patient <>
   (
     SELECT Patient FROM Stay S
      WHERE U.Stay = S.StayID
   );
```

## Obtain the names of all the nurses who have ever been on call for room 123.

```sql
SELECT N.Name FROM Nurse N
 WHERE EmployeeID IN
   (
     SELECT OC.Nurse FROM On_Call OC, Room R
      WHERE OC.BlockFloor = R.BlockFloor
        AND OC.BlockCode = R.BlockCode
        AND R.Number = 123
   );
```

## The hospital has several examination rooms where appointments take place. Obtain the number of appointments that have taken place in each examination room.

```sql
SELECT ExaminationRoom, COUNT(AppointmentID) AS Number FROM Appointment
GROUP BY ExaminationRoom;
```

## Obtain the names of all patients (also include, for each patient, the name of the patient's primary care physician), such that all the following are true

```sql
SELECT Pt.Name, PhPCP.Name FROM Patient Pt, Physician PhPCP
 WHERE Pt.PCP = PhPCP.EmployeeID
   AND EXISTS
       (
         SELECT * FROM Prescribes Pr
          WHERE Pr.Patient = Pt.SSN
            AND Pr.Physician = Pt.PCP
       )
   AND EXISTS
       (
         SELECT * FROM Undergoes U, Procedure Pr
          WHERE U.Procedure = Pr.Code
            AND U.Patient = Pt.SSN
            AND Pr.Cost > 5000
       )
   AND 2 <=
       (
         SELECT COUNT(A.AppointmentID) FROM Appointment A, Nurse N
          WHERE A.PrepNurse = N.EmployeeID
            AND N.Registered = 1
       )
   AND NOT Pt.PCP IN
       (
          SELECT Head FROM Department
       );
```
