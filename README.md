#### 1 задание
Информация о заказах клиентов с декабря 2013 по январь 2014.
```
SELECT
  o.*
FROM
  [Order] AS o
WHERE
  o.OrderDate BETWEEN '2013-12-1' AND '2014-2-1'
```
![изображение](https://user-images.githubusercontent.com/125894838/232237602-c07104ad-142a-4eb4-9e1a-9223fc395c8c.png)

#
#### 2 задание
Информация  о заказах, содержащих продукты из Германии и Италии.
```
SELECT
  o.*,
  s.Country
FROM
  [Order] AS o
  JOIN OrderItem AS oi ON oi.OrderId = o.Id
  JOIN Product AS p ON oi.ProductId = p.Id
  JOIN Supplier AS s ON p.SupplierId = s.Id
WHERE
  s.Country IN (N'Germany', N'Italy')
```
![изображение](https://user-images.githubusercontent.com/125894838/232237629-dee004ac-1aa9-46bb-aedc-a7854d866f8e.png)

#
#### 3 задание
Информация о продуктах, чьё количество в закзе от 50 до 100.
```
SELECT
  p.*,
  oi.Quantity
FROM
  Product AS p
  JOIN OrderItem AS oi ON oi.ProductId = p.Id
  JOIN [Order] AS o ON oi.OrderId = o.Id
WHERE
  oi.Quantity BETWEEN 50 AND 100;
```
![изображение](https://user-images.githubusercontent.com/125894838/232237676-78c7c986-7ae6-4016-b713-fbd321aeaf7a.png)

#
#### 4 задание
Первые 10 продуктов из Франции с минимальной ценой.
```
SELECT TOP 10
  *
FROM
  Product AS p
  JOIN Supplier AS s ON p.SupplierId = s.Id
WHERE
  s.Country = N'France'
ORDER BY
  p.UnitPrice;
```
![изображение](https://user-images.githubusercontent.com/125894838/232237706-49fe4a89-918c-4605-a7fc-02d4ae4658e3.png)

#
#### 5 задание
Количество продуктов по каждому поставщику. Отсортировать по стране и городу в порядке убывания.
```
SELECT
  s.*,
  SupplierProducts.ProductsCount
FROM
  Supplier AS s
  JOIN (
    SELECT
      p.SupplierId,
      COUNT(*) ProductsCount
    FROM
      Product AS p
    GROUP BY
      p.SupplierId
  ) AS SupplierProducts
  ON s.Id = SupplierProducts.SupplierId
ORDER BY
  s.Country DESC,
  s.City DESC
```
![изображение](https://user-images.githubusercontent.com/125894838/233708864-be610f6e-b321-4a56-9f51-8150df7d7bf6.png)

#
#### 6 задание
Первые 3 клиента, у которых заказы с наибольшим (не только максимальным) количеством позиций (вывести фамилию, имя клиента, номера заказов и количество позиций в них).
```
SELECT
  c.LastName,
  c.FirstName,
  OrderNumber,
  OrderItemsCount
FROM
  [Order] AS o
  JOIN (
    SELECT TOP 3
      oi.OrderId AS OrderId,
      COUNT(*) AS OrderItemsCount
    FROM
      OrderItem AS oi
    GROUP BY
      oi.OrderId
    ORDER BY
      OrderItemsCount DESC
  ) AS OrderOrderItems
  ON o.Id = OrderOrderItems.OrderId
  JOIN Customer AS c ON c.Id = o.CustomerId
ORDER BY
  OrderOrderItems.OrderItemsCount DESC
```
![изображение](https://user-images.githubusercontent.com/125894838/233807437-44a2e314-c2cd-4053-882c-b49a0c12f3ed.png)

#
#### 7 задание
Всех клиентов, которые работают с минимальным количеством поставщиков, и их заказы.
```
SELECT
  c.*,
  o.*,
  CustomerSupliers.SuppliersCount
FROM (
  SELECT
    o.CustomerId,
    COUNT(*) AS SuppliersCount
  FROM
    [Order] AS o
    JOIN OrderItem AS oi ON o.Id = oi.OrderId
  GROUP BY
    o.CustomerId
) AS CustomerSupliers
JOIN Customer AS c ON c.Id = CustomerSupliers.CustomerId
JOIN [Order] AS o ON o.CustomerId = c.Id
WHERE
  CustomerSupliers.SuppliersCount = (
    SELECT
      MIN(CustomerSupliers.SuppliersCount) AS MinSupliersCount
    FROM (
      SELECT
        COUNT(*) AS SuppliersCount
      FROM
        [Order] AS o
        JOIN OrderItem AS oi ON o.Id = oi.OrderId
      GROUP BY
        o.CustomerId
    ) AS CustomerSupliers
  )
```
![изображение](https://user-images.githubusercontent.com/125894838/233857853-66de6861-3135-43db-aaa6-01afe37967a4.png)

#
#### 8 задание
Количество различных продуктов по каждому клиенту. Вывести фамилию, имя клиента, количество продуктов.
```
SELECT
  c.LastName,
  c.FirstName,
  CustomerUniqueProducts.UniqueProductsCount
FROM
  Customer AS c
  JOIN (
    SELECT
      o.CustomerId,
      COUNT(DISTINCT oi.ProductId) AS UniqueProductsCount
    FROM
      [Order] AS o
      JOIN OrderItem AS oi ON oi.OrderId = o.Id
    GROUP BY
      o.CustomerId
  ) AS CustomerUniqueProducts
ON CustomerUniqueProducts.CustomerId = c.Id
```
![изображение](https://user-images.githubusercontent.com/125894838/233861110-a39da74f-5573-45b3-a7e6-0caa3acb5533.png)

## Представления
#### 1 задание
Количество поставщиков по каждому клиенту. Вывести сведения о клиенте и количество поставщиков.
```
CREATE VIEW CustomerSuppliers AS
  SELECT
    o.CustomerId,
    COUNT(DISTINCT SupplierId) UniqueSuppliersCount
  FROM
    [Order] AS o
    JOIN OrderItem AS oi ON oi.OrderId = o.Id
    JOIN Product AS p ON p.Id = oi.ProductId
  GROUP BY
    o.CustomerId
```
```
SELECT
  c.*,
  CustomerSuppliers.UniqueSuppliersCount
FROM
  Customer AS c
  JOIN CustomerSuppliers ON CustomerSuppliers.CustomerId = c.Id
```
![изображение](https://user-images.githubusercontent.com/125894838/233862497-e5b18172-a8b3-4afb-a954-1ee0203c1e8c.png)

#### 2 задание
Количество различных продуктов по каждому клиенту. Вывести фамилию, имя клиента, количество продуктов.
```
CREATE VIEW CustomerUniqueProducts AS
  SELECT
    o.CustomerId,
    COUNT(DISTINCT oi.ProductId) AS UniqueProductsCount
  FROM
    [Order] AS o
    JOIN OrderItem AS oi ON oi.OrderId = o.Id
  GROUP BY
    o.CustomerId
```
```
SELECT
  c.LastName,
  c.FirstName,
  CustomerUniqueProducts.UniqueProductsCount
FROM
  Customer AS c
  JOIN CustomerUniqueProducts ON CustomerUniqueProducts.CustomerId = c.Id
```
![изображение](https://user-images.githubusercontent.com/125894838/233863664-0fadf69a-8bdb-4032-a3ac-eb28bc4c3cbf.png)
