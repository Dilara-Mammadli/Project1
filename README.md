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
 (1, 'TELEFON', 2, 1200.00, 0.15),
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



16.07.2026
Tapşırıq 1: HR və Kadrlar Şöbəsi (Smart Leave System)
Ssenari:

Şirkətin daxili "Məzuniyyət İdarəetmə Sistemi" (Smart Leave Manage) üçün işçilərin siyahısını hazırlamalısan. Lakin sistemdə ad, soyad və ata adı fərqli sütunlarda saxlanılır. Sistemdə işçinin tam adı tək bir sətirdə və standart formatda görünməlidir.

Baza Cədvəli: employees

Sütunlar: first_name (Ad), last_name (Soyad), paternal_name (Ata adı)

Biznes Tələbi:

İşçinin tam adını birləşdirərək aralarında boşluq olmaqla "SOYAD AD ATA ADI" formatına sal.

Sistemdə standart görünüş təmin etmək üçün bütün hərflər böyük hərflərlə (UPPER) yazılmalıdır.

Yaranan yeni sütunun adını full_name qoy.

CREATE TABLE employess(first_name VARCHAR(50), 
                       last_name VARCHAR(50),
                       paternal_name VARCHAR(50));
INSERT INTO employess (first_name, last_name, paternal_name)
VALUES
('DILARA','MAMMADLI','RASIM'),
('AYSEL','TAGIYEVA','XEZAIR'),
('LEYLA','MURADOVA','NAMIQ');
SELECT first_name, last_name, paternal_name FROM employess;

SELECT CONCAT (first_name,' ', last_name,' ', paternal_name)AS birlesme FROM employess;

SELECT UPPER(CONCAT (first_name,' ', last_name,' ', paternal_name)) AS boyuk_yazi FROM employess;


Tapşırıq 2: E-Ticarət (Telefon Nömrələrinin Təmizlənməsi)
Ssenari:

Marketinq şöbəsi müştərilərə SMS kampaniyası göndərmək istəyir. SMS provayderi nömrələri yalnız bitişik formatda (məsələn, 0501234567) qəbul edir. Lakin istifadəçilər qeydiyyatdan keçəndə nömrələrini fərqli yazıblar (bəziləri araya tire qoyub: 050-123-4567, bəziləri boşluq qoyub: 050 123 4567).

Baza Cədvəli: customers

Sütunlar: customer_id, phone_raw (Müştərinin yazdığı nömrə)

Biznes Tələbi:

phone_raw sütunundakı bütün tireləri (-) və boşluqları ( ) sil (REPLACE istifadə et).

Yaranan təmiz nömrəni clean_phone sütunu olaraq çıxar.

 TASK2
 -- 1. Cədvəlin düzgün yaradılması
CREATE TABLE customers (
    customer_id INT PRIMARY KEY, 
    phone_raw VARCHAR(20) -- Telefon nömrələri üçün VARCHAR istifadə edirik
);

-- 2. Məlumatların dırnaq daxilində daxil edilməsi (boşluq və tirelərlə)
INSERT INTO customers (customer_id, phone_raw) VALUES 
(1, '070-883-9282'), 
(2, '050 649 7251');

-- 3. Həm boşluqları, həm də tireləri təmizləmək üçün iç-içə REPLACE:
SELECT customer_id, 
       REPLACE(REPLACE(phone_raw, ' ', ''), '-', '') AS clean_phone 
FROM customers;




🛠️ Tapşırıq 3: Maliyyə və Hesabatlıq (Bank Kartı Maskalanması)
Ssenari:

Təhlükəsizlik qaydalarına (PCI-DSS standartlarına) görə, müştəri profilində və ya hesabatlarda bank kartının bütün rəqəmlərini göstərmək qadağandır. Kartın yalnız ilk 4 rəqəmi və son 4 rəqəmi görünməli, ortadakı hissə maskalanmalıdır.

Baza Cədvəli: transactions

Sütunlar: transaction_id, card_number (məsələn, 16 rəqəmli sətir: 4169731234567890)

Biznes Tələbi:

Kart nömrəsinin ilk 4 rəqəmini kəs (SUBSTRING).

Son 4 rəqəmini kəs.

Ortaya ******** (8 ulduz) simvolunu qoyaraq bu hissələri birləşdir (CONCAT).

Nümunə Nəticə: 4169********7890

CREATE TABLE transactions(transaction_id INT PRIMARY KEY, 
                          card_number VARCHAR(50));
INSERT INTO transactions(transaction_id, card_number) VALUES 
(1, 1645789812324556),
(2, 4127545698783696);
SELECT transaction_id, 
       CONCAT(
           SUBSTRING(card_number, 1, 4), 
           '********', 
           SUBSTRING(card_number, 13, 4)
       ) AS yeni_kart 
FROM transactions;





🛠️ Tapşırıq 4: Data Keyfiyyətinə Nəzarət (E-mail Validasiyası)
Ssenari:

İstifadəçi bazasında bəzi e-mail ünvanlarının səhv daxil edildiyindən şübhələnirsən. Şirkət daxili qaydalara əsasən, korporativ e-maillərin sonu mütləq şəkildə @laran.az (9 simvol) ilə bitməlidir və ümumi uzunluğu ən azı 15 simvol olmalıdır.

Baza Cədvəli: users

Sütunlar: user_id, email

Biznes Tələbi:

E-mail ünvanının uzunluğu (LENGTH) 15 simvoldan kiçik olan istifadəçiləri tap.

E-maillərin böyük-kiçik hərflə yazılmasından asılı olmayaraq, hamısını kiçik hərfə çevirib müqayisə et.

CREATE TABLE users(user_id INT PRIMARY KEY, email VARCHAR(50));
INSERT INTO users(user_id, email) VALUES (1, 'dilaredilare@laran.az'),(2, 'vusal@laran.az');

SELECT user_id, LOWER(email) AS email 
FROM users 
WHERE LEN(email) < 20 AND LOWER(email) LIKE '%@laran.az';




🛠️ Tapşırıq 5: Məhsul Bazası (Kateqoriya Kodunun Çıxarılması)
Ssenari:

Anbar idarəetmə sistemində məhsulların daxili kodlaşdırılması belədir: CAT-102-PROD-99. Sətri analiz etdikdə:

İlk 3 simvol (CAT) məhsulun kateqoriyasını bildirir.

Ortadakı 3 rəqəmli hissə (102) alt kateqoriya ID-sidir.

Bizə növbəti hesabat üçün təcili olaraq alt kateqoriya ID-ləri ayrıca sütun kimi lazımdır.

Baza Cədvəli: inventory

Sütunlar: item_id, sku_code (məsələn: CAT-102-PROD-99)

Biznes Tələbi:

sku_code daxilindən 5-ci simvoldan başlayaraq 3 simvolu kəsib götür (SUBSTRING).

Yeni sütunu sub_category_id olaraq adlandır.

CREATE TABLE inventory(item_id INT PRIMARY KEY, sku_code VARCHAR(50));
INSERT INTO inventory(item_id, sku_code) VALUES 
(1, 'CAT-102-PROD-99'),
(2, 'CAT-758-PROD-79');
SELECT item_id, sku_code FROM inventory;

SELECT item_id, SUBSTRING(sku_code,5,3)AS sub_category_id FROM inventory;



