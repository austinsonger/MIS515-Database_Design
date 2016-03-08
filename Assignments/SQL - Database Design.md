Austin Vern Songer
==================
SQL - Database Design
---------------

---

----

Patient (**patientNo**, patName, patAddr, DOB)
Primary Key **patientNo**

----

Ward (**wardNo**, wardName, wardType, noOfBeds)
Primary Key **wardNo**

----

Contains (**patientNo**, **wardNo**, **admissionDate**)
Primary Key **patientNo**
Foreign Key **wardNo**
Alternate Key **admissionDate**

----

Drug (**drugNo**, drugName, costPerUnit)
Primary Key **drugNo**

----

Prescribed (**patientNo**, **drugNo**, unitsPerDay, **startDate**, finishDate)
Primary Key **patientNo**
Foreign Key **drugNo**
Alternate Key **startDate**



----



(1) List all the patients’ details, alphabetically by name.

    SELECT * 
    FROM Patient 
    ORDER BY patName


(2) List all the patients contained in the ‘Surgical’ ward.

    SELECT p.patientNo, p.patName 
    FROM Patient p, Ward w, Contains c 
    WHERE w.wardNo = c.wardNo AND c.patientNo = p.patientNo 
	AND  wardName= ‘Surgical’


(3) List all the patients admitted today.

    SELECT p.patientNo, p.patName  
    FROM Patient p, Contains c 
    WHERE c.patientNo = p.patientNo AND admissionDate = ‘today’
 

(4) Find the names of all the patients being prescribed ‘Morphine’.

    SELECT p.patName 
    FROM Patient p, Prescribed pr, Drug d 
    WHERE pr.patientNo = p.patientNo AND pr.drugNo = d.drugNo AND  drugName = ‘Morphine’


(5) List each patient that was admitted in August 2011.

    SELECT patientNo 
    FROM Contains
    WHERE admissionDate =  '2011'


(6) List each patient, every drug they are prescribed and the cost per day of that drug.


    SELECT patName , patientNo, drugNo, unitPerDay
    FROM Patient p , Prescribed
 

(7) List each patient, and the cost per day of all drugs they are being prescribed


    


(8) What is the maximum, minimum and average number of beds in a ward? Create appropriate column headings for the results table.

    SELECT MAX(noOfBeds) AS Maximum, MIN(noOfBeds) AS Minimum,  AVG(noOfBeds) AS Average  FROM Ward 
 




(9) For each ward that admitted more than 10 patients today, list the ward number, ward type and number of beds in each ward.


    SELECT w.wardNo, wardType, noOfBeds 
    FROM Patient p, Ward w, Contains c 
    WHERE w.wardNo = c.wardNo AND c.patientNo = p.patientNo AND  
    admissionDate = ‘today’ 
    GROUP BY wardNo, wardType, noOfBeds HAVING COUNT(*) > 10

 
(10) Change the costPerUnit of ‘Morphine’ to $10.00.


    UPDATE Drug
    SET costPerUnit = '$10.00'
    WHERE drugName = 'Morphine'