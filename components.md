# Компонентная архитектура
<!-- Состав и взаимосвязи компонентов системы между собой и внешними системами с указанием протоколов, ключевые технологии, используемые для реализации компонентов.
Диаграмма контейнеров C4 и текстовое описание. 
-->
## Компонентная диаграмма

```plantuml
@startuml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

AddElementTag("microService", $shape=EightSidedShape(), $bgColor="CornflowerBlue", $fontColor="white", $legendText="microservice")
AddElementTag("storage", $shape=RoundedBoxShape(), $bgColor="lightSkyBlue", $fontColor="white")

Person(admin, "Администратор")
Person(moderator, "Модератор")
Person(user, "Пользователь")

System_Ext(web_site, "Клиентский веб-сайт", "HTML, CSS, JavaScript, React", "Веб-интерфейс")

System_Boundary(shop_site, "Сайт магазина") {
   Container(client_service, "Сервис авторизации", "C++", "Сервис управления пользователями", $tags = "microService")    
   Container(product_service, "Сервис товаров", "C++", "Сервис управления товарами", $tags = "microService") 
   Container(basket_service, "Сервис корзин", "C++", "Сервис управления корзинами", $tags = "microService")   
   ContainerDb(db, "База данных", "MySQL", "Хранение данных о пользователях, товарах и корзинах пользователей", $tags = "storage")
   
}

Rel(admin, web_site, "Просмотр, добавление и редактирование информации о пользователях, товарах и корзинах")
Rel(moderator, web_site, "Модерация контента и пользователей")
Rel(user, web_site, "Регистрация, просмотр информации о товарах и корзинах")

Rel(web_site, client_service, "Работа с пользователями", "localhost/person")
Rel(client_service, db, "INSERT/SELECT/UPDATE", "SQL")

Rel(web_site, product_service, "Работа с товарами", "localhost/pres")
Rel(product_service, db, "INSERT/SELECT/UPDATE", "SQL")

Rel(web_site, basket_service, "Работа с корзинами", "localhost/conf")
Rel(basket_service, db, "INSERT/SELECT/UPDATE", "SQL")

@enduml
```
## Список компонентов  

### Сервис авторизации
**API**:
-	Создание нового пользователя
      - входные параметры: login, пароль, имя, фамилия, email, обращение (г-н/г-жа)
      - выходные параметры: отсутствуют
-  Авторизация пользователя:
      - входные параметры: login, пароль
      - выходные параметры: токен доступа
-	Поиск пользователя по логину
     - входные параметры:  login
     - выходные параметры: имя, фамилия, email, обращение (г-н/г-жа)
-	Поиск пользователя по маске имени и фамилии
     - входные параметры: маска фамилии, маска имени
     - выходные параметры: login, имя, фамилия, email, обращение (г-н/г-жа)

### Сервис товаров
**API**:
- Создание товара
  - Входные параметры: название товара, описание, фото, цена
  - Выходные параметры: артикул товара(идентификатор)
- Получение списка товаров
  - Входные параметры: отсутствуют
  - Выходные параметры: массив со всеми товарами, где для каждого указаны его идентификатор, название, описание, категория, цена, фото

### Сервис корзин
**API**:
- Создание корзины
  - Входные параметры: идентификатор пользователя, артикул товара, название, описание, цена, фото
  - Выходные параметры: отсутствуют
- Получение корзины для пользователя
  - Входные параметры: идентификатор пользователя
  - Выходные парамтеры: массив артикулов товаров(идентификаторов)
- Добавление, удаление, изменение числа товаров в корзине:
  - Входные параметры: идентификатор пользователя, артикул товара, бренд, число товаров
  - Выходные параметры: массив со всеми добавленными в корзине товарами
- Получение стоимости корзины:
  - Входные параметры: идентификатор пользователя
  - Выходные параметры: суммарная стоимость товаров в корзине
- Удаление корзины:
  - Входные параметры: идентификатор пользователя
  - Выходные параметры: статус операции
