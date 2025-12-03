```tsx
// src/main/ets/pages/DocumentCenterPage/DocumentCenterPage.ets
import DocAbandonList from './DocAbandonList'
@Entry
@Component
struct CustomerManagementPage {
  @State labels: Array<string> = ['结算', '办卡']
  @State currentActive: number = 0
   build() {
      Column(){
        DocumentCenterMenuBtn({
           label: this.labels,
           isActive: this.currentActive,
           onTabClick:this.handTabClick
        })
        DocAbandonList({
          isActive:this.currentActive,
        })
      }
   }
}

@Component
struct TabItem {
  // 1. 类型化数组声明（必须指定元素类型）
  @Prop label: Array<string> = ['结算', '办卡']
  @Prop isActive: number = 0
  onTabClick=(isActive:number) => {}


  build() {
    Row() {
      // 3. 使用ForEach渲染数组元素
      ForEach(this.label, (item: string, index: number) => {
        Text(item) // 每个数组元素生成独立Text组件
          .onClick(() => {
            this.isActive  = index
            // 触发自定义事件
            console.debug('[子组件] 触发点击，索引：', index)
            animateTo({ duration: 100 }, () => {
              this.onTabClick(this.isActive)
            })
          })
      }, (item: string) => item) // 关键：为每个元素指定唯一标识
    }
}
```

```tsx
// src/main/ets/pages/DocumentCenterPage/DocAbandonList.ets
/*
 * 作废
 */
import Util from '../../common/util/Index'
import DocumentCenterModel from '../../model/DocumentCenterModel'
import { SaleBillFooterModel,SaleBillModel,CardTitlesProps,CardDataProps,CardDetailProps,ItemMapProps,RowDataModel } from '../../model/DocumentCenterModel'

import CardTitles from './components/CardTitles';
import CardData from './components/CardData'
import CardDetails from './components/CardDetails';

@Component
export default struct DocAbandonList {
  @Prop isActive:number = 0;
  @State private currentPage:number = 1;
  @State private totalPages:number = 1;
  @State RowsData:RowDataModel[] = []
  // 添加重置方法
  resetData() {
    this.currentPage = 1
    this.totalPages = 1
    this.RowsData = []
    // this.RequestSaleBillList(1)
  }
  aboutToAppear(): void {
    this.RequestSaleBillModelList(this.currentPage);
  }
  // 作废
  private async RequestSaleBillModelList(page: number) {
    const billTimeLower = Util.DateUtils.getOneMonthAgoDate();
    const billTimeUpper = Util.DateUtils.getCurrentDate();
    const res = await DocumentCenterModel.saleBillModelList({
        billTimeLower: billTimeLower,
        billTimeUpper: billTimeUpper,
        page: page,
        rows: 10,
        status: "Checked",
        showCount: 1,
        items: true,
        canceled: true,
        checkCarStatus: 1,
        showEvaluate:1,
        addfilterStr:"otherPayWay.name",
        followUserId: "",
        debetStatus: "",
        workerId:"",
        salerId: ""
    })

    try{
      if (res && res.data) {
        this.totalPages = res.data.page || 1;


        // 处理每一行数据
        const curRowsData: SaleBillModel[] = res.data.rows || [];
        console.log(JSON.stringify(curRowsData.length))
        const processedData = curRowsData.map((row: SaleBillModel) => {

          const code = row.code;

          const customer = row.customer;
          const clientUser = row.clientUser;
          const shop = row.shop;

          // CardTitles 组件内容
          const cardTitles: CardTitlesProps = {
            propsStatue :'Checked',
            customerName: customer.carNumber,
            debtStatus: row.debet === 0 ? 'pic_erp_wqk' : (row.debet > 0 && row.debet - row.debetPaid > 0.001 ? 'pic_erp_wjq' : 'pic_erp_yjq'),
          };

          // cardData 组件内容
          const cardData: CardDataProps = {
            propsStatue :'Checked',
            status: row.debet === 0 ? '0' : (row.debet > 0 && row.debet - row.debetPaid > 0.001 ? '1' : '2'),
            giftMoney: row.giftMoney,
            paidMoney: row.paidMoney,
            pendingPayment: customer.debet - customer.paidDebet,
            biller: clientUser.name,
            shopName: shop.name,
            debetPaid:row.debetPaid

          };


          // cardDetails 组件内容
          const cardDetails: CardDetailProps[] = row.items.map((item: ItemMapProps): CardDetailProps => {
            return {
              id: item.id,
              goodsName: item.goods.name,
              amount: item.amount,
              price: item.price,
              money: item.money
            };
          });

          const rowData: RowDataModel = {
            code,
            cardTitles,
            cardData,
            cardDetails
          };

          return rowData;
        });
        this.RowsData = this.RowsData.concat(processedData);

      }
    }catch (e) {

    }
  }
  build() {
    Column() {
      List() {
        DocCard3({ RowsData: this.RowsData })
      }
      .width('100%')
      .layoutWeight(1)
      .backgroundColor("#f3f3f3")
      .padding({ top: 10 })
      .onReachEnd(() => {
        console.log(`当前页面：${this.currentPage} / 共 ${this.totalPages} 页`)
        if (this.currentPage < this.totalPages) {
          this.currentPage++;
          this.RequestSaleBillModelList(this.currentPage);
        }
      });
    }
    .height('100%')
    .alignItems(HorizontalAlign.Start)
    .justifyContent(FlexAlign.Start)

  }
}


```



