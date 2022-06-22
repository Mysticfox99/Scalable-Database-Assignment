# create a database

DROP DATABASE IF EXISTS deliveryDB;
CREATE DATABASE IF NOT EXISTS deliveryDB;

#create a table for the driver

CREATE TABLE driver (
    driverID INT AUTO_INCREMENT PRIMARY KEY,
    lastName VARCHAR(20) NOT NULL,
    firstName VARCHAR(20) NOT NULL
);

#create a table for the vehicle

CREATE TABLE vehicle (
    vehicleID VARCHAR(10) NOT NULL PRIMARY KEY,
	make VARCHAR(20) NOT NULL,
	model VARCHAR(20) NOT NULL,
	year INT(4) NOT NULL
);

#create a table for type

CREATE TABLE type(
	shifttype VARCHAR(10) NOT NULL PRIMARY KEY,
	starttime TIME NOT NULL,
	endtime TIME NOT NULL
);


#create a table for the shift

CREATE TABLE shift (
    shiftID INT AUTO_INCREMENT PRIMARY KEY,
	driverID INT(10) NOT NULL,
	vehicleID VARCHAR(10) NOT NULL,
	shiftday DATE NOT NULL,
	shifttype VARCHAR(10) NOT NULL,
	FOREIGN KEY (driverID) REFERENCES driver(driverID),
	FOREIGN KEY (vehicleID) REFERENCES vehicle(vehicleID),
	FOREIGN KEY (shifttype) REFERENCES type(shifttype)
); 

#create a table for the GPS

CREATE TABLE gps (
    gpsID INT AUTO_INCREMENT PRIMARY KEY,
	shiftID INT(10) NOT NULL,
	time TIME NOT NULL,
	FOREIGN KEY (shiftID) REFERENCES shift(shiftID)
);

#create a table for the parcel

CREATE TABLE parcel (
    parcelID INT AUTO_INCREMENT PRIMARY KEY,
	shiftID INT NOT NULL,
	FOREIGN KEY (shiftID) REFERENCES shift(shiftID)
);

#alter the gps table
ALTER TABLE gps
ADD latitude DECIMAL(10, 8) NOT NULL,
ADD longitude DECIMAL(11, 8) NOT NULL;

#insert data into the driver table

INSERT INTO driver (lastName,firstName)
VALUES ('Exeter','Tiffanie'),
	   ('Falconar','Any'),
	   ('Childes','Aili'),
	   ('Scothorn','Harry'),
	   ('Lambirth','Drucie'),
	   ('Colnett','Thatch'),
	   ('Lorincz','Lem'),
	   ('Rapps','Lennard'),
	   ('Duddin','Bud'),
	   ('Capstake','Agnella');

#insert data into vehicle

INSERT INTO vehicle (vehicleID, make, model, year)
VALUES ('LD11 AGS','Mercedes','Sprinter','2011'),
		('KN19 DUN','Volkswagen','Caddy','2019'),
		('DU15 AGV','Peugeot','Partner','2015'),
		('YD05 ISD','Ford','Transit','2005'),
		('VF07 KSU','Vauxhall','Vivaro','2007');

#insert data into type	   

INSERT INTO type (shifttype,starttime,endtime)
VALUES ('morning','08:00:00','11:55:00'),
	   ('afternoon','12:00:00','16:00:00');

#insert data into shift	
   
INSERT INTO shift (driverID,vehicleID,shiftday,shifttype)
VALUES (1, 'DU15 AGV', '2021-01-27', 'morning'),
	   (2, 'LD11 AGS', '2021-01-22', 'morning' ),
	   (3, 'VF07 KSU', '2021-01-28', 'afternoon'),
	   (4, 'YD05 ISD', '2021-01-28', 'afternoon'),
	   (5, 'KN19 DUN', '2021-01-24', 'morning'),
	   (6, 'DU15 AGV', '2021-01-23', 'afternoon'),
	   (7, 'LD11 AGS', '2021-01-28', 'afternoon'),
	   (8, 'KN19 DUN', '2021-01-27', 'afternoon'),
	   (9, 'YD05 ISD', '2021-01-22', 'morning'),
	   (10, 'DU15 AGV', '2021-01-24', 'afternoon'),
	   (5, 'KN19 DUN', '2021-01-26', 'afternoon'),
	   (7, 'VF07 KSU', '2021-01-25', 'afternoon'),
	   (3, 'LD11 AGS', '2021-01-27', 'morning'),
	   (1, 'YD05 ISD', '2021-01-26', 'afternoon'),
	   (7, 'VF07 KSU', '2021-01-25', 'morning');

#insert data into gps	

INSERT INTO gps (shiftID,latitude,longitude,time)
VALUES (4,48.5851876, 7.7342943, '13:00:00'),
	   (3,54.11538, 20.1302301, '12:00:00'),
       (15,19.6092712, -72.2134308, '10:00:00'),
	   (2,30.723066, 121.245717, '09:00:00'),
	   (14,-7.823074, 110.1576731, '14:00:00'),
	   (3,-22.9098833, -47.0625812, '15:00:00'),
       (8,14.5716986, 121.026941, '13:00:00'),
	   (7,-2.3549763, 115.5088914, '12:00:00'),
	   (5,-7.5605, 108.2573, '11:00:00'),
	   (11,60.0456385, 30.3403569, '13:00:00'),
	   (2,23.044969, 113.398429, '08:00:00'),
	   (1,51.6571864, 35.6783297, '11:00:00'),
	   (13,-37.052608, -69.880879, '09:00:00'),
	   (12,-20.0511394, 30.5106372, '15:00:00'),
	   (4,0.5948949, -77.8281514, '14:00:00');

#insert data into parcel

INSERT INTO parcel (shiftID)
VALUES (3),
	   (4),
	   (7),
	   (12),
	   (1),
	   (7),
	   (6),
	   (4),
	   (13),
	   (1),
	   (5),
	   (9),
	   (10),
	   (10),
	   (3);

#Query 4.1
SELECT gps.gpsID, s.shiftday, s.vehicleID, d.driverID, d.firstName, d.lastName, gps.time, gps.latitude, gps.longitude
FROM shift AS s
JOIN driver AS d
ON d.driverID = s.driverID
JOIN gps 
ON gps.shiftID = s.shiftID
WHERE gps.time = '13:00:00' AND s.shiftday = '2021-01-28';

#Query 4.2
SELECT s.shiftID, d.driverID, d.firstName, d.lastName, COUNT(p.parcelID) AS parceldelivered
FROM shift AS s
JOIN driver AS d
ON d.driverID = s.driverID
JOIN parcel as p
ON p.shiftID = s.shiftID
WHERE s.shiftID = 11;

#Query 4.3
SELECT * FROM driver;

#Query 4.4
SELECT d.driverID, d.firstName, d.lastName, s.shifttype
FROM driver AS d
JOIN shift AS s
ON d.driverID = s.driverID
WHERE s.driverID NOT IN(SELECT s.driverID FROM shift AS s WHERE s.shifttype != 'morning');

#Procedure for query 4.2

DELIMITER //   
CREATE PROCEDURE parceldelivered( IN driver INT, OUT shiftID INT,OUT driverID INT, OUT FullName VARCHAR(25), OUT TotalDeliveries INT)
BEGIN
	SELECT s.shiftID, d.driverID,CONCAT(d.firstName,' ',d.lastName), COUNT(p.parcelID)
	INTO shiftID, driverID, FullName, TotalDeliveries
	FROM shift AS s
		JOIN driver AS d
		ON d.driverID = s.driverID
		JOIN parcel as p
		ON p.shiftID = s.shiftID
		WHERE s.shiftID = driver;
END //
DELIMITER ;

#Procedure for query 4.4

DELIMITER //
CREATE PROCEDURE getmorning(IN typeshift VARCHAR(25))
BEGIN
	SELECT d.driverID, d.firstName, d.lastName, s.shifttype
	FROM driver AS d
	JOIN shift AS s
	ON d.driverID = s.driverID
	WHERE s.driverID NOT IN(SELECT s.driverID FROM shift AS s WHERE s.shifttype != typeshift);
END //
DELIMITER ;

#Call the procedures

CALL parceldelivered(3, @shiftID,@driverID,@FullName, @TotalDeliveries);
SELECT @shiftID, @driverID,@FullName, @TotalDeliveries;

CALL getmorning('morning');
