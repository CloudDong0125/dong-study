```tsx
export interface DatalistModel {
  id?:string,
  vin?:string|null,
  cardLeftMoney:number,
  cardGiftLeftMoney:number,
  leftWalletMoney:number
}

@Entry
@Component
struct Customer {
  @Provide('customerData') @State datalistData: DatalistModel = new DatalistModel(); 
    
  private safeUpdateData(params: DatalistModel) {
      if (params) { // 类型校验
        this.datalistData.cardLeftMoney = params.cardLeftMoney ;
        this.datalistData.vin = params.vin

        if (params.id) {
          this.customerId = params.id
        }
      }
  }
  
  build() {
     // 客户资料
     CustomerMain({ sectionIds :this.sectionIds[0] })         
  }
}

@Component
export default struct CustomerDetail { // 客户资料
  @Prop sectionIds:string = '';
  build() {
    // 客户资料区块
    Column() {
      detailCard3()
    }
    .id(this.sectionIds) // 绑定锚点ID
  }
}

// 客户资料card3组件
@Component
struct detailCard3 {
  @Consume('datalistData') datalistData: DatalistModel; 
}
```







```tsx
@Component
export default struct CustomerRecentBills{ // 近期单据
  @Prop sectionIds:string=''
  @State tabsItems: string[] = ['结算', '报价', '办卡','作废'];
  @State currentTabIndex: number = 0;
  // @State RowsData:RowDataModel[] = []
  @Provide('RowsData')  RowsData: RowDataModel = new RowDataModel();



  aboutToAppear(): void {
    this.RequestSaleBillList()
  }

  // 结算/报价接口
  private async RequestSaleBillList() {
    const res = await DocumentCenterModelAPI.saleBillList({
      status: "Price",
      canceled: false,
      page: 1,
      rows: 10,
      items: true,
      showCount: 1
    })
    try{
      if (res && res.data) {
        const curRowsData:SaleBillModel[] = res.data.rows || [];
        const processedData = curRowsData.map((row:SaleBillModel) => {
          const code = row.code;
          const customer = row.customer;
          const clientUser = row.clientUser;
          const shop = row.shop;

          // CardTitles 组件内容
          const cardTitles: CardTitlesProps = {
            propsStatue :'Price',
            customerName: customer.carNumber,
            debtStatus: row.debet === 0 ? 'pic_erp_wqk' : (row.debet > 0 && row.debet - row.debetPaid > 0.001 ? 'pic_erp_wjq' : 'pic_erp_yjq'),
            // 操作
            code:clientUser.name,
            clientUserName: clientUser.name,


          };

          const rowData: RowDataModel = {
            code,
            cardTitles,
          };

          return rowData;
        })
        this.RowsData = this.RowsData.concat(processedData);
        console.log(JSON.stringify(this.RowsData))
      }
    } catch (e) {
      console.error('请求销售账单列表时出错:', e);
    }

  }

  build() {
    Column() {
      billsNavBar()
      // 导航选项卡
      ComTabs({
        tabsItems: this.tabsItems,
        currentIndex: $currentTabIndex,
        onTabClick: (index: number) => {
          this.currentTabIndex = index;
          console.log('currentTabIndex'+this.currentTabIndex)
        }
      })
      billsCardShow({
        currentTabIndex:this.currentTabIndex
      })


    }
    .width('100%')
    .backgroundColor(Color.White)
    .borderRadius(10)
    .margin({top:10})
    .padding(15)
    .id(this.sectionIds) // 绑定锚点ID

  }
}
```

```tsx

@Component
struct billsCardShow {
  @Prop currentTabIndex:number = 0

  build() {
    Stack() {
      if (this.currentTabIndex == 0) {
        billsCard()
      } else if (this.currentTabIndex == 1) {
        billsCard()
      } else if (this.currentTabIndex == 2) {
        billsCard2()
      } else if (this.currentTabIndex == 3) {
        billsCard3()
      }
    }
    .transition({ type: TransitionType.All, opacity: 0.8 })
    .animation({ duration: 500, curve: Curve.EaseInOut })
  }
}

@Component
struct billsCard {
  @Consume('RowsData') RowsData: RowDataModel[];
  build() {
    Column() {
      if (this.memberCardList.length!=0) {
        ForEach(this.memberCardList,
            (item:typeMemberCardList,index:number) => {
        })
      }
    }
  }
}
```

