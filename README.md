# java-DB-datatypes

#### 1. Enum data type usage

- Create enum that will contain statuses 'NEW', "PAID", "WAITING FOR PAYMENT".
```sql
CREATE TYPE payment_status AS ENUM ('NEW', 'PAID' ,'WAITING FOR PAYMENT');
```
- Create table "Invoices" with columns: buyer, seller, value, account number, status. Chose the right column types.
```sql
CREATE TABLE Invoices (buyer VARCHAR(100), seller VARCHAR(100), value DECIMAL(10,2), account_number INT, payment_status payment_status);
ALTER TABLE Invoices ADD COLUMN id SERIAL PRIMARY KEY;
```
- Insert a few rows into this table with different statuses.
```sql
INSERT INTO Invoices (buyer, seller, value, account_number, payment_status)
VALUES
('John Doe', 'ABC Electronics', 1500.50, 123456, 'PAID'),
('Alice Johnson', 'XYZ Supplies', 800.75, 789012, 'WAITING FOR PAYMENT'),
('Bob Smith', 'Tech Solutions Inc.', 2200.00, 345678, 'NEW'),
('Eva Davis', 'Global Innovations', 1250.25, 901234, 'WAITING FOR PAYMENT'),
('Charlie Brown', 'Mega Mart', 550.90, 567890, 'PAID'),
('Olivia White', 'Software Systems Ltd.', 1800.60, 432109, 'WAITING FOR PAYMENT'),
('David Johnson', 'Gadget World', 950.75, 876543, 'NEW'),
('Sophia Miller', 'Fashion Trends', 2000.00, 210987, 'WAITING FOR PAYMENT'),
('Michael Lee', 'Energy Solutions', 1200.30, 654321, 'PAID'),
('Emma Wilson', 'Book Haven', 700.45, 789012, 'NEW');
```
- Select only those invoices with status = "NEW"
```sql
SELECT * FROM Invoices WHERE payment_status = 'NEW';
```

#### 2. UUID data type usage

- Modify table "Invoices" - add column that will keep "internal_id".
```sql
ALTER TABLE Invoices ADD COLUMN internal_id uuid;
```
- Update each row with a value in a column "internal_id". You can use this generator: https://www.uuidgenerator.net/
```sql
UPDATE Invoices SET internal_id = gen_random_uuid ();
```
#### 3. JSON data type usage

- Modify table "Invoices" - add 2 columns that will keep json view of an entity. One column should be with type json and other with type jsonb.
```sql
ALTER TABLE Invoices ADD COLUMN view JSON;
ALTER TABLE Invoices ADD COLUMN view2 JSONB;
```
- Try to fill newly created columns with below json:

'{
  "buyer": "company a",
  "seller": "company b",
  "status": "NEW",
  "account_number": 123321123
  "value": 23.43
}

```sql
UPDATE Invoices SET view2 = '{"buyer": "company a", "seller": "company b", "status": "NEW", "account_number": 123321123, "value": 23.43}'::JSONB;

UPDATE Invoices SET view = '{"buyer": "company a", "seller": "company b", "status": "NEW", "account_number": 123321123, "value": 23.43}';
```

#### 4. Array data type usage

- Modify table "Invoices". Add a column where phone numbers of buyer will be kept. Maximum number of those number is 3.
```sql
ALTER TABLE Invoices ADD COLUMN phone_number VARCHAR(50)[3];
```
- Insert a list of phone numbers for an invoice with id = 3: 123211233, 125433221, 127643454
```sql
UPDATE Invoices SET phone_number = ARRAY['123211233', '125433221', '127643454'] WHERE id = 3;
```
- Insert a list of phone numbers for an invoice with id = 2: 432323112, 123344311
```sql
UPDATE Invoices SET phone_number = ARRAY['432323112', '123344311'] WHERE id = 2;
```
- Select buyer name and his LAST phone number for invoice with id = 3;
```sql
SELECT buyer, phone_number[3] FROM Invoices WHERE id = 3;
```
- Select an invoice which contains phone: 432323112
```sql
SELECT * FROM Invoices WHERE '432323112' = ANY(phone_number);
```

#### 5. Binary data type usage

- Create a file, can be an image or just a text file

- Create a new column in Invoices table that will keep this file
```sql
ALTER TABLE Invoices ADD COLUMN file BYTEA;
```
- Insert this file into database for invoice with id = 1
```sql
UPDATE Invoices SET file = ('/home/dci-student/PostgreSQL/schema.png')::BYTEA WHERE id = 1;
```

#### 6. Date/time data types usage

- Modify table "Invoices", add 4 new columns: payment deadline, transaction time, transaction hour, cyclic payment. Choose the right column types.
```sql
ALTER TABLE Invoices ADD COLUMN payment_deadline DATE, 
ADD COLUMN transaction_time TIMETZ, 
ADD COLUMN transaction_hour TIMETZ, 
ADD COLUMN cyclic_payment INTERVAL;
```
- Update each column with values
```sql
UPDATE Invoices SET payment_deadline = to_date('28/02/2024', 'DD/MM/YYYY') WHERE payment_status = 'WAITING FOR PAYMENT';
UPDATE Invoices SET payment_deadline = to_date('31/12/2023', 'DD/MM/YYYY') WHERE payment_status = 'PAID';
UPDATE Invoices SET payment_deadline = to_date('31/03/2024', 'DD/MM/YYYY') WHERE payment_status = 'NEW';

UPDATE Invoices SET transaction_time = timetz '08:00:00';

UPDATE Invoices SET transaction_hour = '08:00:00'::TIMETZ;

UPDATE Invoices SET cyclic_payment = '1 Month'::INTERVAL;
```
- Update timezone in transaction type, set it to be Melbourne, Australia.
```sql
UPDATE Invoices SET transaction_time = transaction_time AT TIME ZONE 'Australia/Melbourne';
``` 
- Update timezone in transaction time column, set it to be Melbourne, Australia.
```sql
UPDATE Invoices SET transaction_hour = transaction_hour AT TIME ZONE 'Australia/Melbourne';
```
- Select transaction time column, make sure that 10 hours has been added to the time that previously was set.
```sql
SELECT transaction_time FROM Invoices;
```
