---
title:  U8数据库同步至WMS数据库语句
date: 2022-05-06 22:39:05
updated: 2022-05-06 22:40:05
description:  U8数据库同步至WMS数据库语句
keywords: U8,WMS
tags:
  - U8,WMS
categories:
  - 教程
---



# U8数据库同步至WMS数据库语句



## 1. 静态数据

### 1.1. 部门档案(Department)

```sql
WITH DepartmentSync AS(
  SELECT
    cDepCode DEPARTMENT_CODE,
    cDepName DEPARTMENT_NAME,
    cDepFullName DEPARTMENT_DESC,
    'Y' DATA_VALID,
    CONVERT(nvarchar, dp.dModifyDate, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    Department dp
  WHERE
    CONVERT(nvarchar, isnull(dp.dModifyDate, getdate()), 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  DepartmentSync
ORDER BY
  dModifyTime desc
```



### 1.2. 用户资料(UserAccount)

```sql
WITH UserAccountSync AS(
  SELECT
    u.cUser_Id USER_ACCOUNT,
    u.cUser_Name USER_NAME,
    u.cPassword USER_PASSWORD,
    u.cUserEmail E_MAIL,
    d.cDepCode DEPARTMENTID,
    h.cPsnMobilePhone MOBILE,
    'Y' DATA_VALID,
    CONVERT(
      nvarchar,
      CASE
        WHEN u.dPasswordDate IS NULL THEN u.dCrDate
        ELSE u.dPasswordDate
      END,
      121
    ) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    UA_User_Ex u
    LEFT JOIN hr_hi_person h ON h.cPsn_Num = u.cUser_Id
    LEFT JOIN Department d ON d.cDepCode = h.cDept_num
  WHERE
    CONVERT(
      nvarchar,
      isnull(
        CASE
          WHEN u.dPasswordDate IS NULL THEN u.dCrDate
          ELSE u.dPasswordDate
        END,
        getdate()
      ),
      121
    ) > CONVERT(nvarchar, '', 121)
)
SELECT
  *
FROM
  UserAccountSync
ORDER BY
  dModifyTime desc
```



### 1.3. 客户档案(Customer)

```sql
WITH CustomerSync AS(
  SELECT
    cCuscode CUSTOMER_CODE,
    cCusname CUSTOMER_NAME,
    cCusabbname SHORT_NAME,
    cc.cCCCode CCUSTOMER_CODE,
    cc.cCCName CCUSTOMER_NAME,
    cu.cCusAddress CUSTOMER_ADDRESS,
    cu.cCusPhone CUSTOMER_TEL,
    CONVERT(nvarchar, cu.dCusDevDate, 121) CUSTOMER_DEVDATE,
    'N/A' SALES_CODE,
    0 RMA_CENTERID,
    'Y' DATA_VALID,
    cu.cModifyPerson MODIFY_USERID,
    CONVERT(
      nvarchar,
      (
        CASE
          WHEN cu.dModifyDate IS NOT NULL THEN cu.dModifyDate
          ELSE cu.dCusCreateDatetime
        END
      ),
      121
    ) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    customer cu
    INNER JOIN customerclass cc ON cu.cCCCode = cc.cCCCode
  WHERE
    CONVERT(
      nvarchar,
      isnull(
        CASE
          WHEN cu.dModifyDate IS NULL THEN cu.dCusCreateDatetime
          ELSE cu.dModifyDate
        END,
        getdate()
      ),
      121
    ) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  CustomerSync
ORDER BY
  dModifyTime desc
```



### 1.4. 供应商档案(Vendor)

```sql
WITH VendorSync AS(
  SELECT
    vc.cVCCode VENDOR_CCODE,
    vc.cVCName VENDOR_CNAME,
    v.cVenCode VENDOR_CODE,
    v.cVenName VENDOR_NAME,
    v.cVenAbbname SHORT_NAME,
    v.cVenAddress VENDOR_ADDRESS,
    v.cCreatePerson CREATE_USERID,
    'Y' DATA_VALID,
    CONVERT(
      nvarchar,
      (
        CASE
          WHEN v.dModifyDate IS NULL THEN v.dVenCreateDatetime
          ELSE v.dModifyDate
        END
      ),
      121
    ) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    Vendor v
    INNER JOIN VendorClass vc ON v.cVCCode = vc.cVCCode
  WHERE
    CONVERT(
      nvarchar,
      isnull(
        CASE
          WHEN v.dModifyDate IS NULL THEN v.dVenCreateDatetime
          ELSE v.dModifyDate
        END,
        getdate()
      ),
      121
    ) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  VendorSync
ORDER BY
  dModifyTime desc
```



### 1.5. 仓库信息(Warehouse)

```sql
WITH WarehouseSync AS(
  SELECT
    w.cWhCode WAREHOUSE_NO,
    w.cWhName WAREHOUSE_NAME,
    w.cDepCode DEPARTMENT_NO,
    w.cWhAddress WAREHOUSE_ADDRESS,
    (
      CASE
        WHEN w.bWhPos = 1 THEN 'Y'
        ELSE 'N'
      END
    ) POSITION_FLAG,
    w.cWhPerson WAREHOUSE_PERSON,
    w.cWhPhone WAREHOUSE_TEL,
    'Y' DATA_VALID,
    CONVERT(nvarchar, w.dModifyDate, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    Warehouse w
  WHERE
    CONVERT(nvarchar, isnull(w.dModifyDate, getdate()), 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  WarehouseSync
ORDER BY
  dModifyTime desc
```



### 1.6. 货位信息(Location)

```sql
WITH LocationSync AS(
  SELECT
    p.cPosCode LOCATION_NO,
    p.cPosName LOCATION_DESC,
    p.cWhCode WAREHOUSE_CODE,
    p.cMemo LOCATION_REMARK,
    'Y' DATA_VALID,
    CONVERT(bit, 0) IsSync
  FROM
    POSITION p
  WHERE
    bPosEnd = 1
    AND CONVERT(nvarchar, p.cPosCode, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  LocationSync
ORDER BY
  LOCATION_NO desc
```



### 1.7. 物料档案(Partnumber)

```sql
WITH PartnumberSync AS(
  SELECT
    DISTINCT i.cInvCode PART_NUMBER,
    i.cInvName PART_DESC1,
    i.cInvStd PART_DESC2,
    com.cComUnitName PART_UNIT,
    i.cInvCCode PART_TYPECODE,
    ic.cInvCName PART_TYPE,
    cVenCode VENDOR_CODE,
    cPosition LOCATION_NO,
    (
      CASE
        WHEN i.bPropertyCheck = 1 THEN 'Y'
        ELSE 'N'
      END
    ) QC_FLAG,
    --是否质检
    (
      CASE
        WHEN i.bInvQuality = 1 THEN 'Y'
        ELSE 'N'
      END
    ) QUALITY_FLAG,
    --是否保质期管理 
    (
      CASE
        WHEN i.bInvBatch = 1 THEN 'Y'
        ELSE 'N'
      END
    ) BATCH_FLAG,
    --是否批次管理 
    'Y' DATA_VALID,
    CONVERT(nvarchar, i.dModifyDate, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    inventory i
    INNER JOIN inventoryclass ic ON i.cInvCCode = ic.cInvCCode
    INNER JOIN ComputationUnit com ON com.cComunitCode = i.cComUnitCode
  WHERE
    CONVERT(nvarchar, isnull(i.dModifyDate, getdate()), 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  PartnumberSync
ORDER BY
  dModifyTime desc
```



## 2. 动态数据

### 2.1. 入库作业

#### 2.1.1 收料

采购（委外）到货单（ArrivalVouch）

```sql
WITH ArrivalVouchSync AS(
  SELECT
    CAST(ph.cCode AS nvarchar) + '|' + RIGHT(
      '000' + CAST(
        (
          CASE
            WHEN len(phs.ivouchrowno) > 0 THEN phs.ivouchrowno
            ELSE 0
          END
        ) AS nvarchar
      ),
      3
    ) RTID,
    ph.cCode RTNO,
    RIGHT(
      '000' + CAST(
        (
          CASE
            WHEN len(phs.ivouchrowno) > 0 THEN phs.ivouchrowno
            ELSE 0
          END
        ) AS VARCHAR
      ),
      3
    ) ITEMNO,
    v.cVenCode VENDORID,
    'N/A' VENDOR_SITE,
    'N/A' VENDOR_DELIVERYNO,
    phs.cInvCode PARTID,
    'N/A' VERSION,
    'Y' PROPERTYCHECK,
    CAST(
      isnull(phs.fValidQuantity, 0) + isnull(phs.fDegradeQuantity, 0) AS DECIMAL(10, 4)
    ) RT_QTY,
    ph.cBusType RT_TYPE,
    'N/A' BRAND,
    'N/A' ORIGIN,
    'Initial' RT_STATUS,
    ph.cMaker RT_USERID,
    ph.cpocode PO_NO,
    CONVERT(nvarchar, ph.dDate, 121) PLAN_RTDATE,
    getdate() ACTURAL_RTDATE,
    (
      CASE
        WHEN phs.bexigency = 1 THEN 'Y'
        ELSE 'N'
      END
    ) URGENT_STATUS,
    (
      CASE
        WHEN phs.bGsp = 1 THEN 'Y'
        ELSE 'N'
      END
    ) QC_FLAG,
    ctransordercode ASN_NO,
    cgeneralordercode ASN_ITEMNO,
    'N/A' WORK_ORDER,
    phs.cBatch ERPLOT_NO,
    'N/A' IQC_LOTNO,
    0 MINPACK_LOTSIZE,
    phs.cGspState QC_RESULT,
    phs.cWhCode WAREHOUSEID,
    'N/A' WAIVE_NO,
    CONVERT(nvarchar, q.DVERIFYTIME, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    PU_ArrivalVouchs phs
    INNER JOIN PU_ArrivalVouch ph ON ph.id = phs.id
    AND phs.cCloser IS NULL
    INNER JOIN QMCHECKVOUCHER q ON q.SOURCEID = ph.id
    AND q.SOURCEAUTOID = phs.Autoid
    INNER JOIN vendor v ON v.cVenCode = ph.cVenCode
  WHERE
    ph.cverifier IS NOT NULL
    AND q.DVERIFYTIME IS NOT NULL
    AND ph.cCloser IS NULL
    AND (
      ph.cBusType = '普通采购'
      OR ph.cBusType = '委外加工'
    )
    AND ph.bNegative = 0
    AND phs.bGsp = 1
    AND CONVERT(nvarchar, q.DVERIFYTIME, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  ArrivalVouchSync
ORDER BY
  dModifyTime desc
```

采购（委外）到货单（免检）（ArrivalvouchInspection）

```sql
WITH ArrivalvouchInspectionSync AS(
  SELECT
    CAST(ph.cCode AS nvarchar) + '|' + RIGHT(
      '000' + CAST(
        (
          CASE
            WHEN len(phs.ivouchrowno) > 0 THEN phs.ivouchrowno
            ELSE 0
          END
        ) AS nvarchar
      ),
      3
    ) RTID,
    ph.cCode RTNO,
    RIGHT(
      '000' + CAST(
        (
          CASE
            WHEN len(phs.ivouchrowno) > 0 THEN phs.ivouchrowno
            ELSE 0
          END
        ) AS VARCHAR
      ),
      3
    ) ITEMNO,
    v.cVenCode VENDORID,
    'N/A' VENDOR_SITE,
    'N/A' VENDOR_DELIVERYNO,
    phs.cInvCode PARTID,
    'N/A' VERSION,
    'N' PROPERTYCHECK,
    CAST(phs.iQuantity AS DECIMAL(10, 4)) RT_QTY,
    ph.cBusType RT_TYPE,
    'N/A' BRAND,
    'N/A' ORIGIN,
    'Initial' RT_STATUS,
    ph.cMaker RT_USERID,
    ph.cpocode PO_NO,
    CONVERT(nvarchar, ph.dDate, 121) PLAN_RTDATE,
    getdate() ACTURAL_RTDATE,
    (
      CASE
        WHEN phs.bexigency = 1 THEN 'Y'
        ELSE 'N'
      END
    ) URGENT_STATUS,
    (
      CASE
        WHEN phs.bGsp = 1 THEN 'Y'
        ELSE 'N'
      END
    ) QC_FLAG,
    ctransordercode ASN_NO,
    cgeneralordercode ASN_ITEMNO,
    'N/A' WORK_ORDER,
    phs.cBatch ERPLOT_NO,
    'N/A' IQC_LOTNO,
    0 MINPACK_LOTSIZE,
    phs.cGspState QC_RESULT,
    phs.cWhCode WAREHOUSEID,
    'N/A' WAIVE_NO,
    CONVERT(nvarchar, ph.caudittime, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    PU_ArrivalVouchs phs
    INNER JOIN PU_ArrivalVouch ph ON ph.id = phs.id
    AND phs.cCloser IS NULL
    INNER JOIN vendor v ON v.cVenCode = ph.cVenCode
  WHERE
    ph.cverifier IS NOT NULL
    AND ph.caudittime IS NOT NULL
    AND ph.cCloser IS NULL
    AND (
      ph.cBusType = '普通采购'
      OR ph.cBusType = '委外加工'
    )
    AND ph.bNegative = 0
    AND phs.bGsp = 0
    AND CONVERT(nvarchar, ph.caudittime, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  ArrivalvouchInspectionSync
ORDER BY
  dModifyTime desc
```

#### 2.1.2 产成品入库

生产订单(FgstockinManufacture)

```sql
WITH FgstockinManufactureSync AS(
  SELECT
    o.MoCode FORM_NO,
    'Initial' FORM_STATUS,
    '' REMARK,
    '生产订单' FORM_TYPE,
    '' FORM_ATTR,
    NULL MOVE_NO,
    NULL PROCESS_USERID,
    NULL PROCESS_STARTTIME,
    NULL PROCESS_ENDTIME,
    'I04' WRH_FORMCODE,
    NULL CREATE_TYPE,
    NULL SOURCE_NO,
    NULL SITEID,
    o.CreateUser REQUEST_USERID,
    o.CreateDate CREATE_DATE,
    mot.RelsUser MODIFY_USERID,
    CONVERT(nvarchar, mot.RelsTime, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    mom_order o
    INNER JOIN mom_orderdetail mot ON mot.moid = o.moid
  WHERE
    mot.Status = 3
    AND mot.RelsTime IS NOT NULL
    AND mot.RelsUser IS NOT NULL
    AND CONVERT(nvarchar, mot.RelsTime, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  FgstockinManufactureSync
ORDER BY
  dModifyTime DESC
```



生产订单明细(FgstockinManufactureDetail)

```sql
WITH FgstockinManufactureDetailSync AS(
  SELECT
    o.MoCode + '|' + RIGHT('000' + CAST(od.SortSeq AS nvarchar), 3) FORM_RECID,
    o.MoCode FORM_NO,
    RIGHT('000' + CAST(od.SortSeq AS nvarchar), 3) ITEMNO,
    '生产订单' FORM_TYPE,
    '' WORK_ORDER,
    od.InvCode PARTID,
    'N/A' VERSION,
    od.WhCode WAREHOUSEID,
    od.MoLotCode ERP_LOTNO,
    NULL ERP_PROCESS,
    CAST(od.Qty AS DECIMAL(10, 4)) RETURN_QTY,
    0 ACTUAL_QTY,
    od.remark REMARK,
    '' ITEMNO_FLAG,
    0 STOCKIN_QTY,
    'N/A' RTNPACKAGE_ID,
    od.QcFlag QC_FLAG,
    NULL QC_RESULT,
    NULL IQC_LOTNO,
    NULL WAIVE_NO,
    NULL WRH_ORG,
    NULL SITEID,
    NULL DID,
    od.RelsUser MODIFY_USERID,
    CONVERT(nvarchar, od.RelsTime, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    mom_orderdetail od
    INNER JOIN mom_order o ON o.MOID = od.MOID
  WHERE
    od.RelsTime IS NOT NULL
    AND od.RelsUser IS NOT NULL
    AND CONVERT(nvarchar, od.RelsTime, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  FgstockinManufactureDetailSync
ORDER BY
  dModifyTime DESC
```



委外订单(FgstockinOutsourcing)

```sql
WITH FgstockinOutsourcingSync AS(
  SELECT
    mo.cCode FORM_NO,
    'Initial' FORM_STATUS,
    mo.cMemo REMARK,
    '委外订单' FORM_TYPE,
    '' FORM_ATTR,
    NULL MOVE_NO,
    NULL PROCESS_USERID,
    NULL PROCESS_STARTTIME,
    NULL PROCESS_ENDTIME,
    'I01' WRH_FORMCODE,
    NULL CREATE_TYPE,
    NULL SOURCE_NO,
    NULL SITEID,
    mo.cMaker REQUEST_USERID,
    mo.dCreateTime CREATE_DATE,
    mo.cVerifier MODIFY_USERID,
    CONVERT(nvarchar, mo.dVerifyTime, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    OM_MOMain mo
  WHERE
    mo.cVerifier IS NOT NULL
    AND mo.dVerifyTime IS NOT NULL
    AND CONVERT(nvarchar, mo.dVerifyTime, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  FgstockinOutsourcingSync
ORDER BY
  dModifyTime DESC
```



委外订单明细(FgstockinOutsourcingDetail)

```sql
WITH FgstockinOutsourcingDetailSync AS(
  SELECT
    CAST(od.MOID AS nvarchar) + '|' + RIGHT('000' + CAST(od.iVouchRowNo AS nvarchar), 3) FORM_RECID,
    o.MOID FORM_NO,
    RIGHT('000' + CAST(od.iVouchRowNo AS nvarchar), 3) ITEMNO,
    '委外订单' FORM_TYPE,
    NULL WORK_ORDER,
    od.cInvCode PARTID,
    NULL VERSION,
    NULL WAREHOUSEID,
    NULL ERP_LOTNO,
    NULL ERP_PROCESS,
    CAST(od.iQuantity AS DECIMAL(10, 4)) RETURN_QTY,
    0 ACTUAL_QTY,
    od.cbMemo REMARK,
    '' ITEMNO_FLAG,
    0 STOCKIN_QTY,
    NULL RTNPACKAGE_ID,
    od.bGsp QC_FLAG,
    NULL QC_RESULT,
    NULL IQC_LOTNO,
    NULL WAIVE_NO,
    NULL WRH_ORG,
    NULL SITEID,
    NULL DID,
    o.cVerifier MODIFY_USERID,
    CONVERT(nvarchar, o.dVerifyTime, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    OM_MODetails od
    INNER JOIN OM_MOMain o ON o.MOID = od.MOID
  WHERE
    o.dVerifyTime IS NOT NULL
    AND o.cVerifier IS NOT NULL
    AND CONVERT(nvarchar, o.dVerifyTime, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  FgstockinOutsourcingDetailSync
ORDER BY
  dModifyTime DESC
```



工单退料单(FgstockinReturnMaterial)

```sql
WITH FgstockinReturnMaterialSync AS(
  SELECT
    o.cCode FORM_NO,
    'Initial' FORM_STATUS,
    o.cMemo REMARK,
    '工单退料单' FORM_TYPE,
    '' FORM_ATTR,
    NULL MOVE_NO,
    NULL PROCESS_USERID,
    NULL PROCESS_STARTTIME,
    NULL PROCESS_ENDTIME,
    'I03' WRH_FORMCODE,
    NULL CREATE_TYPE,
    NULL SOURCE_NO,
    NULL SITEID,
    o.cHandler REQUEST_USERID,
    o.dnmaketime CREATE_DATE,
    (
      CASE
        WHEN o.cHandler IS NULL THEN o.cMaker
        ELSE o.cHandler
      END
    ) MODIFY_USERID,
    CONVERT(
      nvarchar,
      CASE
        WHEN o.dnverifytime IS NULL THEN o.dnmaketime
        ELSE o.dnverifytime
      END,
      121
    ) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    rdrecords11 od
    INNER JOIN rdrecord11 o ON od.ID = o.ID
  WHERE
    od.iQuantity < 0
    AND CONVERT(
      nvarchar,
      CASE
        WHEN o.dnverifytime IS NULL THEN o.dnmaketime
        ELSE o.dnverifytime
      END,
      121
    ) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  FgstockinReturnMaterialSync
ORDER BY
  dModifyTime DESC
```



工单退料单明细(FgstockinReturnMaterialDetail)

```sql
WITH FgstockinReturnMaterialDetailSync AS(
  SELECT
    CAST(o.cCode AS nvarchar) + '|' + CAST(od.AutoID AS nvarchar) FORM_RECID,
    o.cCode FORM_NO,
    CAST(od.AutoID AS nvarchar) ITEMNO,
    '工单退料单' FORM_TYPE,
    '' WORK_ORDER,
    od.cInvCode PARTID,
    'N/A' VERSION,
    o.cWhCode WAREHOUSEID,
    od.cMoLotCode ERP_LOTNO,
    NULL ERP_PROCESS,
    CAST(ABS(od.iQuantity) AS DECIMAL(10, 4)) RETURN_QTY,
    0 ACTUAL_QTY,
    od.cbMemo REMARK,
    '' ITEMNO_FLAG,
    0 STOCKIN_QTY,
    'N/A' RTNPACKAGE_ID,
    '' QC_FLAG,
    NULL QC_RESULT,
    NULL IQC_LOTNO,
    NULL WAIVE_NO,
    NULL WRH_ORG,
    NULL SITEID,
    NULL DID,
    (
      CASE
        WHEN o.cHandler IS NULL THEN o.cMaker
        ELSE o.cHandler
      END
    ) MODIFY_USERID,
    CONVERT(
      nvarchar,
      CASE
        WHEN o.dnverifytime IS NULL THEN o.dnmaketime
        ELSE o.dnverifytime
      END,
      121
    ) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    rdrecords11 od
    INNER JOIN rdrecord11 o ON od.ID = o.ID
  WHERE
    od.iQuantity < 0
    AND CONVERT(
      nvarchar,
      CASE
        WHEN o.dnverifytime IS NULL THEN o.dnmaketime
        ELSE o.dnverifytime
      END,
      121
    ) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  FgstockinReturnMaterialDetailSync
ORDER BY
  dModifyTime DESC
```



其他入库单(FgstockinOther)

```sql
WITH FgstockinOtherSync AS(
  SELECT
    o.cCode FORM_NO,
    'Initial' FORM_STATUS,
    o.cMemo REMARK,
    '其他入库单' FORM_TYPE,
    '' FORM_ATTR,
    NULL MOVE_NO,
    NULL PROCESS_USERID,
    NULL PROCESS_STARTTIME,
    NULL PROCESS_ENDTIME,
    'I01' WRH_FORMCODE,
    NULL CREATE_TYPE,
    NULL SOURCE_NO,
    NULL SITEID,
    o.cMaker REQUEST_USERID,
    o.dnmaketime CREATE_DATE,
    o.cHandler MODIFY_USERID,
    CONVERT(nvarchar, o.dnverifytime, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    RdRecord08 o
  WHERE
    o.dnverifytime IS NOT NULL
    AND o.cHandler IS NOT NULL
    AND CONVERT(nvarchar, o.dnverifytime, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  FgstockinOtherSync
ORDER BY
  dModifyTime DESC
```



其他入库单明细(FgstockinOtherDetail)

```sql
WITH FgstockinOtherDetailSync AS(
  SELECT
    o.cCode + '|' + CAST(od.AutoID AS nvarchar) FORM_RECID,
    o.cCode FORM_NO,
    CAST(od.AutoID AS nvarchar) ITEMNO,
    '其他入库单' FORM_TYPE,
    '' WORK_ORDER,
    od.cInvCode PARTID,
    'N/A' VERSION,
    o.cWhCode WAREHOUSEID,
    '' ERP_LOTNO,
    NULL ERP_PROCESS,
    CAST(od.iQuantity AS DECIMAL(10, 4)) RETURN_QTY,
    0 ACTUAL_QTY,
    od.cbMemo REMARK,
    '' ITEMNO_FLAG,
    0 STOCKIN_QTY,
    'N/A' RTNPACKAGE_ID,
    NULL QC_FLAG,
    NULL QC_RESULT,
    NULL IQC_LOTNO,
    NULL WAIVE_NO,
    NULL WRH_ORG,
    NULL SITEID,
    NULL DID,
    o.cMaker REQUEST_USERID,
    o.dnmaketime CREATE_DATE,
    o.cHandler MODIFY_USERID,
    CONVERT(nvarchar, o.dnverifytime, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    rdrecords08 od
    INNER JOIN RdRecord08 o ON od.ID = o.ID
  WHERE
    o.dnverifytime IS NOT NULL
    AND o.cHandler IS NOT NULL
    AND CONVERT(nvarchar, o.dnverifytime, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  FgstockinOtherDetailSync
ORDER BY
  dModifyTime DESC
```

### 2.2.出库作业

#### 2.2.1 材料出库

生产订单(MaterialstockoutManufacture)

```sql
WITH MaterialstockoutManufactureSync AS(
  SELECT
    DISTINCT o.MoCode FORM_NO,
    od.RelsUser REQUEST_USERID,
    CONVERT(nvarchar, o.CreateDate, 120) CREATE_DATE,
    CONVERT(nvarchar, od.RelsDate, 120) REQUEST_DATE,
    'Initial' FORM_STATUS,
    od.remark REMARK,
    '生产订单' FORM_TYPE,
    'N/A' FORM_ATTR,
    NULL MOVE_NO,
    NULL PROCESS_USERID,
    CAST(NULL AS datetime) PROCESS_STARTTIME,
    CAST(NULL AS datetime) PROCESS_ENDTIME,
    'N' PICKBYLOT_SPLIT_FLAG,
    'N' UNOPEN_FIRST_FLAG,
    'N' CHECK_BRAND,
    'ERP' CREATE_TYPE,
    'N' PICKBYAREA_FLAG,
    'N' PICKBYLOCATION,
    'N' MERGE_FLAG,
    'N' LIFO_FLAG,
    'N' RESERVE_FIRST_FLAG,
    'N' PRIORITYLOCATION,
    'N/A' SOURCE_NO,
    NULL SITEID,
    od.RelsUser MODIFY_USERID,
    CONVERT(nvarchar, od.RelsTime, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    mom_order o
    INNER JOIN mom_orderdetail od ON o.MOID = od.MOID
  WHERE
    od.RelsTime IS NOT NULL
    AND od.RelsUser IS NOT NULL
    AND CONVERT(nvarchar, od.RelsTime, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  MaterialstockoutManufactureSync
ORDER BY
  dModifyTime DESC
```



生产订单子件(MaterialstockoutManufactureDetail)

```sql
WITH MaterialstockoutManufactureDetailSync AS(
  SELECT
    CAST(o.MoCode AS nvarchar) + '|' + CAST(om.AllocateId AS nvarchar) FORM_RECID,
    CAST(o.MoCode AS nvarchar) FORM_NO,
    CAST(om.AllocateId AS nvarchar) ITEMNO,
    '生产订单' FORM_TYPE,
    '' WORK_ORDER,
    om.InvCode PARTID,
    'N/A' VERSION,
    om.WhCode WAREHOUSEID,
    om.LotNo ERP_LOTNO,
    NULL ERP_PROCESS,
    CAST(om.Qty AS DECIMAL(10, 4)) REQUEST_QTY,
    CAST(0 AS DECIMAL(10, 4)) ACTUAL_QTY,
    om.remark REMARK,
    '' ITEMNO_FLAG,
    (
      CASE
        WHEN om.QcFlag = 1 THEN 'Y'
        ELSE 'N'
      END
    ) QC_FLAG,
    NULL QC_RESULT,
    NULL IQC_LOTNO,
    od.RelsUser MODIFY_USERID,
    CONVERT(nvarchar, od.RelsTime, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    mom_moallocate om
    INNER JOIN mom_orderdetail od ON om.MoDId = od.MoDId
    INNER JOIN mom_order o ON od.MoId = o.MoId
  WHERE
    om.WIPType = 3 --只有领料的物料才同步
    AND od.RelsTime IS NOT NULL
    AND od.RelsUser IS NOT NULL
    AND CONVERT(nvarchar, od.RelsTime, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  MaterialstockoutManufactureDetailSync
ORDER BY
  dModifyTime DESC
```



委外订单(MaterialstockoutOutsourcing)

```sql
WITH MaterialstockoutOutsourcingSync AS(
  SELECT
    DISTINCT o.cCode FORM_NO,
    o.cVerifier REQUEST_USERID,
    CONVERT(nvarchar, o.dDate, 120) CREATE_DATE,
    CONVERT(nvarchar, o.dVerifyDate, 120) REQUEST_DATE,
    'Initial' FORM_STATUS,
    o.cMemo REMARK,
    '委外订单' FORM_TYPE,
    'N/A' FORM_ATTR,
    NULL MOVE_NO,
    NULL PROCESS_USERID,
    CAST(NULL AS datetime) PROCESS_STARTTIME,
    CAST(NULL AS datetime) PROCESS_ENDTIME,
    'N' PICKBYLOT_SPLIT_FLAG,
    'N' UNOPEN_FIRST_FLAG,
    'N' CHECK_BRAND,
    'ERP' CREATE_TYPE,
    'N' PICKBYAREA_FLAG,
    'N' PICKBYLOCATION,
    'N' MERGE_FLAG,
    'N' LIFO_FLAG,
    'N' RESERVE_FIRST_FLAG,
    'N' PRIORITYLOCATION,
    'N/A' SOURCE_NO,
    NULL SITEID,
    o.cVerifier MODIFY_USERID,
    CONVERT(nvarchar, o.dVerifyTime, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    OM_MOMain o
  WHERE
    o.dVerifyTime IS NOT NULL
    AND o.cVerifier IS NOT NULL
    AND CONVERT(nvarchar, o.dVerifyTime, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  MaterialstockoutOutsourcingSync
ORDER BY
  dModifyTime DESC
```



委外订单子件(MaterialstockoutOutsourcingDetail)

```sql
WITH MaterialstockoutOutsourcingDetailSync AS(
  SELECT
    CAST(o.cCode AS nvarchar) + '|' + RIGHT(
      '000' + CAST(
        (
          CASE
            WHEN len(ods.irowno) > 0 THEN ods.irowno
            ELSE 0
          END
        ) AS nvarchar
      ),
      3
    ) FORM_RECID,
    CAST(o.cCode AS nvarchar) FORM_NO,
    RIGHT(
      '000' + CAST(
        (
          CASE
            WHEN len(ods.irowno) > 0 THEN ods.irowno
            ELSE 0
          END
        ) AS nvarchar
      ),
      3
    ) ITEMNO,
    '委外订单' FORM_TYPE,
    '' WORK_ORDER,
    ods.cInvCode PARTID,
    'N/A' VERSION,
    ods.cWhCode WAREHOUSEID,
    ods.cBatch ERP_LOTNO,
    NULL ERP_PROCESS,
    CAST(ods.iQuantity AS DECIMAL(10, 4)) REQUEST_QTY,
    CAST(0 AS DECIMAL(10, 4)) ACTUAL_QTY,
    ods.cbMemo REMARK,
    '' ITEMNO_FLAG,
    'N' QC_FLAG,
    NULL QC_RESULT,
    NULL IQC_LOTNO,
    o.cVerifier MODIFY_USERID,
    CONVERT(nvarchar, o.dVerifyTime, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    OM_MOMaterials ods
    INNER JOIN OM_MODetails od ON od.MODetailsID = ods.MoDetailsID
    INNER JOIN OM_MOMain o ON o.MOID = od.MOID
  WHERE
    o.dVerifyTime IS NOT NULL
    AND o.cVerifier IS NOT NULL
    AND CONVERT(nvarchar, o.dVerifyTime, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  MaterialstockoutOutsourcingDetailSync
ORDER BY
  dModifyTime DESC
```



领料申请单(MaterialstockoutMaterial)

```sql
WITH MaterialstockoutMaterialSync AS(
  SELECT
    DISTINCT o.cCode FORM_NO,
    o.cHandler REQUEST_USERID,
    CONVERT(nvarchar, o.dDate, 120) CREATE_DATE,
    CONVERT(nvarchar, o.dVeriDate, 120) REQUEST_DATE,
    'Initial' FORM_STATUS,
    o.cMemo REMARK,
    '领料申请单' FORM_TYPE,
    'N/A' FORM_ATTR,
    NULL MOVE_NO,
    NULL PROCESS_USERID,
    CAST(NULL AS datetime) PROCESS_STARTTIME,
    CAST(NULL AS datetime) PROCESS_ENDTIME,
    'N' PICKBYLOT_SPLIT_FLAG,
    'N' UNOPEN_FIRST_FLAG,
    'N' CHECK_BRAND,
    'ERP' CREATE_TYPE,
    'N' PICKBYAREA_FLAG,
    'N' PICKBYLOCATION,
    'N' MERGE_FLAG,
    'N' LIFO_FLAG,
    'N' RESERVE_FIRST_FLAG,
    'N' PRIORITYLOCATION,
    'N/A' SOURCE_NO,
    NULL SITEID,
    o.cHandler MODIFY_USERID,
    CONVERT(nvarchar, o.dnverifytime, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    MaterialAppVouch o
  WHERE
    o.dnverifytime IS NOT NULL
    AND o.cHandler IS NOT NULL
    AND CONVERT(nvarchar, o.dnverifytime, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  MaterialstockoutMaterialSync
ORDER BY
  dModifyTime DESC
```



领料申请单子件(MaterialstockoutMaterialDetail)

```sql
WITH MaterialstockoutMaterialDetailSync AS(
  SELECT
    CAST(o.cCode AS nvarchar) + '|' + RIGHT(
      '000' + CAST(
        (
          CASE
            WHEN len(od.irowno) > 0 THEN od.irowno
            ELSE 0
          END
        ) AS nvarchar
      ),
      3
    ) FORM_RECID,
    CAST(o.cCode AS nvarchar) FORM_NO,
    RIGHT(
      '000' + CAST(
        (
          CASE
            WHEN len(od.irowno) > 0 THEN od.irowno
            ELSE 0
          END
        ) AS nvarchar
      ),
      3
    ) ITEMNO,
    '领料申请单' FORM_TYPE,
    '' WORK_ORDER,
    od.cInvCode PARTID,
    'N/A' VERSION,
    od.cWhCode WAREHOUSEID,
    '' ERP_LOTNO,
    NULL ERP_PROCESS,
    CAST(od.iQuantity AS DECIMAL(10, 4)) REQUEST_QTY,
    CAST(0 AS DECIMAL(10, 4)) ACTUAL_QTY,
    od.cbMemo REMARK,
    'N' ITEMNO_FLAG,
    'N' QC_FLAG,
    NULL QC_RESULT,
    NULL IQC_LOTNO,
    (
      CASE
        WHEN o.cModifyPerson IS NULL THEN o.cMaker
        ELSE o.cModifyPerson
      END
    ) MODIFY_USERID,
    CONVERT(nvarchar, o.dnverifytime, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    MaterialAppVouchs od
    INNER JOIN MaterialAppVouch o ON o.id = od.ID
  WHERE
    --od.WIPType = 3 AND  --只有领料的物料才同步
    o.dnverifytime IS NOT NULL
    AND o.cHandler IS NOT NULL
    AND CONVERT(nvarchar, o.dnverifytime, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  MaterialstockoutMaterialDetailSync
ORDER BY
  dModifyTime DESC
```



补料申请单(MaterialstockoutReplenishapply)

```sql
WITH MaterialstockoutReplenishapplySync AS(
  SELECT
    DISTINCT o.DocCode FORM_NO,
    o.RelsUser REQUEST_USERID,
    CONVERT(nvarchar, o.CreateDate, 120) CREATE_DATE,
    CONVERT(nvarchar, o.DocDate, 120) REQUEST_DATE,
    'Initial' FORM_STATUS,
    NULL REMARK,
    '补料申请单' FORM_TYPE,
    'N/A' FORM_ATTR,
    NULL MOVE_NO,
    o.RelsUser PROCESS_USERID,
    CAST(NULL AS datetime) PROCESS_STARTTIME,
    CAST(NULL AS datetime) PROCESS_ENDTIME,
    'N' PICKBYLOT_SPLIT_FLAG,
    'N' UNOPEN_FIRST_FLAG,
    'N' CHECK_BRAND,
    'ERP' CREATE_TYPE,
    'N' PICKBYAREA_FLAG,
    'N' PICKBYLOCATION,
    'N' MERGE_FLAG,
    'N' LIFO_FLAG,
    'N' RESERVE_FIRST_FLAG,
    'N' PRIORITYLOCATION,
    'N/A' SOURCE_NO,
    NULL SITEID,
    o.ModifyUser MODIFY_USERID,
    CONVERT(nvarchar, o.RelsTime, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    mom_replenishapply o
  WHERE
    o.RelsTime IS NOT NULL
    AND o.RelsUser IS NOT NULL
    AND CONVERT(nvarchar, o.RelsTime, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  MaterialstockoutReplenishapplySync
ORDER BY
  dModifyTime DESC
```



补料申请单子件(MaterialstockoutReplenishapplyDetail)

```sql
WITH MaterialstockoutReplenishapplyDetailSync AS(
  SELECT
    CAST(o.DocCode AS nvarchar) + '|' + RIGHT(
      '000' + CAST(
        (
          CASE
            WHEN len(od.SortSeq) > 0 THEN od.SortSeq
            ELSE 0
          END
        ) AS nvarchar
      ),
      3
    ) FORM_RECID,
    CAST(o.DocCode AS nvarchar) FORM_NO,
    RIGHT(
      '000' + CAST(
        (
          CASE
            WHEN len(od.SortSeq) > 0 THEN od.SortSeq
            ELSE 0
          END
        ) AS nvarchar
      ),
      3
    ) ITEMNO,
    '补料申请单' FORM_TYPE,
    '' WORK_ORDER,
    ods.InvCode PARTID,
    'N/A' VERSION,
    ods.WhCode WAREHOUSEID,
    ods.LotNo ERP_LOTNO,
    NULL ERP_PROCESS,
    CAST(od.ApplyQty AS DECIMAL(10, 4)) REQUEST_QTY,
    CAST(0 AS DECIMAL(10, 4)) ACTUAL_QTY,
    ods.Remark REMARK,
    '' ITEMNO_FLAG,
    'N' QC_FLAG,
    NULL QC_RESULT,
    NULL IQC_LOTNO,
    o.RelsUser MODIFY_USERID,
    CONVERT(nvarchar, o.RelsTime, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    mom_moallocate ods
    INNER JOIN mom_replenishapplydtl od ON od.MoAllocateId = ods.AllocateId
    INNER JOIN mom_replenishapply o ON o.ApplyId = od.ApplyId
  WHERE
    ods.WIPType = 3
    AND --只有领料的物料才同步
    o.RelsTime IS NOT NULL
    AND o.RelsUser IS NOT NULL
    AND CONVERT(nvarchar, o.RelsTime, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  MaterialstockoutReplenishapplyDetailSync
ORDER BY
  dModifyTime DESC
```



其他出库单(MaterialstockoutOther)

```sql
WITH MaterialstockoutOtherSync AS(
  SELECT
    DISTINCT o.cCode FORM_NO,
    o.cHandler REQUEST_USERID,
    CONVERT(nvarchar, o.dnmaketime, 120) CREATE_DATE,
    CONVERT(nvarchar, o.dDate, 120) REQUEST_DATE,
    'Initial' FORM_STATUS,
    NULL REMARK,
    '其他出库单' FORM_TYPE,
    'N/A' FORM_ATTR,
    NULL MOVE_NO,
    o.cHandler PROCESS_USERID,
    CAST(NULL AS datetime) PROCESS_STARTTIME,
    CAST(NULL AS datetime) PROCESS_ENDTIME,
    'N' PICKBYLOT_SPLIT_FLAG,
    'N' UNOPEN_FIRST_FLAG,
    'N' CHECK_BRAND,
    'ERP' CREATE_TYPE,
    'N' PICKBYAREA_FLAG,
    'N' PICKBYLOCATION,
    'N' MERGE_FLAG,
    'N' LIFO_FLAG,
    'N' RESERVE_FIRST_FLAG,
    'N' PRIORITYLOCATION,
    'N/A' SOURCE_NO,
    NULL SITEID,
    o.cHandler MODIFY_USERID,
    CONVERT(nvarchar, o.dnverifytime, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    RdRecord09 o
  WHERE
    o.dnverifytime IS NOT NULL
    AND o.cHandler IS NOT NULL
    AND CONVERT(nvarchar, o.dnverifytime, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  MaterialstockoutOtherSync
ORDER BY
  dModifyTime DESC
```



其他出库单子件(MaterialstockoutOtherDetail)

```sql
WITH MaterialstockoutOtherDetailSync AS(
  SELECT
    CAST(o.cCode AS nvarchar) + '|' + CAST(od.AutoID AS nvarchar) FORM_RECID,
    CAST(o.cCode AS nvarchar) FORM_NO,
    CAST(od.AutoID AS nvarchar) ITEMNO,
    '其他出库单' FORM_TYPE,
    '' WORK_ORDER,
    od.cInvCode PARTID,
    'N/A' VERSION,
    o.cWhCode WAREHOUSEID,
    NULL ERP_LOTNO,
    NULL ERP_PROCESS,
    CAST(od.iQuantity AS DECIMAL(10, 4)) REQUEST_QTY,
    CAST(od.iFQuantity AS DECIMAL(10, 4)) ACTUAL_QTY,
    od.cbMemo REMARK,
    '' ITEMNO_FLAG,
    'N' QC_FLAG,
    NULL QC_RESULT,
    NULL IQC_LOTNO,
    o.cHandler MODIFY_USERID,
    CONVERT(nvarchar, o.dnverifytime, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    Rdrecords09 od
    INNER JOIN RdRecord09 o ON od.ID = o.ID
  WHERE
    --od.WIPType = 3 AND  --只有领料的物料才同步
    o.dnverifytime IS NOT NULL
    AND o.cHandler IS NOT NULL
    AND CONVERT(nvarchar, o.dnverifytime, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  MaterialstockoutOtherDetailSync
ORDER BY
  dModifyTime DESC
```

#### 2.2.2 发货备料

销售发货退货单(Shipping)

```sql
WITH ShippingSync AS(
  SELECT
    DISTINCT CAST(d.cDLCode AS nvarchar) SHIPPING_NO,
    d.cCusCode CUSTOMERID,
    d.cPersonCode SALER_USERID,
    CONVERT(nvarchar, d.dDate, 111) CREATE_DATE,
    d.dverifydate SCHEDULE_DATE,
    'Initial' SO_STATUS,
    d.cMemo REMARK,
    d.cBusType SO_TYPE,
    d.cmodifier MODIFY_USERID,
    NULL CREATE_TYPE,
    NULL TRANSPORT_TYPE,
    'N' PICKBYLOT_SPLIT_FLAG,
    'N' CHECK_BRAND,
    'N' RESERVE_FIRST_FLAG,
    'N' PICKBYAREA_FLAG,
    'N' PICKBYLOCATION,
    'N' MERGE_FLAG,
    'N' LIFO_FLAG,
    'N' PRIORITYLOCATION,
    'N' UNOPEN_FIRST_FLAG,
    'N' SOURCE_NO,
    NULL SITEID,
    CONVERT(nvarchar, d.dverifysystime, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    DispatchList d
  WHERE
    d.dverifysystime IS NOT NULL
    AND CONVERT(nvarchar, dverifysystime, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  ShippingSync
ORDER BY
  dModifyTime desc
```



销售发货退货单明细(ShippingDetail)

```sql
WITH ShippingDetailSync AS(
  SELECT
    DISTINCT CAST(d.cDLCode AS nvarchar) + '|' + RIGHT('000' + CAST(ds.irowno AS nvarchar), 3) SHIPPING_RECID,
    CAST(d.cDLCode AS nvarchar) SHIPPING_NO,
    RIGHT('000' + CAST(ds.irowno AS nvarchar), 3) ITEMNO,
    ds.cInvCode PARTID,
    ds.cBatch VERSION,
    cBatch ERP_LOTNO,
    ds.cWhCode WAREHOUSEID,
    CAST(ds.iQuantity AS DECIMAL(10, 4)) SOQTY,
    CAST(fOutQuantity AS DECIMAL(10, 4)) SHIP_QTY,
    iMassDate WARRANTY_MONTH,
    NULL CUSTOMER_SITE,
    d.cShipAddress SHIP_ADDRESS,
    NULL PO_NO,
    NULL PO_ITEMNO,
    ds.cMemo REMARK,
    d.cmodifier MODIFY_USERID,
    NULL SHIP_ADDRESS2,
    NULL CREATE_TYPE,
    NULL ITEMNO_FLAG,
    NULL SCHEDULE_DATE,
    NULL SITEID,
    NULL DID,
    CONVERT(nvarchar, d.dverifysystime, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    DispatchLists ds
    INNER JOIN DispatchList d ON ds.DLID = d.DLID
  WHERE
    d.dverifysystime IS NOT NULL
    AND CONVERT(nvarchar, dverifysystime, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  ShippingDetailSync
ORDER BY
  dModifyTime desc
```



### 2.3. 调拨作业

调拨申请单(Transfer)

```sql
WITH TransferSync AS(
  SELECT
    DISTINCT v.cTVCode FORM_NO,
    '调拨申请单' FORM_TYPE,
    CONVERT(nvarchar, v.dnmaketime, 121) CREATE_DATE,
    'Initial' FORM_STATUS,
    'N/A' FORM_ATTR,
    v.cTVMemo REMARK,
    v.cOWhCode FROM_WRHID,
    v.cIWhCode TARGET_WRHID,
    v.cMaker REQUEST_USERID,
    v.cVerifyPerson PROCESS_USERID,
    v.cModifyPerson MODIFY_USERID,
    CONVERT(nvarchar, v.dnverifytime, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    ST_AppTransVouch v
  WHERE
    v.dnverifytime IS NOT NULL
    AND v.cVerifyPerson IS NOT NULL
    AND CONVERT(nvarchar, v.dnverifytime, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  TransferSync
ORDER BY
  dModifyTime DESC
```



调拨申请单明细(TransferDetail)

```sql
WITH TransferDetailSync AS(
  SELECT
    CAST(v.cTVCode AS nvarchar) + '|' + RIGHT('000' + CAST(vs.irowno AS nvarchar), 3) FORM_RECID,
    v.cTVCode FORM_NO,
    RIGHT('000' + CAST(vs.irowno AS nvarchar), 3) ITEMNO,
    '调拨申请单' FORM_TYPE,
    'Initial' FORM_STATUS,
    'N/A' FORM_ATTR,
    v.cTVMemo REMARK,
    vs.cInvCode PARTID,
    'N/A' VERSION,
    CAST(iTVQuantity AS DECIMAL(10, 4)) MOVE_QTY,
    v.cOWhCode FROM_WRHID,
    'N/A' FROM_LOCID,
    v.cIWhCode TARGET_WRHID,
    'N/A' TARGET_LOCID,
    CAST(iTVQuantity AS DECIMAL(10, 4)) FROM_QTY,
    CAST(iTvSumQuantity AS DECIMAL(10, 4)) TARGET_QTY,
    v.cMaker FROM_USERID,
    CONVERT(nvarchar, v.dnmaketime, 121) FROM_DATETIME,
    v.cMaker REQUEST_USERID,
    CONVERT(nvarchar, v.dnmaketime, 121) CREATE_DATE,
    v.cVerifyPerson PROCESS_USERID,
    v.cModifyPerson MODIFY_USERID,
    CONVERT(nvarchar, v.dnverifytime, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    ST_AppTransVouchs vs
    INNER JOIN ST_AppTransVouch v ON vs.ID = v.ID
  WHERE
    v.cVerifyPerson IS NOT NULL
    AND v.dnverifytime IS NOT NULL
    AND CONVERT(nvarchar, v.dnverifytime, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  TransferDetailSync
ORDER BY
  dModifyTime DESC
```



### 2.4. 退货作业

#### 2.4.1 仓库退货

采购（委外）退货单(ReturnVendor)

```sql
WITH ReturnVendorSync AS(
  SELECT
    ph.cCode FORM_NO,
    ph.cMaker REQUEST_USERID,
    CONVERT(nvarchar, ph.cMakeTime, 121) CREATE_DATE,
    'Initial' FORM_STATUS,
    ph.cMemo REMARK,
    (
      CASE
        WHEN ph.cBusType = '普通采购' THEN '采购退货单'
        ELSE '委外退货单'
      END
    ) FORM_TYPE,
    NULL FORM_ATTR,
    NULL [ PROCESS_USERID ],
    NULL [ PROCESS_STARTTIME ],
    NULL [ PROCESS_ENDTIME ],
    (
      CASE
        WHEN ph.cchanger IS NULL THEN ph.cMaker
        ELSE ph.cchanger
      END
    ) MODIFY_USERID,
    v.cVenCode VENDORID,
    CONVERT(nvarchar, ph.caudittime, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    PU_ArrivalVouch ph
    INNER JOIN vendor v ON v.cVenCode = ph.cVenCode
  WHERE
    ph.cverifier IS NOT NULL
    AND ph.caudittime IS NOT NULL
    AND ph.cCloser IS NULL
    AND (
      ph.cBusType = '普通采购'
      OR ph.cBusType = '委外加工'
    )
    AND ph.bNegative = 1
    AND CONVERT(nvarchar, ph.caudittime, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  ReturnVendorSync
ORDER BY
  dModifyTime desc
```



采购（委外）退货单明细(ReturnVendorDetail)

```sql
WITH ReturnVendorDetailSync AS(
  SELECT
    CAST(ph.cCode AS nvarchar) + '|' + RIGHT(
      '000' + CAST(
        (
          CASE
            WHEN len(phs.ivouchrowno) > 0 THEN phs.ivouchrowno
            ELSE 0
          END
        ) AS nvarchar
      ),
      3
    ) FORM_RECID,
    ph.cCode FORM_NO,
    RIGHT(
      '000' + CAST(
        (
          CASE
            WHEN len(phs.ivouchrowno) > 0 THEN phs.ivouchrowno
            ELSE 0
          END
        ) AS VARCHAR
      ),
      3
    ) ITEMNO,
    phs.cInvCode PARTID,
    'N/A' VERSION,
    CAST(phs.iQuantity AS DECIMAL(10, 4)) RETURN_QTY,
    0 ACTUAL_QTY,
    'Initial' PICK_STATUS,
    phs.cWhCode WAREHOUSEID,
    'N/A' ITEMNO_FLAG,
    (
      CASE
        WHEN ph.cchanger IS NULL THEN ph.cMaker
        ELSE ph.cchanger
      END
    ) MODIFY_USERID,
    CONVERT(nvarchar, ph.caudittime, 121) dModifyTime,
    CONVERT(bit, 0) IsSync
  FROM
    PU_ArrivalVouchs phs
    INNER JOIN PU_ArrivalVouch ph ON ph.id = phs.id
    AND phs.cCloser IS NULL
  WHERE
    ph.cverifier IS NOT NULL
    AND ph.caudittime IS NOT NULL
    AND ph.cCloser IS NULL
    AND (
      ph.cBusType = '普通采购'
      OR ph.cBusType = '委外加工'
    )
    AND ph.bNegative = 1
    AND phs.bGsp = 0
    AND CONVERT(nvarchar, ph.caudittime, 121) > CONVERT(nvarchar, '${LastDatetime_}', 121)
)
SELECT
  *
FROM
  ReturnVendorDetailSync
ORDER BY
  dModifyTime desc
```

























