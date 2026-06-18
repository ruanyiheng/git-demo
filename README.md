”import com.alibaba.excel.metadata.data.DataFormatData;
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



C:\Users\2471197\IdeaProjects\DDMnew\cts-app\stc-stock我现在
  要修改这个文件中的DetailChain.java和DetailChainServiceImpl.ja
  va的相关代码，主要是在DetailChainServiceImpl.java这个文件的15
  26到1540行之间detailChain.setIsKeyProduct(retailStruct.getIsT
  argetData());这个方法内部的retailStruct.get
    IsTargetData()不对我要改成从DDM_MDM_STC_ESTIMATE_PRODUCT这
  个表中查询得到    productgroupcode这个字段然后再和下面的detai
  lChain.setProductCode(detailChain.getProductCode());这里面的
  得到的ProductCode比较，如果相同，setIsKeyProduct返回true，你
  先分析一下怎么改然后出个方案给我


