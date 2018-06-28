# CSS TIPS

## border-radius
使用“/”可以设置border-radius形状为椭圆

    #example-five {
      border-radius: 10px/30px; /* horizontal radius / vertical radius */
    }

## animation-fill-mode
该属性规定动画在播放之前或之后，其动画效果是否可见

|    值    | 描述                                                  |
|   ----   |  ----- | ------ |        ----------------------       |
| none     | 不改变，默认值                                         |
| forwards | 当动画完成后，保持最后一个关键帧的属性                   |
| backwards| 在animation-delay期间，动画显示之前应用第一个关键帧的属性 |
| both     | 同时应用forwards、backwards的特性                       |