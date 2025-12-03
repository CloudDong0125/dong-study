

### 使用方式

```tsx
@Component
struct BalanceItem  {
  @Prop label: string;
  @Prop value: number;

  build() {
    Column() {
      Text(this.label).TextStyle({fontSize:12,fontColor:"#777777"})
      Text((this.value?? 0).toFixed(2)).TextStyle({fontSize:12,fontColor:"#323232"})
    }.justifyContent(FlexAlign.SpaceAround);
  }
}
```

### 写法一：

```tsx

@Extend(Column) function customerPackageManageStyle() {
  .width('100%')
  .backgroundColor(Color.White)
  .borderRadius(10)
  .alignItems(HorizontalAlign.Start)
  .margin({top:10})
  .padding({top:13.5,left:12,bottom:13.5,right:12,})
  .shadowStyle()
}

@Extend(Column) function shadowStyle() {
  .shadow({
    radius: 16,
    color: '#EEEEEE',
    offsetY: 2
  })
}

```

### 写法二：

```tsx

@Styles function ColumnStyle() {
  .margin({top:18})
  .width('100%')
  .padding({top:16,bottom:16})

}
@Styles function topRightImage() {
  .width(20)
  .height('auto')
  .margin({top:18,right:0})
}
```

### 写法三：

```tsx
interface TextStyleParams {
  fontColor?: string;
  fontSize?: number;
  fontWeight?: FontWeight;
}

@Extend(Text)
function TextStyle(params?: TextStyleParams) {
  .fontColor(params?.fontColor ?? '#323232')
  .fontSize(params?.fontSize ?? 14)
  .fontWeight(params?.fontWeight ?? FontWeight.Normal)
}

```

