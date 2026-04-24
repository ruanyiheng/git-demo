UPDATE ddmdba.dbo.DDM_PURCHASE_LIST
SET update_time = GETDATE()  
WHERE create_time = (
    SELECT TOP 1 target_time 
    FROM (
        SELECT MAX(create_time) AS target_time
        FROM ddmdba.dbo.DDM_PURCHASE_LIST
          WHERE  ((YEAR=2025 AND MONTH >=10 ) AND submit_flow = 0 )
        GROUP BY pharm_eyes_code
    ) AS tmp
    ORDER BY target_time DESC
)
AND submit_flow = 0;

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
