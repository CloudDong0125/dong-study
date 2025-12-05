# 需求

| 需求                                    |
| --------------------------------------- |
| 1、滚动的时候，自动的切换到对应的tab栏  |
| 2、点击的时候，页面自动滚动到对应的高度 |
|                                         |

## 一、自动的切换到对应的tab栏

### 1、实现思路

```mermaid
graph TD
    A[开始: 选项卡锚点滚动系统] --> B[初始化阶段]
    
    subgraph B[初始化阶段]
        B1["定义状态变量<br/>@State tabsItems, sectionOffsets等"]
        B2[定义锚点ID数组<br/>sectionIds]
        B3[创建Scroller实例]
        B4["设置初始标志<br/>offsetsInitialized: false"]
    end
    
    B --> C[UI构建阶段]
    
    subgraph C[UI构建]
        C1[ComTabs组件<br/>顶部选项卡导航]
        C2[Scroll组件<br/>主内容滚动区]
    end
    
    C1 --> D1[onTabClick事件<br/>点击选项卡]
    D1 --> E1[更新currentIndex]
    D1 --> E2["调用tabScrollArea(index)"]
    
    C2 --> D2[构建三个内容区块]
    D2 --> E3[buildCustomerSection]
    D2 --> E4[buildPackageSection]
    D2 --> E5[buildOrderSection]
    D2 --> E6["补充高度Column<br/>height=this.ColumnHeight"]
    
    C2 --> D3[事件监听]
    D3 --> E7[onSizeChange<br/>更新scrollHeight]
    D3 --> E8[onSizeChange<br/>更新screenHeight]
    D3 --> E9[onScrollStop<br/>滚动停止时处理]
    
    E3 & E4 & E5 --> F[comColumnId装饰器]
    F --> G[onAreaChange回调]
    G --> H[记录区块偏移量<br/>更新sectionOffsets]
    H --> I[updateSectionOffsets检查]
    I --> J{"是否全部初始化?"}
    J -->|是| K["offsetsInitialized = true"]
    J -->|否| L[延迟重试]
    
    E2 --> M[tabScrollArea逻辑]
    
    subgraph M[选项卡点击处理]
        M1[scrollHeight复原<br/>减去ColumnHeight]
        M2["计算高度差值diff<br/>scrollHeight - sectionOffsets[index]"]
        M3["判断是否需要补充高度<br/>if (screenHeight - diff > 0)"]
        M4["动态调整ColumnHeight<br/>screenHeight - diff 或 0"]
        M5[调用scrollToSection]
    end
    
    M5 --> N[scrollToSection执行]
    
    subgraph N[滚动定位]
        N1[参数验证<br/>index范围检查]
        N2["调用scroller.scrollTo"]
        N3["设置yOffset=sectionOffsets[index]"]
        N4[添加动画效果]
    end
    
    E9 --> O[handleScrollPosition<br/>滚动停止同步]
    O --> P[更新当前激活的选项卡]
    
    %% 数据流连接
    E7 -.->|提供scrollHeight| M1
    E8 -.->|提供screenHeight| M3
    H -.->|提供偏移量数据| M2
    H -.->|提供偏移量数据| N3
    
    %% 样式定义
    classDef init fill:#e1f5fe,stroke:#01579b,stroke-width:2px
    classDef ui fill:#f3e5f5,stroke:#4a148c,stroke-width:2px
    classDef dynamic fill:#fff3e0,stroke:#ef6c00,stroke-width:2px
    classDef logic fill:#e8f5e8,stroke:#1b5e20,stroke-width:2px
    classDef event fill:#fce4ec,stroke:#ad1457,stroke-width:2px
    classDef data fill:#e0f2f1,stroke:#00695c,stroke-width:2px
    
    class B1,B2,B3,B4 init
    class C1,C2,D2,E3,E4,E5 ui
    class E6 dynamic
    class M1,M2,M3,M4,N1,N2,N3,N4,O logic
    class D1,E7,E8,E9,G event
    class H,I,J data
```



### 2、代码实现

#### 1)`CustomerPage.ets`

《进入系统-客户管理-客户资料》`src/main/ets/pages/CustomerPage/CustomerPage.ets`

![image-20251205164453221](./assets/image-20251205164453221.png)



```tsx
	
  @State tabsItems: string[] = ['客户资料', '套餐管理', '近期单据']; 
// 内容区域锚点标识（与导航项顺序对应）
  private readonly sectionIds: string[] = [
    'customerSection',
    'packageSection',
    'orderSection'
  ]
  // 区块偏移量存储（单位：vp）
  @State private sectionOffsets: number[] = [];
  // 标记 sectionOffsets 是否已经初始化
  @State private offsetsInitialized: boolean = false;
  @State ColumnHeight: number = 0
  @State scrollHeight: number = 0
  @State screenHeight: number = 0
```



```tsx
 // 导航选项卡
 ComTabs({
          tabsItems: this.tabsItems,
          currentIndex: $currentTabIndex,
          onTabClick: (index: number) => {
            this.currentIndex = index;
            this.tabScrollArea(index)
          }
        })       

// 内容滚动区
Scroll(this.scroller) {
  Column(){
    // 客户资料
    this.buildCustomerSection()
    // 套餐管理
    this.buildPackageSection()
    // 近期单据
    this.buildOrderSection()
    
    // 补充高度
   	Column().width('100%').height(this.ColumnHeight).backgroundColor(Color.White)
  }
  				.width('100%')
          .onSizeChange((oldValue: SizeOptions, newValue: SizeOptions) => {
            // console.info(`Ace: on size change, oldValue is ${JSON.stringify(oldValue)} value is ${JSON.stringify(newValue)}`)
            this.scrollHeight = newValue.height as number
          })
}
				.scrollable(ScrollDirection.Vertical)
        .scrollBar(BarState.Auto)
        .edgeEffect(EdgeEffect.Spring)
        .onSizeChange((oldValue: SizeOptions, newValue: SizeOptions) => {
          // console.info(`Ace1: on size change, oldValue is ${JSON.stringify(oldValue)} value is ${JSON.stringify(newValue)}`)
          this.screenHeight = newValue.height as number
        })
			 .onDidScroll((xOffset: number, yOffset: number) => { // 滚动事件回调，Scroll滚动时触发。
          // // 滚动过程中持续监听位置变化
          // console.log('yOffset',yOffset)
          // this.handleScrollPosition(yOffset);
        })
        .onScrollStop(() => {
          // 滚动停止时再次确认位置，确保状态准确
          const yOffset = this.scroller.currentOffset().yOffset;
          console.log('yOffset', yOffset)
          this.handleScrollPosition(yOffset);
        })


@Builder
buildCustomerSection() {
  this.comColumnId('customerSection')
}

@Builder
buildPackageSection() {
  this.comColumnId('packageSection')
}

@Builder
buildOrderSection() {
  this.comColumnId('orderSection')
}

```

```tsx
// 滚动定位
  private scrollToSection(index: number) {
    console.log('1', index)
    if (index >= 0 && index < this.sectionOffsets.length) {
      console.log('2', this.scroller.scrollTo, this.sectionOffsets[index])
      this.scroller.scrollTo({
        xOffset: 0,  // 必须显式声明x轴偏移量
        yOffset: this.sectionOffsets[index],
        animation: {  // 可选动画参数
          duration: 300,
          curve: curves.initCurve()
        }
      });
    }
  }

// 处理tab点击区域滚动联动
  private tabScrollArea(index: number) {
    this.scrollHeight = this.scrollHeight - this.ColumnHeight; // scroll高度复原
    let diff = this.scrollHeight - this.sectionOffsets[index]; // 高度差值计算

    if (this.screenHeight - diff > 0) {
      this.ColumnHeight = this.screenHeight - diff // Column4 的容器高度补充
    } else {
      this.ColumnHeight = 0
    }
    console.log('scrollHeight', this.scrollHeight, 'ColumnHeight', this.ColumnHeight, 'diff', diff)
    this.scrollToSection(index);
  }

  private updateSectionOffsets() {
    // 增加等待时间，确保布局加载完成
    setTimeout(() => {
      // 移除使用 this.$ 的部分，因为不存在该属性
      if (this.sectionOffsets.length === this.sectionIds.length &&
      this.sectionOffsets.every(offset => offset > 0)) {
        this.offsetsInitialized = true;
        console.log('sectionOffsets 初始化完成');
      } else {
        console.log('sectionOffsets 未完全初始化', this.sectionOffsets);
      }
    }, 1000);
  }
```







----



## 二、自动滚动到对应的高度

### 1、实现思路


```mermaid
graph TD
    A[开始: 锚点滚动联动系统] --> B[初始化阶段]
    
    subgraph B[初始化阶段]
        B1[定义状态变量]
        B2[创建Scroller实例]
        B3[定义锚点ID数组]
        B4[定义侧边栏选项]
    end
    
    B --> C[构建UI界面]
    
    subgraph C[UI构建阶段]
        C1[List组件<br/>主滚动区域]
        C2[SideBuilder<br/>侧边导航栏]
    end
    
    C1 --> D1[buildSection方法<br/>构建内容区块]
    D1 --> E1[comColumnId装饰器<br/>监控区块位置]
    E1 --> F1[onAreaChange回调<br/>记录区块偏移量]
    F1 --> G1[更新sectionOffsets数组]
    
    C1 --> D2['动态高度补充Column<br/>height=this.ColumnHeight']
    D2 --> E2[白色背景<br/>占位作用]
    
    C2 --> D3[SideOptionItem方法<br/>构建导航项]
    D3 --> E3[onClick事件<br/>导航项点击]
    E3 --> F2[调用tabScrollArea方法]
    
    F2 --> G[滚动联动处理]
    
    subgraph G[滚动联动逻辑]
        G1["计算高度差值diff<br/>scrollHeight - sectionOffsets[index]"]
        G2["智能判断<br/>if (screenHeight - diff > 0)"]
        G3["动态调整ColumnHeight<br/>this.ColumnHeight = screenHeight - diff"]
        G4["else<br/>this.ColumnHeight = 0"]
        G5[调用scrollToSection]
    end
    
    G5 --> H[执行滚动定位]
    
    subgraph H[滚动执行]
        H1["使用Scroller.scrollTo"]
        H2["设置yOffset=sectionOffsets[index]"]
        H3["动画效果duration:300"]
    end
    
    H --> I[完成: 精准定位]
    
    C1 --> J[onSizeChange监控<br/>更新scrollHeight]
    J --> G1
    
    %% 数据流连接
    G3 -.->|动态更新| D2
    G1 -.->|提供偏移量| G5
    E3 -.->|传递索引index| F2
    
    %% 样式定义
    classDef init fill:#e1f5fe,stroke:#01579b,stroke-width:2px
    classDef ui fill:#f3e5f5,stroke:#4a148c,stroke-width:2px
    classDef dynamic fill:#fff3e0,stroke:#ef6c00,stroke-width:2px
    classDef logic fill:#e8f5e8,stroke:#1b5e20,stroke-width:2px
    classDef event fill:#fce4ec,stroke:#ad1457,stroke-width:2px
    
    class B1,B2,B3,B4 init
    class C1,C2,D1,D3 ui
    class D2,E2 dynamic
    class G1,G2,G3,G4,G5 logic
    class E3,F1,F2,J event
```

### 2、代码实现

#### 1) ` VehicleInfoPage.ets`

查询-车型数据 `src/main/ets/pages/QueryPage/VehicleInfoPage/VehicleInfoPage.ets`

![image-20251205161031542](./assets/image-20251205161031542.png)

```tsx
// 定义状态变量、创建Scroller实例、定义锚点ID数组、定义侧边栏选项

@State scrollHeight: number = 0
@State screenHeight: number = 0
private scroller: Scroller = new Scroller();

// 区块偏移量存储（单位：vp）
@State private sectionOffsets: number[] = [];
// 内容区域锚点标识（与导航项顺序对应）
private readonly sectionIds: string[] = ['Section1', 'Section2']
private sideOptions = [
    { name: '基本信息', key: 'basic' },
    { name: '车身信息', key: 'body' }
] as FinanceParams[];

```

```tsx
// UI构建阶段：SideBuilder侧边导航栏、List组件<br/>主滚动区域
List({ scroller: this.scroller }) {
  ListItem() {
    Column() {
      this.buildSection({
        comColumnId: 'Section1',
      })
      this.buildSection({
  			comColumnId: 'Section2',
			})
      // 关键优化部分-补充高度：滚动到最下面位子不够的时候，可以撑起来
     	Column().width('100%').height(this.ColumnHeight).backgroundColor(Color.White)
    }
    .onSizeChange((oldValue: SizeOptions, newValue: SizeOptions) => {
 			this.scrollHeight = newValue.height as number
     })
  }
}


@Builder
BuilderSide() {
  Column() {
   ForEach(this.sideOptions, (option: FinanceParams, index: number) => {
      this.SideOptionItem(option.name, index)
   })         
  }.margin({ top: 20 })
}


@Builder
  SideOptionItem(optionName: string, index: number) {
    Row() {
      Text(optionName)
        .fontSize(12)
        .fontColor(this.chooseSideName === optionName ? Color.Black : '#777777')

      Image(this.chooseSideName === optionName ?
      $r('app.media.btn_simple_danxuan_y_2x') :
      $r('app.media.btn_simple_danxuan_n_2x')
      )
        .width(14)
        .height('auto')
        .margin({ right: 5 })
    }
    .width('100%')
    .justifyContent(FlexAlign.SpaceBetween)
    .margin({ bottom: 20 })
    .onClick(() => {
      this.chooseSideName = optionName;
      this.tabScrollArea(index)
    })
}
```



```tsx
// 实现思路

@Builder
buildSection(sectionObj: FinanceParams) {
   this.comColumnId(sectionObj.comColumnId)
   Column() {
     // 自定义内容
   }
  .id(sectionObj.comColumnId) // 绑定锚点ID
}

// 封装scroll的获取高度方法
@Builder
comColumnId(id: string) {
  Column(){}
   .id(id)
   .onAreaChange((oldArea: Area, newArea: Area) => {
        if (newArea?.globalPosition) {
          const index = this.sectionIds.indexOf(id);
          if (index !== -1) {
            this.sectionOffsets[index] = Number(newArea.position.y);
            // console.log(`${id}偏移量更新:`, this.sectionOffsets[index]);
          }
        }
    })
}
```

```tsx
/**
 * 调用处理
 */

  //  处理tab点击区域的联动
  private tabScrollArea(index: number) {
    console.log('1', index)
    this.scrollHeight = this.scrollHeight - this.ColumnHeight; // scroll高度复原
    let diff = this.scrollHeight - this.sectionOffsets[index]; // 高度差值计算

    if (this.screenHeight - diff > 0) {
      this.ColumnHeight = this.screenHeight - diff // Column4 的容器高度补充
    } else {
      this.ColumnHeight = 0
    }
    this.scrollToSection(index);
  }

  // 滚动定位
  private scrollToSection(index: number) {
    // console.log('1',index)
    if (index >= 0 && index < this.sectionOffsets.length) {
      // console.log('2',index)
      this.scroller.scrollTo({
        xOffset: 0, // 必须显式声明x轴偏移量
        yOffset: this.sectionOffsets[index],
        animation: {
          // 可选动画参数
          duration: 300,
          curve: curves.initCurve()
        }
      });
    }
  }


```

