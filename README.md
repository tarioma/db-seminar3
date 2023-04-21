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
ON
  Supplier.Id = SupplierIdWithProductsCount.SupplierId
ORDER BY
  Supplier.Country DESC,
  Supplier.City DESC;
```
![изображение](https://user-images.githubusercontent.com/125894838/233708864-be610f6e-b321-4a56-9f51-8150df7d7bf6.png)


