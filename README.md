#### 1 задание
Информация о заказах клиентов с декабря 2013 по январь 2014.
```
SELECT
  [Order].*
FROM
  [Order]
  JOIN Customer ON [Order].CustomerId = Customer.Id
WHERE
  [Order].OrderDate BETWEEN '2013-12-1' AND '2014-2-1';
```
![изображение](https://user-images.githubusercontent.com/125894838/232237602-c07104ad-142a-4eb4-9e1a-9223fc395c8c.png)

#
#### 2 задание
Информация  о заказах, содержащих продукты из Германии и Италии.
```
SELECT
  [Order].*,
  Supplier.Country
FROM
  [Order]
  JOIN OrderItem ON OrderItem.OrderId = [Order].Id
  JOIN Product ON OrderItem.ProductId = Product.Id
  JOIN Supplier ON Product.SupplierId = Supplier.Id
WHERE
  Supplier.Country IN (N'Germany', N'Italy');
```
![изображение](https://user-images.githubusercontent.com/125894838/232237629-dee004ac-1aa9-46bb-aedc-a7854d866f8e.png)

#
#### 3 задание
Информация о продуктах, чьё количество в закзе от 50 до 100.
```
SELECT
  Product.*,
  OrderItem.Quantity
FROM
  Product
  JOIN OrderItem ON OrderItem.ProductId = Product.Id
  JOIN [Order] ON OrderItem.OrderId = [Order].Id
WHERE
  OrderItem.Quantity BETWEEN 50 AND 100;
```
![изображение](https://user-images.githubusercontent.com/125894838/232237676-78c7c986-7ae6-4016-b713-fbd321aeaf7a.png)

#
#### 4 задание
Первые 10 продуктов из Франции с минимальной ценой.
```
SELECT TOP 10
  *
FROM
  Product
  JOIN Supplier ON Product.SupplierId = Supplier.Id
WHERE
  Supplier.Country = N'France'
ORDER BY
  Product.UnitPrice;
```
![изображение](https://user-images.githubusercontent.com/125894838/232237706-49fe4a89-918c-4605-a7fc-02d4ae4658e3.png)

#
#### 5 задание
Количество продуктов по каждому поставщику. Отсортировать по стране и городу в порядке убывания.
```
SELECT
  Supplier.*,
  SupplierIdWithProductsCount.ProductsCount
FROM
  Supplier
  JOIN (
    SELECT
      Product.SupplierId,
      COUNT(*) ProductsCount
    FROM
      Product
    GROUP BY
      Product.SupplierId
  ) AS SupplierIdWithProductsCount
  ON Supplier.Id = SupplierIdWithProductsCount.SupplierId
ORDER BY
  Supplier.Country DESC,
  Supplier.City DESC;
```
![изображение](https://user-images.githubusercontent.com/125894838/233708864-be610f6e-b321-4a56-9f51-8150df7d7bf6.png)

#
#### 6 задание
Первые 3 клиента, у которых заказы с наибольшим (не только максимальным) количеством позиций (вывести фамилию, имя клиента, номера заказов и количество позиций в них).
```
SELECT
  Customer.LastName,
  Customer.FirstName,
  OrderNumber,
  OrderItemsCount
FROM
  [Order]
  JOIN (
    SELECT TOP 3
      OrderItem.OrderId AS OrderId,
      COUNT(*) AS OrderItemsCount
    FROM
      OrderItem
    GROUP BY
      OrderItem.OrderId
    ORDER BY
      OrderItemsCount DESC
  ) AS OrderNumberWithOrderItemsCount
  ON [Order].Id = OrderNumberWithOrderItemsCount.OrderId
  JOIN Customer ON Customer.Id = [Order].CustomerId
ORDER BY
  OrderNumberWithOrderItemsCount.OrderItemsCount DESC
```
![изображение](https://user-images.githubusercontent.com/125894838/233807437-44a2e314-c2cd-4053-882c-b49a0c12f3ed.png)

#
#### 7 задание
Всех клиентов, которые работают с минимальным количеством поставщиков, и их заказы.
```
SELECT
  Customer.*,
  [Order].*,
  CustomerIdWithSupliersCount.SuppliersCount
FROM (
  SELECT
    [Order].CustomerId,
    COUNT(*) AS SuppliersCount
  FROM
    [Order]
    JOIN OrderItem ON [Order].Id = OrderItem.OrderId
  GROUP BY
    [Order].CustomerId
) AS CustomerIdWithSupliersCount
JOIN Customer ON CustomerIdWithSupliersCount.CustomerId = Customer.Id
JOIN [Order] ON [Order].CustomerId = Customer.Id
WHERE
  CustomerIdWithSupliersCount.SuppliersCount = (
    SELECT
      MIN(CustomerSuppliersCount.SuppliersCount) AS MinSupliersCount
    FROM (
      SELECT
        COUNT(*) AS SuppliersCount
      FROM
        [Order]
        JOIN OrderItem ON [Order].Id = OrderItem.OrderId
      GROUP BY
        [Order].CustomerId
    ) AS CustomerSuppliersCount
  )
```
![изображение](https://user-images.githubusercontent.com/125894838/233857853-66de6861-3135-43db-aaa6-01afe37967a4.png)

#
#### 8 задание
Количество различных продуктов по каждому клиенту. Вывести фамилию, имя клиента, количество продуктов.
```
SELECT
  Customer.LastName,
  Customer.FirstName,
  CustomerIdWithUniqueProductsCount.UniqueProductsCount
FROM
  Customer
  JOIN (
    SELECT
      [Order].CustomerId,
      COUNT(DISTINCT OrderItem.ProductId) AS UniqueProductsCount
    FROM
      [Order]
      JOIN OrderItem ON OrderItem.OrderId = [Order].Id
    GROUP BY
      [Order].CustomerId
  ) AS CustomerIdWithUniqueProductsCount
ON CustomerIdWithUniqueProductsCount.CustomerId = Customer.Id
```
![изображение](https://user-images.githubusercontent.com/125894838/233861110-a39da74f-5573-45b3-a7e6-0caa3acb5533.png)
