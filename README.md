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


17.07.2026

Trading platforması üçün istifadəçilərin etdiyi investisiyaların gəlirlilik faizini (Return on Investment - ROI) hesablayan hesabat hazırlamalısan. Düstur belədir: (qazanc / investisiya_meblegi) * 100. Lakin bəzi istifadəçilərin balansında investisiya məbləği 0 daxil edilib və bu, sistemdə hesablama apararkən sıfıra bölünmə xətası yaradır.

Baza Cədvəli: trading_accounts

Sütunlar: account_id, invested_amount (investisiya məbləği), profit (qazanc)

Biznes Tələbi:

NULLIF funksiyasından istifadə edərək investisiya məbləği 0 olan sətirlərdə sıfıra bölünmə xətasının qarşısını al.

Yaranan gəlirlilik faizini roi_percentage sütunu olaraq çıxar.

CREATE TABLE trading_accounts(account_id INT PRIMARY KEY, invested_amount DECIMAL(18, 2), -- 18 rəqəm ümumi uzunluq, 2 rəqəm vergüldən sonra
    profit DECIMAL(18, 2));
INSERT INTO trading_accounts(account_id, invested_amount, profit)  VALUES 
(1, 2000, 150),
(2, 15096, 200),
(3, 0, 150);
SELECT account_id, invested_amount, profit FROM trading_accounts;

SELECT account_id, invested_amount, profit, ((profit / NULLIF (invested_amount,0)) * 100) 
AS roi_percentage FROM trading_accounts;





Tapşırıq 2: Müştəri Xidmətləri (Əlaqə Vasitələrinin Təmizlənməsi)
Ssenari:

Müştərilərə mühüm bildiriş göndərmək lazımdır. Bizim əlaqə prioritetimiz belədir:

Əgər müştərinin şəxsi e-maili (personal_email) varsa, ora göndər.

Şəxsi e-maili yoxdursa (NULL-dursa), iş e-mailinə (work_email) göndər.

Heç biri yoxdursa, default olaraq 'no-reply@company.com' ünvanına göndər.

Baza Cədvəli: contacts

Sütunlar: contact_id, personal_email, work_email

Biznes Tələbi:

COALESCE funksiyasını istifadə edərək yuxarıdakı prioritet sırasına uyğun olaraq tək bir e-mail sütunu çıxar.

Yeni sütunun adını notification_email qoy.

CREATE TABLE contacts(contact_id INT PRIMARY KEY, personal_email VARCHAR(50), work_email VARCHAR(50));
INSERT INTO contacts(contact_id, personal_email, work_email) VALUES 
(1, 'dilare@mail.ru', 'dilare@laran.az'),
(2, NULL, 'leyla@laran.az'),
(3, 'vusal@mail.ru', 'vusal@laran.az'),
(4, NULL, NULL);
SELECT contact_id, 
       COALESCE(personal_email, work_email, 'no-reply@company.com') AS notification_email
FROM contacts;

SELECT contact_id,
       CASE 
           WHEN personal_email IS NOT NULL THEN personal_email
           WHEN work_email IS NOT NULL THEN work_email
           ELSE 'no-reply@company.com'
       END AS notification_email
FROM contacts;






🛠️ Tapşırıq 3: HR / Mükafatlandırma (Staja görə Kateqoriya və Bonus təyini)
Ssenari:

Şirkət rəhbərliyi işçilərin işə başlama tarixinə (stajına) görə kateqoriyalar müəyyən etmək və onlara bonus vermək istəyir.

Baza Cədvəli: employees

Sütunlar: employee_id, first_name, salary (əməkhaqqı), experience_years (iş stajı ili)

Biznes Tələbi:

CASE operatorundan istifadə edərək yeni employee_tier sütunu yarat:

Stajı 5 ildən çox olanlar üçün -> 'Senior Azerbaijani Expert'

Stajı 2 ilə 5 il arasında olanlar üçün -> 'Mid Professional'

Stajı 2 ildən az olanlar üçün -> 'Junior Specialist'

İşçilərə veriləcək bonusu hesabla: Əgər kateqoriyası 'Senior Azerbaijani Expert'-dirsə, maaşının 20%-i, digər hallarda isə maaşının 5%-i qədər bonus hesablanmalıdır. (Bu bonusu da bonus_amount olaraq göstər).

CREATE TABLE employees (employee_id INT PRIMARY KEY, 
                        first_name VARCHAR(50), 
                        salary DECIMAL(10,2), 
                        experience_years INT);
INSERT INTO employees (employee_id, first_name, salary, experience_years) VALUES
(1, 'DILARA', 500, 5),
(2, 'LEYLA', 400, 3);
SELECT employee_id, first_name, salary, experience_years FROM employees;

SELECT employee_id, 
       first_name, 
       salary, 
       experience_years,

       CASE 
           WHEN experience_years >= 5 THEN 'Senior Azerbaijani Expert'
           WHEN experience_years BETWEEN 2 AND 4 THEN 'Mid Professional'
           ELSE 'Junior Specialist'
       END AS employee_tier,
       
       CASE 
           WHEN experience_years >= 5 THEN salary * 0.20
           ELSE salary * 0.05
       END AS bonus_amount

FROM employees;










🛠️ Tapşırıq 4: Fintech / Abunəlik Sistemi (Növbəti Ödəniş Tarixinin Hesablanması)
Ssenari:

İstifadəçilərin aylıq və illik abunəlik tarixlərini idarə edən sistem qurmalısan. Müştərinin abunə olduğu tarix və abunəlik tipi (Aylıq / İllik) bizə məlumdur.

Baza Cədvəli: subscriptions

Sütunlar: subscription_id, start_date (abunəlik başlama tarixi - DATE tipində), plan_type (abunəlik növü: 'Monthly' və ya 'Yearly')

Biznes Tələbi (SQL Server - DATEADD və CASE birlikdə):

Əgər plan_type dəyəri 'Monthly'-dirsə, start_date üzərinə 1 ay əlavə et.

Əgər plan_type dəyəri 'Yearly'-dirsə, start_date üzərinə 1 il əlavə et.

Nəticə sütununu next_payment_date olaraq adlandır.

🛠️ Tapşırıq 5: Satış Analitikası (Həftəsonu və Həftəiçi Satışlarının Analizi)
Ssenari:

Mağazanın satışlarının hansı hissəsinin həftəiçi, hansı hissəsinin isə həftəsonu reallaşdığını analiz etmək lazımdır. SQL Server-də DATEPART(weekday, tarix) bizə həftənin gününü rəqəm olaraq qaytarır (Məsələn: 1 = Bazar, 7 = Şənbə, digər rəqəmlər isə həftəiçi günlərdir).

Baza Cədvəli: sales

Sütunlar: sale_id, sale_amount, sale_date (DATETIME tipində)

Biznes Tələbi (SQL Server - DATEPART və CASE birlikdə):

DATEPART vasitəsilə satış tarixinin həftənin hansı günü olduğunu tap.

CASE vasitəsilə: Əgər gün 1 (Bazar) və ya 7 (Şənbə)-dirsə, 'Weekend' (Həftəsonu), digər günlərdirsə 'Weekday' (Həftəiçi) yazdır.

Yeni sütunun adını day_type qoy.
