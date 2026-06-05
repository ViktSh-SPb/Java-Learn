# SQL
### Задача 1
#### Условие:
Вывести следующие данные:
1. Оценки студентов по каждому предмету.
2. Сколько студентов сдавали 'Программирование'?
3. Средняя оценка по каждому преподавателю.
4. Наивысшая оценка по  предмету 'Базы данных'.
5. Средний балл по каждой группе.
6. Самый молодой студент.
7. Студенты с максимальным баллом.
8. Средний балл по предметам .
9. Студенты, получившие по всем предметам выше 3 баллов.
10. Средний балл по предмету у каждой группы.
11. Преподаватели, чьи студенты получали 5.
12. Процент пятёрок.
13.  Предмет с самой высокой средней оценкой.
14. Студенты, получившие только пятёрки.
15. Найти студентов, у которых хотя бы одна оценка ниже среднего балла по всем экзаменам.
16. Найти студента с наибольшим количеством пятёрок.
17. Найти средний балл студентов по каждому предмету и показать только тех, кто сдавал минимум 2 экзамена по этому предмету.
18. Найти преподавателя, по предметам которого больше всего экзаменов.
19. Найти студента, сдавшего больше всего экзаменов.
20. Найти группу, с самой высокой средней оценкой.
21. Студенты, не получившие ни одной тройки.
22. Самая высокая и самая низкая оценка по каждому предмету (оконная функция).
23. Найти предмет, по которому разница между максимальной и минимальной оценкой наибольшая.
```sql
DROP TABLE IF EXISTS Marks;
DROP TABLE IF EXISTS Students;
DROP TABLE IF EXISTS Groups;
DROP TABLE IF EXISTS Subjects;
DROP TABLE IF EXISTS Teachers;

-- Таблица групп
CREATE TABLE groups (
    group_id INT PRIMARY KEY,
    group_name VARCHAR(50)
);

INSERT INTO groups VALUES
(1, 'ИКБО-01-23'),
(2, 'ИКБО-02-23'),
(3, 'ИКБО-03-23');

-- Таблица студентов
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    birth_date DATE,
    group_id INT,
    FOREIGN KEY (group_id) REFERENCES groups(group_id)
);

INSERT INTO students VALUES
(1, 'Иван', 'Иванов', '2005-06-01', 1),
(2, 'Петр', 'Петров', '2004-08-15', 2),
(3, 'Мария', 'Сидорова', '2005-02-25', 1),
(4, 'Анна', 'Кузнецова', '2003-11-20', 3),
(5, 'Сергей', 'Смирнов', '2004-03-05', 2);

-- Таблица преподавателей
CREATE TABLE teachers (
    teacher_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50)
);

INSERT INTO teachers VALUES
(1, 'Алексей', 'Орлов'),
(2, 'Ольга', 'Васильева'),
(3, 'Дмитрий', 'Ковалев');

-- Таблица предметов
CREATE TABLE subjects (
    subject_id INT PRIMARY KEY,
    subject_name VARCHAR(50),
    teacher_id INT,
    FOREIGN KEY (teacher_id) REFERENCES teachers(teacher_id)
);

INSERT INTO subjects VALUES
(1, 'Математика', 1),
(2, 'Программирование', 2),
(3, 'Базы данных', 3);

-- Таблица оценок
CREATE TABLE marks (
    mark_id INT PRIMARY KEY,
    student_id INT,
    subject_id INT,
    mark INT,
    exam_date DATE,
    FOREIGN KEY (student_id) REFERENCES students(student_id),
    FOREIGN KEY (subject_id) REFERENCES subjects(subject_id)
);

INSERT INTO marks VALUES
(1, 1, 1, 5, '2024-06-01'),
(2, 1, 2, 4, '2024-06-03'),
(3, 2, 1, 3, '2024-06-01'),
(4, 2, 2, 5, '2024-06-03'),
(5, 3, 1, 4, '2024-06-01'),
(6, 3, 3, 5, '2024-06-05'),
(7, 4, 2, 3, '2024-06-03'),
(8, 5, 3, 4, '2024-06-05');
```
#### Решение:
```sql
```
### Задача 2
#### Условие:
Вывести:
1. Количество сотрудников в каждом отделе.
2. Среднюю зарплату по каждому отделу.
3. Найти сотрудников, работающих не в отделах 'HR' и 'Finance'.
4. Сотрудников и их отделы, кроме тех, кто работает в 'HR'.
5. Показать сотрудников, чьи зарплаты ниже средней зарплаты в их отделе.
6. Найти сотрудников, у которых зарплата выше средней по всем сотрудникам.
7. Найти отдел, в котором работает больше всего сотрудников.
8. Для каждого сотрудника вывести его зарплату, разницу между его зарплатой и средней зарплатой по отделу
9. Отделы, в которых количество сотрудников больше или равно 2 и средняя зарплата выше 55000.
10. Сотрудников и их позицию по зарплате внутри отдела (используя оконную функцию).
11. Вывести только второй по зарплате отдел (по средней зарплате).
12. Посчитать разницу между зарплатой сотрудника и максимальной зарплатой в его отделе.
13. Имена сотрудников вместе с названием их отдела.
14. Найти отделы, в которых ещё нет сотрудников.
15. Найти сотрудников, которые зарабатывают больше средней зарплаты по своей группе (отделу).
```sql
CREATE TABLE A (
    Id SERIAL PRIMARY KEY,
    Name VARCHAR(50)
);

-- Вставка данных в таблицу A
INSERT INTO A (Name) VALUES
('Alex'),
('Bob'),
('Alex'),
('Bob'),
('Ola'),
('Vika'),
('Alex'),
('Timur'),
('Mikael'),
('Kate');

-- Таблица B: содержит Id, Name и Phone
CREATE TABLE B (
    Id SERIAL PRIMARY KEY,
    Name VARCHAR(50),
    Phone VARCHAR(20)
);

-- Вставка данных в таблицу B
INSERT INTO B (Name, Phone) VALUES
('Alex', '123'),
('Bob', '234'),
('Anton', '456'),
('Vitya', '789'),
('Max', '1012');


SELECT * FROM A LEFT JOIN B ON A.Name = B.Name;
SELECT * FROM A RIGHT JOIN B ON A.Name = B.Name;

-------------------------------------------------

-- Таблица отделов
CREATE TABLE Departments (
    DepartmentID INT PRIMARY KEY,
    DepartmentName VARCHAR(50)
);

-- Таблица сотрудников
CREATE TABLE Employees (
    EmployeeID INT PRIMARY KEY,
    FirstName VARCHAR(50),
    LastName VARCHAR(50),
    DepartmentID INT,
    Salary INT,
    FOREIGN KEY (DepartmentID) REFERENCES Departments(DepartmentID)
); 

INSERT INTO Departments (DepartmentID, DepartmentName) VALUES
(1, 'Sales'),
(2, 'IT'),
(3, 'HR'),
(4, 'Finance');

-- Заполнение таблицы Employees
INSERT INTO Employees (EmployeeID, FirstName, LastName, DepartmentID, Salary) VALUES
(1, 'Ivan', 'Ivanov', 1, 50000),
(2, 'Anna', 'Petrova', 3, 60000),
(3, 'Petr', 'Sidorov', 2, 70000),
(4, 'Maria', 'Smirnova', 1, 55000),
(5, 'Sergey', 'Kuznetsov', 4, 48000),
(6, 'Andrey', 'Orlov', 2, 72000),
(7, 'Alex', 'Novikov', 2, 45000);
```
#### Решение:
```sql
```
### Задача 3
#### Условие:
```sql

```
#### Решение:
```sql
```