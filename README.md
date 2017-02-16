
## Hifond3.0配置文档

为了更简单快捷的使用Hifond设备，我们提供了配置化的开发方式，用户只需要修改简单的配置，就可以实现基础的业务需求

---

## 业务
* **门诊缴费**: *使用条形码或医保卡查询待交费信息,使用支付宝或微信进行交费。
* **住院押金补缴**: 使用住院号查询押金余额,选择补缴金额后通过支付宝或微信进行押金补缴。
* **充值**: 使用诊疗卡查询就诊人信息，选择充值金额后通过支付宝或者微信进行充值。
* **预约取号**: 使用身份证、登记号、医保卡进行取号，使用支付宝、微信进行取号缴费。
* **报到**: 使用就诊卡、条形码进行报到。

---
## 配置方式
我们通过在浏览器上的`window`对象上绑定一个`HifondConfig`对象的方式来进行配置。  
用户下载我们的代码后，直接修改`public/config/HifondConfig.js`文件就可以完成自定义配置。

### 公共头
配置在公共头里的数据会添加到项目中所有的请求参数里
```javascript
{
  public: {
    // 公共头
    // ...
  }
}
```
![Markdown](http://p1.bpimg.com/585622/37f7031fed5ec036.png)

### 业务头
配置在业务头里的数据会添加到项目中具体业务的请求参数里
```javascript
{
  // 公共头...
  payment: {
    public: {
      // 业务头
      // ...
    }
  }
}
```
![Markdown](http://p1.bpimg.com/585622/183be9d59fca6bfb.png)
> api请求参数 = 公共头 + 业务头 + api参数  
> 当我们的api中有很多重复且相同的参数的时候，我们可以把它抽取到公共头或业务头中

### **业务** 门诊交费
```javascript
payment: {
    /* 交费 公共信息 */
    public: {
      // 业务类型
      BUS_TYP: 'MZJF',
    },
    /* 页面一 欢迎页面 */
    /* 页面二 缴费类型选择  自助交费|医保交费  */
    /* 页面三 刷卡页面  */
    // 查询待交费信息接口 用户刷卡 => 根据卡号查询待交费列表信息
    list: {
      // 请求接口
      url: '/medwap/hif6_id_card.xhtml',
      req: {
        // 卡类型
        cardType: 'CARD_TYP',
        // 卡号
        cardNo: 'CARD_NO',
      },
      res: {
        // 患者姓名
        patientName: 'PAT_NM',
        // 交费记录条数
        recordNumber: 'REC_NUM',
        // 交费记录数据集合名称
        paymentList: 'REC',
        // 交费金额
        payMoney: 'SUM_FEE',
        // 交费记录数据集合明细
        paymentDetail: {
          // 订单编号
          orderID: 'HIS_ORD_NO',
          // 项目名称
          projectName: 'ITM_NM',
          // 科室
          departmentName: 'DEP_NM',
          // 医生
          doctorName: 'DOC_NM',
          // 订单金额
          payMoney: 'SUM_FEE',
        },
      },
    },
    qrInfo: {
      url: '/medwap/hif6_treat_confirm.xhtml',
      req: {
        orderID: 'HIS_ORD_NO', 
        // REC 中的key
        cardName: 'CARD_NM', 
        // 卡拥有者名字
        cardType: 'CARD_TYP', 
        // 卡类型
        cardNumber: 'CARD_NO', 
        // 卡编号
      },
      res: {
        ZFB_orderNumber: 'PAY_ORD_NO', 
        // 支付宝编码
        WX_orderNumber: 'WX_ORD_NO', 
        // 微信编码
        WX_QR_url: 'WX_QR', 
        // 微信二维码地址
        ZFB_QR_url: 'ZFB_QR', 
        // 支付宝二维码地址
        patientName: 'PAT_NM', 
        // 病人名字
        sumMoney: 'SUM_FEE',  
        // 总金额
        totalMoney: 'TOT_FEE', 
        // 自费金额
        medicalMoney: 'REC_PAY_AMT', 
        // 医保金额
      },
    },
    result: {
      url: '/medwap/scan_pay_status.xhtml',
      req: {
        orderNumber: 'ORD_NO', 
        //订单编号
      },
      res: {
        orderState: 'ORD_STS',
        // 订单状态
        orderText: 'GUID_INF', 
        // 订单反馈信息
        receipt: 'KEY_ELE4',
        // 保留字段
      }
    },
    /* 页面五 展示交费二维码页面  */
  },
```

