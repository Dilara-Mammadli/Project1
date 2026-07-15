CREATE TABLE departaments(
  id INT PRIMARY KEY,
  dep_name VARCHAR(100) NOT NULL
);
CREATE TABLE employees(
  id INT PRIMARY KEY, 
  first_name VARCHAR(50) NOT NULL,
  last_name VARCHAR(50) NOT NULL,
  position VARCHAR(100),
  salary DECIMAL (10,2) CHECK(salary>=500),
  hire_date DATE,
  dep_id INT,
  FOREIGN KEY (dep_id)REFERENCES departaments(id)
);

ALTER TABLE employees ADD status VARCHAR(10) DEFAULT 'Aktiv';

INSERT INTO departaments (id, dep_name) VALUES
(1, 'Istehsal'),
(2, 'Ofis'),
(3, 'Anbar');

INSERT INTO employees (id, first_name, last_name, position, salary, hire_date, dep_id)VALUES
(1, 'DILARE', 'MEMMEDLI','HR', 700, '01.11.2022', 2),
(2, 'LEYLA', 'AGAZADE','MUHASIB', 1000, '01.08.2023', 2),
(3, 'NAMIQ', 'SALAYEV','OPERATOR', 1200, '01.01.2024', 1),
(4, 'GULAY', 'TAGIYEVA','QEYDIYYATCI', 700, '01.11.2022', 3),
(5, 'ANAR', 'REHIMOV','REIS', 1400, '01.09.2025', 1)
SELECT * FROM employees; 

SELECT first_name, last_name, salary FROM employees WHERE salary>1000 AND (position ='Operator'OR position = 'HR');

SELECT first_name, last_name, position FROM employees ORDER BY first_name ASC;

SELECT COUNT (*) AS umumi_iscisayi FROM employees;

SELECT dep_id, COUNT(*) AS isci_sayi, AVG(salary) AS orta_maas FROM employees GROUP BY dep_id;

SELECT dep_id, COUNT(*) AS isci_sayi, AVG(salary) AS orta_maas FROM employees GROUP BY dep_id HAVING AVG(salary)>1000;

SELECT 
e.first_name,
e.last_name,
e.position,
d.dep_name
FROM employees e INNER JOIN departaments d ON e.dep_id=d.id;

UPDATE employees SET position='NEZARETCI', salary=1500 WHERE id=4;
UPDATE employees SET status='Passiv' WHERE id=2;
SELECT * FROM employees;







15.07.2026
-- 1. Addım: Cədvəli yaradırıq
CREATE TABLE Satislar (
    satis_id INT PRIMARY KEY, 
    mehsul_adi VARCHAR(50), 
    miqdar INT, 
    vahid_qiymeti DECIMAL(10,2), 
    endirim_faizi DECIMAL(10,2)
);

-- 2. Addım: Məlumatları daxil edirik (Endirimləri 0.15 və 0.12 olaraq yazırıq)
INSERT INTO Satislar (
    satis_id, 
    mehsul_adi, 
    miqdar, 
    vahid_qiymeti, 
    endirim_faizi
) VALUES
 (1, 'TELEFON', 2, 1500.00, 0.15),
 (2, 'LAPTOP',  5, 2000.00, 0.12);

-- 3. Addım: Həm ana məlumatları, həm də ROUND ilə net məbləği hesablayıb gətiririk
SELECT 
    satis_id, 
    mehsul_adi, 
    miqdar, 
    vahid_qiymeti, 
    endirim_faizi,
    ROUND((miqdar * vahid_qiymeti) * (1 - endirim_faizi), 2) AS net_mebleg
FROM Satislar;



CREATE TABLE kargo_gonderisleri (
gonderis_id INT PRIMARY KEY , 
musteri_adi VARCHAR(50), 
paket_cekisi DECIMAL(10,2)
);

INSERT INTO kargo_gonderisleri(
gonderis_id, 
musteri_adi, 
paket_cekisi )
VALUES 
(1, 'DILARA', 2.589),
(2, 'LEYLA', 5.1),
(3, 'NAMIQ', 26.14);

SELECT paket_cekisi 
CEILING (paket_cekisi) 
FROM kargo_gonderisleri;
