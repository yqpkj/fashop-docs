# 地区
## 地区列表

```html
GET - /area/list
```

| 参数    | 是否必填 | 说明                                       |
| ----- | ---- | ---------------------------------------- |
| pid   | 否    | 父级id                                     |
| level | 否    | level 层级 规则：设置显示下级行政区级数（行政区级别包括：省/直辖市、市、区/县3个级别）可选值：0、1、2  0：返回省/直辖市；1：返回省/直辖市、市；2：返回省/直辖市、市、区/县 |

```json
[
  {
    "id" : 1111111,
    "name" : "天津",
    "pid" : 0,
    ...其他字段,
    "_childs" : [ ... ] // 当层级不为1时才有
  },
  ....
]
```
