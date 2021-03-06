# 图库
## 应用图片列表

```html
GET - /image/list
```

| 参数          | 是否必填 | 说明                |
| ----------- | ---- | ----------------- |
| create_time | 否    | 时间区间[开始时间戳,结束时间戳] |
| page        | 是    | 页数                |
| rows        | 是    | 条数，最大30条          |

## 微信图片列表

```html
GET - /image/wechat
```

| 参数     | 是否必填 | 说明                                      |
| ------ | ---- | --------------------------------------- |
| offset | 是    | 从全部素材的该偏移位置开始返回，可选，默认 `0`，0 表示从第一个素材 返回 |
| count  | 是    | 返回素材的数量，可选，默认 `20`, 取值在 1 到 20 之间       |

## 上传图片

```html
POST - /image/add
```

| 参数      | 是否必填 | 说明                |
| ------- | ---- | ----------------- |
| name    | 否    | 图片名               |
| image   | 是    | 数据格式支持：base64     |
| is_save | 否    | 是否保存到图库，0否1是，默认为0 |

- todo 完事image类，写出文档
- image的result类封装

## 图片删除

```html
POST - /image/del
```

| 参数 | 是否必填 | 说明   |
| ---- | -------- | ------ |
| id   | 是       | 图片id |



## 提取网络图片【v1不开发】

```html
POST - /image/remoteGrab
```

| 参数   | 是否必填 | 说明   |
| ---- | ---- | ---- |
| url  | 是    | 链接地址 |

## 商品图

```html
GET - /image/goodsImageList
```

| 参数       | 是否必填 | 说明      |
| -------- | ---- | ------- |
| keywords | 否    | 商品标题关键词 |
| page     | 是    | 页数      |
| rows     | 是    | 条数      |

