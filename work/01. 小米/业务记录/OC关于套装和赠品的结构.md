套装和多品的关联关系记录逻辑纪要

@王超 @徐冲 @王碰 @Jamie 吴静姗 @Gary Guo 国策

1. 如果只是单纯的套装，也在防套利的结构里记录关联关系，方便后续防套利有规则了以后计算。目前售后应该是通过订单商品上的套装ID来判断是否是套装，没有用这个字段，若不用忽略即可 @Gary Guo 国策 。
    

```
[{
    "promotion_id": "21539", // 活动 ID
    "promotion_mode": 1, // 防套利形式，1 套装
    "master_threshold_amount": 0, // 触发防套利阈值，套装为 0，必然触发
    "master_goods" [{
        "item_no": [1], // 商品行，有可能因为扣到不同仓或金额分摊不均导致拆商品行
        "sale_count": 1,  // 套装中该商品的数量，非购买总数
        "promotion_amount": "10", // 优惠的金额，单价
    }, {
        "item_no": [2],
        "sale_count": 2,  // 套装中该商品的数量，非购买总数
        "promotion_amount": "30", // 优惠的金额,单价
    }]
}]
```

2. 套装+赠品时或套装+加价购，传一个套装结构，传一个主赠结构，其中主赠结构里主品里传价值高的1个商品即可
    

```
[{   // 套装防套利规则
    "promotion_id": "2153", // 活动 ID
    "promotion_mode": 1, // 防套利形式，1 套装
    "master_threshold_amount": 0, // 触发防套利阈值，套装为 0，必然触发
    "master_goods" [{
        "item_no": [1], // 商品行，有可能因为扣到不同仓或金额分摊不均导致拆商品行
        "sale_count": 1,  // 套装中该商品的数量，非购买总数
        "promotion_amount": "10", // 优惠的金额，单价
    }, {
        "item_no": [2],
        "sale_count": 2,  // 套装中该商品的数量，非购买总数
        "promotion_amount": "30", // 优惠的金额,单价
    }]
}, { // 套装赠送一个赠品
    "promotion_id": "21539", // 活动 ID
    // 如买 3 赠 1，买 7 就会赠 2， 这里只记活动形式为买 3 赠 1
    "promotion_mode": 2, // 防套利形式，2 满件赠品， 3 满件加价购 
    "master_threshold_amount": 0, 
    "master_goods" [{
        "item_no": [2], // 套装最高值的商品对应商品行
        "sale_count": 2, // 套装中最高值商品的购买数量
        "promotion_amount": "0", 
    }],
    "gift_goods": [{
        "item_no": [3],
        "sale_count": 1,
        "promotion_amount": "33", // 优惠的金额
        // 原价 = xm_order_item.shop_price + promotion_amount
    }]
}]
```

3. 如果是同一个活动，两个商品对应两个赠品，优惠金额不同，则传两个结构
    

```
[{
 "promotion_id": "dsd", // 促销活动的id
 "promotion_mode": 2, // 防套利形式，2 满件赠品， 3 满件加价购 
 "master_threshold_amount": 0, // 触发防套利阈值，买赠为 0，必然触发
 "master_goods" [{
 "item_no": [1], // 商品行，有可能因为扣到不同仓或金额分摊不均导致拆商品行
 "sale_count": 2, // 主品中该商品的数量，非购买总数
 "promotion_amount": "0", 
 }],
 "gift_goods": [{
 "item_no": [4],
 "sale_count": 1,
 "promotion_amount": "33", // 优惠的金额
 // 原价 = xm_order_item.shop_price + promotion_amount
 }]
},{
 "promotion_id": "dsd", // 促销活动的id
 "promotion_mode": 2, // 防套利形式，2 满件赠品， 3 满件加价购 
 "master_threshold_amount": 0, // 触发防套利阈值，买赠为 0，必然触发
 "master_goods" [{
 "item_no": [2], // 商品行，有可能因为扣到不同仓或金额分摊不均导致拆商品行
 "sale_count": 2, // 主品中该商品的数量，非购买总数
 "promotion_amount": "0", 
 }],
 "gift_goods": [{
 "item_no": [5],
 "sale_count": 1,
 "promotion_amount": "30", // 优惠的金额
 // 原价 = xm_order_item.shop_price + promotion_amount
 }]
}
]
```