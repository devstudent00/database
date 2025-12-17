## 購買明細を「顧客名・購入日時・商品名・数量」で一覧（内部結合、カラム別名、関数）
```
SELECT CONCAT(c.cst_lastname, ' ', c.cst_firstname) AS 顧客名,
       s.shp_date AS 購買日時,
       p.prd_name AS 商品名,
       d.dtl_cnt  AS 数量
FROM details_tbl d
JOIN shopping_tbl s ON d.dtl_shp_id = s.shp_id
JOIN customers_tbl c ON s.shp_cst_id = c.cst_id
JOIN products_tbl p ON d.dtl_prd_id = p.prd_id
```

## 最新購買（副お問い合わせ、ソート、件数制限）
```
SELECT d.*
FROM details_tbl d
WHERE d.dtl_shp_id = (
  SELECT s.shp_id
  FROM shopping_tbl s
  ORDER BY s.shp_date DESC
  LIMIT 1
);
```

## 平均より高い（関数、条件）
```
select * from products_tbl WHERE prd_price > (SELECT AVG(prd_price) FROM products_tbl)
```


## 顧客ごとの購入回数（カラム別名、関数、集約関数）
```
SELECT shp_cst_id, COUNT(*) AS 購入回数
FROM shopping_tbl
GROUP BY shp_cst_id;
```

## 商品ごとの販売数量（カラム別名、集約関数）
```
SELECT dtl_prd_id, SUM(dtl_cnt) AS total_cnt
FROM details_tbl
GROUP BY dtl_prd_id;
```

## 商品ごとの売上金額（カラム別名、関数、内部結合、集約関数）
```
SELECT
  p.prd_name,
  SUM(d.dtl_cnt * p.prd_price) AS total_sales
FROM details_tbl d
INNER JOIN products_tbl p
ON d.dtl_prd_id = p.prd_id
GROUP BY p.prd_name;
```


## 総額5000円以上（カラム別名、内部結合、集約関数）
```
SELECT s.shp_id,
       s.shp_date,
       SUM(p.prd_price * d.dtl_cnt) AS 総額
FROM shopping_tbl s
JOIN details_tbl d ON d.dtl_shp_id = s.shp_id
JOIN products_tbl p ON p.prd_id = d.dtl_prd_id
GROUP BY s.shp_id, s.shp_date
HAVING 総額 >= 5000
ORDER BY 総額 DESC;
```

## 顧客別の購入回数（関数、内部結合、集約関数）
```
SELECT c.cst_id,
       CONCAT(c.cst_lastname, ' ', c.cst_firstname) AS 顧客名,
       COUNT(*) AS 購買回数
FROM shopping_tbl s
JOIN customers_tbl c ON s.shp_cst_id = c.cst_id
GROUP BY c.cst_id, 顧客名
ORDER BY 購買回数 DESC;
```

## 商品別の総販売数（内部結合、集約関数、ソート）
```
SELECT p.prd_name AS 商品名,
       SUM(d.dtl_cnt) AS 総数量
FROM details_tbl d
JOIN products_tbl p ON d.dtl_prd_id = p.prd_id
GROUP BY p.prd_name
ORDER BY 総数量 DESC;
```
