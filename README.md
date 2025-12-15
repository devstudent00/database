
結合
①購買明細を「顧客名・購入日時・商品名・数量」で一覧
SELECT CONCAT(c.cst_lastname, ' ', c.cst_firstname) AS 顧客名,
       s.shp_date AS 購買日時,
       p.prd_name AS 商品名,
       d.dtl_cnt  AS 数量
FROM details_tbl d
JOIN shopping_tbl s ON d.dtl_shp_id = s.shp_id
JOIN customers_tbl c ON s.shp_cst_id = c.cst_id
JOIN products_tbl p ON d.dtl_prd_id = p.prd_id


副お問い合わせ
①最新購買
SELECT d.*
FROM details_tbl d
WHERE d.dtl_shp_id = (
  SELECT s.shp_id
  FROM shopping_tbl s
  ORDER BY s.shp_date DESC
  LIMIT 1
);



集約関数
①総額5000円以上
SELECT s.shp_id,
       s.shp_date,
       SUM(p.prd_price * d.dtl_cnt) AS 総額
FROM shopping_tbl s
JOIN details_tbl d ON d.dtl_shp_id = s.shp_id
JOIN products_tbl p ON p.prd_id = d.dtl_prd_id
GROUP BY s.shp_id, s.shp_date
HAVING 総額 >= 5000
ORDER BY 総額 DESC;

②顧客別の購入回数
SELECT c.cst_id,
       CONCAT(c.cst_lastname, ' ', c.cst_firstname) AS 顧客名,
       COUNT(*) AS 購買回数
FROM shopping_tbl s
JOIN customers_tbl c ON s.shp_cst_id = c.cst_id
GROUP BY c.cst_id, 顧客名
ORDER BY 購買回数 DESC;

③商品別の総販売数
SELECT p.prd_name AS 商品名,
       SUM(d.dtl_cnt) AS 総数量
FROM details_tbl d
JOIN products_tbl p ON d.dtl_prd_id = p.prd_id
GROUP BY p.prd_name
ORDER BY 総数量 DESC;
