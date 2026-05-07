

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
    AND (update_time < CAST(GETDATE() AS DATE) OR 
    
    
    
    
    update_time IS NULL)
    ORDER BY create_time DESC 
)

UPDATE TargetRow
SET update_time = GETDATE()




咱们根本不需要写 Converter！更绝不向字符串妥协！
​在 EasyExcel 3.3.2 中，我们刚刚讨论的 WriteCellData 机制是完美支持的，这是官方专门为动态导出设计的终极大招。
​为了绝对防止 EasyExcel 偷偷摸摸把你的 Date 转换成 String，我们用**“双重保险（底层纯数字 + 动态单元格格式）”**，彻底绕过所有的 Converter！
​请把你处理日期字段的代码，直接一字不差地替换成下面这段，不需要新建任何拦截器类，直接在你的 try-catch 里改：import com.alibaba.excel.metadata.data.DataFormatData;
import com.alibaba.excel.metadata.data.WriteCellData;
import com.alibaba.excel.write.metadata.style.WriteCellStyle;
import org.apache.poi.ss.usermodel.DateUtil;
import java.math.BigDecimal;
import java.time.LocalDate;
import java.time.ZoneId;
import java.util.Date;

// ... 你原来的代码，找到下面这个 try 块 ...

try {
    LocalDate localDate = (LocalDate) field.get(obj);
    Date utilDate = Date.from(localDate.atStartOfDay(ZoneId.systemDefault()).toInstant());

    // 第一重保险：直接转成 Excel 底层的数字（比如 46011.0），彻底切断 EasyExcel 把它当 Date/String 处理的念想！
    double excelDouble = DateUtil.getExcelDate(utilDate);
    
    // 把数字包装进 WriteCellData (3.3.2 绝对支持塞 BigDecimal)
    WriteCellData<BigDecimal> cellData = new WriteCellData<>(new BigDecimal(String.valueOf(excelDouble)));

    // 第二重保险：强制挂上系统短日期格式（index 14）
    WriteCellStyle style = new WriteCellStyle();
    DataFormatData format = new DataFormatData();
    format.setIndex((short) 14); // 14 = 原生短日期 (yyyy/MM/dd)
    style.setDataFormatData(format);
    
    // 把衣服穿在数据上
    cellData.setWriteCellStyle(style);

    // 把这个无敌的实体塞进列里
    row.add(cellData);

} catch (Exception e) {
    row.add(""); 
}
