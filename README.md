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
