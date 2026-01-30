-- Customer Table
CREATE TABLE Customer (
    CustomerID NUMBER(5),
    FirstName  VARCHAR2(50) NOT NULL
        CONSTRAINT chk_fname CHECK (FirstName = INITCAP(FirstName)),
    LastName   VARCHAR2(50) NOT NULL
        CONSTRAINT chk_lname CHECK (LastName = INITCAP(LastName)),
    PhoneNo    VARCHAR2(15)
        CONSTRAINT chk_phone CHECK (REGEXP_LIKE(PhoneNo, '^[0-9]{10,15}$')),
    Email      VARCHAR2(50)
        CONSTRAINT chk_email CHECK (REGEXP_LIKE(Email, '^[^@]+@[^@]+\.[^@]+$')),
    Address    VARCHAR2(100),
    CONSTRAINT pk_Customer PRIMARY KEY (CustomerID));

-- Package Table
CREATE TABLE Package (
    PackageID   NUMBER(5),
    PackageName VARCHAR2(50) NOT NULL
        CONSTRAINT chk_packagename CHECK (PackageName = INITCAP(PackageName)),
    Description VARCHAR2(200),
    Price       NUMBER(10,2) NOT NULL
        CONSTRAINT chk_price CHECK (Price > 0),
    CONSTRAINT pk_Package PRIMARY KEY (PackageID));

-- Performer Table
CREATE TABLE Performer (
    PerformerID   NUMBER(5),
    PerformerName VARCHAR2(50) NOT NULL
        CONSTRAINT chk_performername CHECK (PerformerName = INITCAP(PerformerName)),
    PerformerType VARCHAR2(30)
        CONSTRAINT chk_performertype CHECK (PerformerType = INITCAP(PerformerType)),
    HourlyRate    NUMBER(7,2)
        CONSTRAINT chk_hourlyrate CHECK (HourlyRate >= 0),
    ContactNumber VARCHAR2(15)
        CONSTRAINT chk_contactnum CHECK (REGEXP_LIKE(ContactNumber, '^[0-9]{10,15}$')),
    CONSTRAINT pk_Performer PRIMARY KEY (PerformerID));

-- Party_Item Table
CREATE TABLE Party_Item (
    ItemID           NUMBER(5),
    ItemName         VARCHAR2(50) NOT NULL
        CONSTRAINT chk_itemname CHECK (ItemName = INITCAP(ItemName)),
    Category         VARCHAR2(30),
    QuantityAvailable NUMBER(5) NOT NULL
        CONSTRAINT chk_quantity CHECK (QuantityAvailable >= 0),
    DailyRate        NUMBER(7,2)
        CONSTRAINT chk_dailyrate CHECK (DailyRate >= 0),
    CONSTRAINT pk_PartyItem PRIMARY KEY (ItemID));

-- Booking Table
CREATE TABLE Booking (
    BookingID   NUMBER(5),
    BookingDate DATE,
    StartDate   DATE,
    EndDate     DATE,
    TotalAmount NUMBER(10,2) CONSTRAINT chk_total CHECK (TotalAmount >= 0),
    Status      VARCHAR2(20) DEFAULT 'Pending',
    CustomerID  NUMBER(5),
    PackageID   NUMBER(5),
    PerformerID NUMBER(5),
    CONSTRAINT pk_Booking PRIMARY KEY (BookingID),
    CONSTRAINT fk_BookingCustomer FOREIGN KEY (CustomerID) REFERENCES Customer(CustomerID),
    CONSTRAINT fk_BookingPackage FOREIGN KEY (PackageID) REFERENCES Package(PackageID),
    CONSTRAINT fk_BookingPerformer FOREIGN KEY (PerformerID) REFERENCES Performer(PerformerID),
    CONSTRAINT chk_dates CHECK (EndDate >= StartDate));  



-- Booking_Item Table (Composite Primary Key)
CREATE TABLE Booking_Item (
    BookingID    NUMBER(5) NOT NULL,
    ItemID       NUMBER(5) NOT NULL,
    QuantityUsed NUMBER(5) NOT NULL,
    CONSTRAINT chk_quantityused CHECK (QuantityUsed > 0),
    CONSTRAINT pk_BookingItem PRIMARY KEY (BookingID, ItemID),
    CONSTRAINT fk_BItemBooking FOREIGN KEY (BookingID) REFERENCES Booking(BookingID),
    CONSTRAINT fk_BItemItem FOREIGN KEY (ItemID) REFERENCES Party_Item(ItemID));

CREATE TABLE Payment (
    PaymentID NUMBER(5),
    BookingID NUMBER(5) NOT NULL,
    PaymentDate DATE DEFAULT SYSDATE,
    Amount NUMBER(10,2) NOT NULL CONSTRAINT chk_payment_amount CHECK (Amount > 0),
    Method VARCHAR2(20) CONSTRAINT chk_payment_method CHECK (Method IN ('Cash','Card','Bank Transfer','Online')),
    Status VARCHAR2(20) DEFAULT 'Completed'CONSTRAINT chk_payment_status CHECK (Status IN ('Completed','Pending','Failed')),
    CONSTRAINT pk_Payment PRIMARY KEY (PaymentID),
    CONSTRAINT fk_PaymentBooking FOREIGN KEY (BookingID) REFERENCES Booking(BookingID));


-----------------------------------------------------------------------------------------------
-- Insert sample data
INSERT INTO Customer VALUES (1001,'Pawani','Jayasooriya','0771234567','pawanijayasooriyapma@gmail.com','Colombo');
INSERT INTO Customer VALUES (1002,'Nimal','Perera','0712345678','nimalpererampa@gmail.com','Kandy');
INSERT INTO Customer VALUES (1003,'Kamal','Senanayake','0723456789','kamalsenayakakmp@gmail.com','Galle');
INSERT INTO Customer VALUES (1004,'Saman','Fernando','0756789123','samanfernandosf@gmail.com','Jaffna');
INSERT INTO Customer VALUES (1005,'Anusha','Jayasinghe','0767891234','anushajayasinghaaj@gmail.com','Matara');
INSERT INTO Customer VALUES (1006,'Ruwan','Silva','0789123456','ruwansilvars@gmail.com','Gampaha');
INSERT INTO Customer VALUES (1007,'Nadeesha','Perera','0778912345','nadeeshapereranp@gmail.com','Negombo');
INSERT INTO Customer VALUES (1008,'Tharindu','Wijesinghe','0712345987','tharinduwijesinghetw@gmail.com','Batticaloa');

SELECT * FROM Customer;
-------------------------------------------------------------------------------------


INSERT INTO Package VALUES (1,'Birthday Party','Kids birthday celebration',5000);
INSERT INTO Package VALUES (2,'Wedding Party','Wedding event with dinner',15000);
INSERT INTO Package VALUES (3,'Corporate Event','Office annual party',12000);
INSERT INTO Package VALUES (4,'Graduation Party','School graduation party',8000);
INSERT INTO Package VALUES (5,'Anniversary','Wedding anniversary celebration',7000);
INSERT INTO Package VALUES (6,'Engagement','Engagement ceremony',6000);
INSERT INTO Package VALUES (7,'Baby Shower','Baby welcoming party',4000);
INSERT INTO Package VALUES (8,'Farewell Party','Farewell for colleague',4500);

SELECT * FROM Package;
-------------------------------------------------------------------------------
INSERT INTO Performer VALUES (1,'Sunil Perera','Singer',5000,'0771234567');
INSERT INTO Performer VALUES (2,'Rohan Fernando','DJ',3000,'0712345678');
INSERT INTO Performer VALUES (3,'Kumara Silva','Magician',4000,'0723456789');
INSERT INTO Performer VALUES (4,'Nirosha Perera','Dancer',3500,'0756789123');
INSERT INTO Performer VALUES (5,'Chamika Jayasinghe','Comedian',2000,'0767891234');
INSERT INTO Performer VALUES (6,'Sanduni Wijesinghe','Musician',2500,'0789123456');
INSERT INTO Performer VALUES (7,'Ravindu Silva','Singer',3000,'0778912345');
INSERT INTO Performer VALUES (8,'Shanika Perera','DJ',2800,'0712345987');

SELECT * FROM Performer;
-------------------------------------------------------------------------------


INSERT INTO Party_Item VALUES (1,'Chair','Furniture',50,100);
INSERT INTO Party_Item VALUES (2,'Table','Furniture',30,200);
INSERT INTO Party_Item VALUES (3,'Speaker','Electronics',20,500);
INSERT INTO Party_Item VALUES (4,'Decoration Set','Decor',15,1000);
INSERT INTO Party_Item VALUES (5,'Stage Light','Electronics',10,800);
INSERT INTO Party_Item VALUES (6,'Balloon Pack','Decor',100,50);
INSERT INTO Party_Item VALUES (7,'Sound System','Electronics',5,1200);
INSERT INTO Party_Item VALUES (8,'Flower Arrangements','Decor',25,400);

SELECT * FROM Party_Item;
-------------------------------------------------------------------------------
INSERT INTO Booking VALUES (1,TO_DATE('2025-12-01','YYYY-MM-DD'),TO_DATE('2025-12-05','YYYY-MM-DD'),TO_DATE('2025-12-05','YYYY-MM-DD'),5000,'Confirmed',1,1,1);
INSERT INTO Booking VALUES (2,TO_DATE('2025-12-02','YYYY-MM-DD'),TO_DATE('2025-12-10','YYYY-MM-DD'),TO_DATE('2025-12-10','YYYY-MM-DD'),15000,'Pending',2,2,2);
INSERT INTO Booking VALUES (3,TO_DATE('2025-12-03','YYYY-MM-DD'),TO_DATE('2025-12-12','YYYY-MM-DD'),TO_DATE('2025-12-12','YYYY-MM-DD'),12000,'Confirmed',3,3,3);
INSERT INTO Booking VALUES (4,TO_DATE('2025-12-04','YYYY-MM-DD'),TO_DATE('2025-12-15','YYYY-MM-DD'),TO_DATE('2025-12-15','YYYY-MM-DD'),8000,'Pending',4,4,4);
INSERT INTO Booking VALUES (5,TO_DATE('2025-12-05','YYYY-MM-DD'),TO_DATE('2025-12-20','YYYY-MM-DD'),TO_DATE('2025-12-20','YYYY-MM-DD'),7000,'Confirmed',5,5,5);
INSERT INTO Booking VALUES (6,TO_DATE('2025-12-06','YYYY-MM-DD'),TO_DATE('2025-12-22','YYYY-MM-DD'),TO_DATE('2025-12-22','YYYY-MM-DD'),6000,'Pending',6,6,6);
INSERT INTO Booking VALUES (7,TO_DATE('2025-12-07','YYYY-MM-DD'),TO_DATE('2025-12-25','YYYY-MM-DD'),TO_DATE('2025-12-25','YYYY-MM-DD'),4000,'Confirmed',7,7,7);
INSERT INTO Booking VALUES (8,TO_DATE('2025-12-08','YYYY-MM-DD'),TO_DATE('2025-12-30','YYYY-MM-DD'),TO_DATE('2025-12-30','YYYY-MM-DD'),4500,'Pending',8,8,8);

SELECT * FROM Booking;
---------------------------------------------------------------------------------

INSERT INTO Booking_Item VALUES (1,1,10);
INSERT INTO Booking_Item VALUES (1,2,5);
INSERT INTO Booking_Item VALUES (2,3,2);
INSERT INTO Booking_Item VALUES (2,4,1);
INSERT INTO Booking_Item VALUES (3,1,15);
INSERT INTO Booking_Item VALUES (3,5,3);
INSERT INTO Booking_Item VALUES (4,6,20);
INSERT INTO Booking_Item VALUES (5,7,1);
INSERT INTO Booking_Item VALUES (6,8,5);
INSERT INTO Booking_Item VALUES (7,2,10);
INSERT INTO Booking_Item VALUES (8,3,2);

SELECT * FROM Booking_Item;

INSERT INTO Payment VALUES (101,1,TO_DATE('2025-12-01','YYYY-MM-DD'),5000,'Card','Completed');
INSERT INTO Payment VALUES (102,2,TO_DATE('2025-12-02','YYYY-MM-DD'),15000,'Cash','Completed');
INSERT INTO Payment VALUES (103,3,TO_DATE('2025-12-03','YYYY-MM-DD'),12000,'Online','Pending');
INSERT INTO Payment VALUES (104,4,TO_DATE('2025-12-04','YYYY-MM-DD'),8000,'Cash','Completed');
INSERT INTO Payment VALUES (105,5,TO_DATE('2025-12-05','YYYY-MM-DD'),7000,'Card','Completed');
INSERT INTO Payment VALUES (106,6,TO_DATE('2025-12-06','YYYY-MM-DD'),6000,'Online','Pending');
INSERT INTO Payment VALUES (107,7,TO_DATE('2025-12-07','YYYY-MM-DD'),4000,'Bank Transfer','Completed');
INSERT INTO Payment VALUES (108,8,TO_DATE('2025-12-08','YYYY-MM-DD'),4500,'Cash','Completed');

SELECT * FROM Payment;



