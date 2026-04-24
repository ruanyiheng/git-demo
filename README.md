

UPDATE DDM_PURCHASE_LIST
SET update_time = CURRENT_TIMESTAMP 
WHERE (
    (Year = 2025 AND Month >= 10) 
    OR Year > 2025
  )
  AND submit_flow = 0
  AND (update_time < CURRENT_DATE() OR update_time IS NULL)
ORDER BY create_time DESC 
LIMIT 1;

-- 1. 使用 CTE 圈出那“唯一的一条”符合条件且今天还没被碰过的数据
WITH TargetRow AS (
    SELECT TOP 1 update_time 
    FROM ddmdba.dbo.DDM_PURCHASE_LIST
    WHERE (
        (Year = 2025 AND Month >= 10) 
        OR Year > 2025
    )
    AND submit_flow = 0
    AND (update_time < CAST(GETDATE() AS DATE) OR update_time IS NULL)
    ORDER BY create_time DESC 
)

UPDATE TargetRow
SET update_time = GETDATE()
