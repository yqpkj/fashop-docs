# 购买
## 计算购买项

```html
POST - /buy/calculate
```

| 参数         | 是否必填 | 说明      |
| ---------- | ---- | ------- |
| cart_ids   | 是    | 购物车id集合 |
| address_id | 是    | 地址id    |

返回：

```json
{
    "goods_amount" : 1000,// 商品总额
    "pay_amount" : 10000,// 实付，包含运费
    "goods_freight_list" : [
        {
            "goods_sku_id" : 12,
            "freight_fee" : 2,// 商品运费
            "freight_way" : "goods_freight_template" // goods_freight_template 运费模板 goods_freight_unified 统一运费
        }
    ],
    "freight_unified_fee":200,// 统一运费总额
    "freight_template_fee" : 200,// 运费模板总额
    "pay_freight_fee":200 // 实付运费，具体算法看《运费计算规则》 
}
```



## 创建购买订单

```html
POST - /buy/create
```

| 参数         | 是否必填 | 说明                                   |
| ---------- | ---- | ------------------------------------ |
| way        | 否    | 购买途径，`cart` 购物车（默认）、`right_now` 立即购买 |
| address_id | 是    | 收货地址id                               |
| cart_ids   | 是    | 购物车id集合                              |
| message    | 否    | 买家留言                                 |

返回：

```json
{
    "order_id" : "xxxxxxxxxxxx",// 订单id
    "pay_sn" : "xxxxxxxxxxxxx"// 订单支付的sn
}
```



## 支付信息

```html
GET - /buy/info
```

| 参数     | 是否必填 | 说明     |
| ------ | ---- | ------ |
| pay_sn | 是    | 订单的sn码 |

返回：需要吊起支付的配置

## 支付

```html
POST - /buy/pay
```

| 参数           | 是否必填 | 说明                                       |
| ------------ | ---- | ---------------------------------------- |
| order_type   | 否    | 订单支付类型，goods_buy商品购买（默认），predeposit预存款充值 |
| pay_sn       | 是    | 订单的sn码                                   |
| payment_code | 是    | 支付方式的标识码，在该接口上级的逻辑的接口上有返回，用来调起服务器端的支付方式的策略 |
| openid       | 是    | 微信的openid 非必需 只有微信支付的情况下可能需要             |

## 运费计算规则

## 购买商品申请，todo这步是否还有必要，返回计算完的价格，因为有运费模板

```html
POST - /buy/apply
```

| 参数                      | 是否必填 | 说明                                       |
| ----------------------- | ---- | ---------------------------------------- |
| cart_ids                | 是    | 购买项，数组                                   |
| cart_items.cart_id【废弃】  | 是    | 购物车id【废弃】                                |
| cart_items.quantity【废弃】 | 是    | 购买量                                      |
| way【废弃】                 | 是    | 购买途径，`cart` 购物车、`right_now` 立即购买，todo是否需要废弃，如果有优惠券的话也单独接口请求 |
| cart_no_need_ids【废弃】    | 否    | 不再需要的购物车id，数组，系统收到提交后还会删除对应的购物车项【这个没啥用】  |

> 需要告诉用户
>
> 商品总金额
>
> 运费
>
> 实付款

返回：

| 字段           | 说明   |
| ------------ | ---- |
| total_amount | 总额   |
| freight_fee  | 运费   |
| real_pay     | 实付   |

### 运费计算规则

**一.商品关联运费模板**
1、商品关联设置：每件商品，仅可设置一种运费收取方式，即：统一运费，或指定运费模板； 
2、统一运费：按商品一次计收运费；如，统一运费10元，买家单笔订单购买n件，均只收取10元运费；
3、运费模板：选择相应的运费模板，即可指定该商品，可配送至相应区域，并按规则收取相应运费； 
**二.运费计算方式**
支持商家选择【按商品累加运费】和【组合运费】，默认使用目前微商城使用的按照商品累加运费，推荐使用组合运费。
**1.按商品累加运费**
​    1）不同或相同的商品，设置同一运费模板：按该模板设置的规则计算
​         更新：不足续件或者续重的数目的时候，仍然按照续件或者续重的数目进行计算。
​         例如商品A，B都是用模板M（首件10块，续2件5块），如果购买商品A和B，各一件，则一共购买两件，运费=10+5=15元。
​    2）多种商品，分别设置不同金额的统一运费：以最高运费金额收取。
​         更新：例如 商品A，B，C的统一运费分别为1元，2元和3元，一期购买这三个商品，则运费为3元。
​    3）不同的商品，设置不同的运费模板：分别计算每个运费模板规则应收运费，再累加计算合计运费；
​          例如： 例如商品A（1kg）使用用模板M（首1kg，10块，续2kg，5块），商品B（0.5kg）使用模板N（首2kg，12块，续1kg，5块），如果购买商品A和B，各2件，则运费=模板M的运费+模板N的运费=（10+5）+（12）=27元。
​    4）统一运费商品，和运费模板商品一同结算：单独计算统一运费商品应收运费，再累加运费模板应收运费；
​         例如：商品A和B使用统一运费，分别为2元和10元，商品C使用模板M（首1件，10块，续2件，5块），商品D（1kg）使用模板N（首2kg，12块，续1kg，5块），购买A，B，C和D各两件。
​         此时统一运费（商品A和B）=10元；运费模板运费（商品C和D）=模板M的运费+模板N的运费=（10+5）+（12）=27元。
​         则总运费=统一运费+运费模板运费=（10）+（27）=37元。
**2.组合运费**
运费模板计费原则：取所有商品中，首件/首重金额最大的运费模板，计算使用该模板的所有商品运费，使用其他模板的商品都按照续件/续费金额来计算，再求和。
 1）不同或相同的商品，设置同一运费模板：同按商品累加运费的计算方式，按该模板设置的规则计算。
​         例如商品A，B都是用模板M（首件10块，续2件5块），如果购买商品A和B，各一件，则一共购买两件，运费=10+5=15元。
​    2）多种商品，分别设置不同金额的统一运费：同按商品累加运费的计算方式，以最高运费金额收取。
​          例如 商品A，B，C的统一运费分别为1元，2元和3元，一期购买这三个商品，则运费为3元。
​    3）不同的商品，设置不同的运费模板：不同于按照商品累加的计算方式。
​          例如： 例如商品A（1件）使用用模板M（首1件，10块，续1件，5块），商品B（1kg）使用模板N（首2kg，12块，续1kg，5块），如果购买商品A和B，各2件，则运费=模板N的运费（首费为12，大于模板M的10）+模板N的运费（按照续费计算）=（12）+（5*2）=22元。
​          如果按商品累加计费，则运费=模板M的运费+模板N的运费=（10+5）+（12）=27元。
​    4）统一运费商品，和运费模板商品一同结算：单独计算统一运费商品应收运费，再累加运费模板应收运费；
​         例如：商品A和B使用统一运费，分别为2元和10元，商品C使用模板M（首1件，10块，续2件，5块），商品D（1kg）使用模板N（首2kg，12块，续1kg，5块），购买A，B，C和D各两件。
​         此时统一运费（商品A和B）=10元；运费模板运费（商品C和D）=模板N的运费（首费为12，大于模板M的10）+模板N的运费（按照续费计算）=（12）+（5*2）=22元。总运费=总运费=统一运费+运费模板运费=（10）+（22）=32元。
​         如果按商品累加计费，运费按照之前的计算=统一运费+运费模板运费=（10）+（27）=37元。

```html
POST - /buy/createOrder
```

| 参数                      | 是否必填 | 说明                               |
| ----------------------- | ---- | -------------------------------- |
| way                     | 是    | 购买途径，`cart` 购物车、`right_now` 立即购买 |
| pay_name                | 是    | 支付方式code，如：`wechat` 微信支付         |
| address_id              | 是    | 收获地址id                           |
| invoice_id              | 否    | 发票id，v1 无                        |
| cart_items              | 是    | 购买项，数组                           |
| cart_items.goods_sku_id | 是    | 购物车id                            |
| cart_items.quantity     | 是    | 购买量                              |
| buy_message             | 否    | 买家带话                             |
| voucher_id              | 否    | 要使用的优惠券id，v1无                    |
