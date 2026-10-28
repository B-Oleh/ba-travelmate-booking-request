**Вимоги до API: поле dateOfBirth у POST /auth/register**

## Контекст

Команда TravelMate замінює поле is\_adult у реєстрації на точну дату народження користувача. Endpoint: POST {{baseUrl}}/auth/register (Postman, група Authentication, запит Register User).  
Поточний опис endpoint'а містить поля email, password, firstName, lastName (обов'язкові) і phone (необов'язкове). Поле is\_adult ні в описі, ні в прикладі тіла запиту не знайдено, тому dateOfBirth описано як нове обов'язкове поле.

## Поле dateOfBirth

Параметри нового поля:

| Параметр | Значення |
| :---- | :---- |
| Назва | dateOfBirth |
| Тип даних | string (дата) |
| Формат | YYYY-MM-DD |
| Приклад | 1996-01-01 |
| Обов'язкове | Так |
| Правило віку | Повних 18 років на дату сервера (UTC); якщо 18 років виповнюється сьогодні, реєстрація дозволена |
| is\_adult | Видаляється з тіла запиту, замість нього використовується dateOfBirth |

## Валідації та помилки

Усі помилки повертаються з HTTP 400 Bad Request у форматі { "code": "VALIDATION\_ERROR", "message": "..." }, як і наявна помилка пароля. Перевірки виконуються в такому порядку:

| № | Умова | Приклад значення | message |
| :---- | :---- | :---- | :---- |
| 1 | Поле не передано, null або порожній рядок | відсутнє, null, " " | Date of birth is required |
| 2 | Невірний формат або неіснуюча дата | 23.04.1995, 2001-02-30 | Date of birth must be a valid date in YYYY-MM-DD format |
| 3 | Дата в майбутньому | завтрашня дата | Date of birth cannot be in the future |
| 4 | Користувачу менше 18 років | дата, за якої виповнилося менше 18 повних років | You must be at least 18 years old to register |

## Приклади

Успішний запит:  
{ "email": "newuser@example.com", "password": "SecurePass123\!", "firstName": "New", "lastName": "User", "phone": "+1-555-000-0000", "dateOfBirth": "1995-04-23" }  
Відповідь 400, якщо користувачу менше 18 років:  
{ "code": "VALIDATION\_ERROR", "message": "You must be at least 18 years old to register" }