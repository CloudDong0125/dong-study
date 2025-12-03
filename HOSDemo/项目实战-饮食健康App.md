# 	原型分析

![image-20250324235332037](./assets/image-20250324235332037.png)

![image-20250324235428707](./assets/image-20250324235428707.png)

![image-20250324235627532](./assets/image-20250324235627532.png)

![image-20250324235754324](./assets/image-20250324235754324.png)

---



# 新建与初始化项目

![image-20250325223037668](./assets/image-20250325223037668.png)



---



# 欢迎页面UI实现

> https://www.bilibili.com/video/BV1Sa4y1Z7B1?spm_id_from=333.788.player.switch&vd_source=45b1ea5d80a271b24029deadc0c69379&p=36

> - 分析
>
>   ![image-20250325223003745](./assets/image-20250325223003745.png)



---



> ## 1、基本页面实现
>
> <img src="./assets/image-20250325223453391.png" alt="image-20250325223453391" style="zoom:50%;" />
>
> ![image-20250325223632291](./assets/image-20250325223632291.png)
>
> <img src="./assets/image-20250325223811726.png" alt="image-20250325223811726" style="zoom:50%;" />

## `@Extend`样式抽取

> ![image-20250325224010466](./assets/image-20250325224010466.png)

​	

# 欢迎页面业务



> ![image-20250325224244441](./assets/image-20250325224244441.png)
>
> #### 功能点
>
> - ###### 功能点1、首选项	
>
> - ###### 功能点2、自定义弹窗
>
>   ```bash
>   entry/src/main/ets/
>   └── view/
>   		└── common/
>       			└── constants/ # 常量文件夹
>       						└── CommonConstants.ts
>       			└── utils / # 工具类文件夹
>       						└── PreferenceUtil.ts  # 功能点1、首选项			
>       └── page/ # 页面目录
>       			└── welcomePage.ets # 欢迎页面 
>       └── view/ # 组件目录
>       		  └── welcome/ # 欢迎页面相关组件
>       		  				└── UserPrivacyDialog.ets # 功能点2、自定义弹窗
>   ```



## `@CustomDialog`自定义弹窗

> ![image-20250325224554917](./assets/image-20250325224554917.png)

> `UserPrivacyDialog.ets`
>
> ### UI与事件
>
> ![image-20250325225021791](./assets/image-20250325225021791.png)
>
> ![image-20250325225244874](./assets/image-20250325225244874.png)



> ### 导出与使用
>
> ![image-20250325225316162](./assets/image-20250325225316162.png)
>
> ##### 1、初始化使用
>
> - ![image-20250325225602461](./assets/image-20250325225602461.png)
>
> - ![image-20250325225710619](./assets/image-20250325225710619.png)
>
> 



## 数据持久化-用户首选项

> ##### 作用：APP重启的时候数据不丢失
>
> ###### 场景
>
> - ![image-20250327102127991](./assets/image-20250327102127991.png)
>
> ###### 使用方式
>
> - ![image-20250327102432844](./assets/image-20250327102432844.png)



#### 步骤：封装定义 

> ```bash
> entry/src/main/ets/
> └── entryability/ 
> 		└── EntryAbility.ets  # 在此实现启动就调用
> └── view/
> 		└── common/
>     			└── utils / # 工具类文件夹
>     						└── PreferenceUtil.ts  # 用户首选项			
> ```
>
> ```bash
> 初始化定义与导出--加载-操作
> ```

#### 步骤1：初始化处理

> ![image-20250327102854014](./assets/image-20250327102854014.png)
>
> 



#### 步骤2：第一个方法加载（异步的Promise写法）

> ##### 回调多且较为的繁琐
>
> - ###### 失败的时候处理
>
>   - ###### ![image-20250327103046345](./assets/image-20250327103046345.png)
>
> - ##### 成功的时候处理
>
>   - ![image-20250327103243647](./assets/image-20250327103243647.png)



#### 步骤2：第一个方法加载（同步写法async/await）`推荐`

> - ![image-20250327103529670](./assets/image-20250327103529670.png)
> - ![image-20250327103720842](./assets/image-20250327103720842.png)

#### 完整截图（取自案例：2弹出窗）

> ![image-20250327110539149](./assets/image-20250327110539149.png)
>
> ![image-20250327110619850](./assets/image-20250327110619850.png)
>
> ![image-20250327110931154](./assets/image-20250327110931154.png)

#### 步骤3：操作数据-存

> ![image-20250327104108991](./assets/image-20250327104108991.png)

#### 步骤4：数据操作-读

> ![image-20250327104324114](./assets/image-20250327104324114.png)

#### 步骤5：使用-需要项目启动的时候开始调用

> ##### ![image-20250327104955074](./assets/image-20250327104955074.png)



#### 步骤使用1：使用-页面加载就读操作（案例：1字体的设置、2弹出窗）

> - #### 案例：字体的大小配置
>
>   - ![image-20250327105417244](./assets/image-20250327105417244.png)
>
> 
>- #### 案例：弹出窗的配置
> 
>![image-20250325230614467](./assets/image-20250325230614467.png)
> 
>![image-20250325230639488](./assets/image-20250325230639488.png)
> 
>

#### 步骤使用2：使用-页面写操作（案例：1字体的设置、2弹出窗）

> - #### 案例：字体的大小配置
>
> - ###### ![image-20250327105546924](./assets/image-20250327105546924.png)
>
> 
>
> - ###### 案例：弹出窗的配置
>
>   - ###### ![image-20250325230822073](./assets/image-20250325230822073.png)
>
> 

#### 测试：模拟器测试

> ![image-20250325231006934](./assets/image-20250325231006934.png)
>
> ![image-20250327105850538](./assets/image-20250327105850538.png)![image-20250327105913782](./assets/image-20250327105913782.png)

## 使用`getContext`退出app

![image-20250327110239198](./assets/image-20250327110239198.png)



# 首页Tabs

> #### 首页UI设计
>
> ![image-20250325233108944](./assets/image-20250325233108944.png)

## `@Builder`自定义tabBar

> ##### 自定义样式
>
> - ![image-20250325233225246](./assets/image-20250325233225246.png)
>
> ##### 自定义事件
>
> - ![image-20250325233312188](./assets/image-20250325233312188.png)



> ![image-20250325233601766](./assets/image-20250325233601766.png)
>
> ![image-20250325233523253](./assets/image-20250325233523253.png)
>
> ![image-20250325233618753](./assets/image-20250325233618753.png)



# ------1饮食记录

```bash
entry/src/main/ets/
└── common/
    			└── constants/ # 常量文件夹
    						└── CommonConstants.ts	
    			└── utils / # 工具类文件夹
    						└── PreferenceUtil.ts 
    						└── DateUtil.ts  # 日期转换工具包
└── view/
    └── page/ # 页面目录
    			└── Index.ets # 首页 
    └── view/ # 组件目录
    		  └── record/ # 饮食记录文件
    		  				└── RecordIndex.ets  # 饮食记录
    		  				
                    └── SearchHeadr.ets  # 顶部搜索栏组件
                    └── StatsCard.ets  # 统计卡片
                    └── DatePickDialog.ets  # 日期弹窗
                    └── CalorieState.ets # 热量统计
                    └── NutrientStats.ets # 营养素统计
                    
                    └── RecordList.ets # 饮食记录列表
```

# 饮食记录UI设计

> ![image-20250325233856963](./assets/image-20250325233856963.png)







# 饮食记录-顶部搜索栏

> #### `Badeg`
>
> - ![image-20250325234359661](./assets/image-20250325234359661.png)

# 饮食记录-统计卡片



> ![image-20250325234510184](./assets/image-20250325234510184.png)

## `@CustomDialog`自定义弹窗--日期选择

> ##### 样式
>
> - ![image-20250325234929006](./assets/image-20250325234929006.png)



## `AppStorage.SetOrCreate()` 保存全局

> ![image-20250325235415487](./assets/image-20250325235415487.png)

## `@StorageProp()` 读取单向绑定

> 全局存储后读取再回现
>
> ![image-20250326000034928](./assets/image-20250326000034928.png)



## `组件Swiper滑块视图容器`

> ![image-20250326000615016](./assets/image-20250326000615016.png)
>
> ![image-20250326000608118](./assets/image-20250326000608118.png)

## `@Builder` 重复代码抽取

> ![image-20250326000848617](./assets/image-20250326000848617.png)

## `Progress` 进度条圆环

> ![image-20250326001006880](./assets/image-20250326001006880.png)



> ##### 同理得到
>
> - ![image-20250326001500350](./assets/image-20250326001500350.png)
> - ![image-20250326001655240](./assets/image-20250326001655240.png)
> - ![image-20250326001558720](./assets/image-20250326001558720.png)
> - ![image-20250326001357067](./assets/image-20250326001357067.png)
>
> 





# 饮食记录-记录列表

## `@Extend(Text)`抽取文本样式

> ![image-20250326001952898](./assets/image-20250326001952898.png)

## `swipeAction` 侧滑按钮

> ![image-20250326002248023](./assets/image-20250326002248023.png)



# ------2食物列表页

```bash
entry/src/main/ets/
└── common/
    			└── constants/ 
    			└── utils / 
    						└── PreferenceUtil.ts 
    						└── DateUtil.ts  
└── view/
    └── page/ # 页面目录
    			└── Index.ets 
    			└── ItemIndex.ets # 公用的页面
    └── view/ # 组件目录
    		  └── ItemList # 内容列表
```



# 食物列表页UI设计

> ![image-20250326002438089](./assets/image-20250326002438089.png)





# 食物列表页

# 食物列表-底部Panel

# 食物列表-数字键盘

# 数据模型-记录项

```bash
model # 接口
	ItemCategoryModel.ets
	ItemModel.ets

viewmodel  # 数据模型
	ItemCategory.ets
	RecordItem.ets	
```



- ###### 分析数据模型

![image-20250327215543254](./assets/image-20250327215543254.png)

# 数据模型-饮食记录

# 数据模型-通用DB工具

# 饮食记录-业务层开发

# 实现数据持久化和页面交互



![image-20250327111514585](./assets/image-20250327111514585.png)



![image-20250327111741293](./assets/image-20250327111741293.png)





## 如何让`@Builder`重新触发渲染--较为繁琐，建议使用`@Component`

> 可以正常的传到参数，但是视图没有渲染到，如何解决？

![image-20250327112445983](./assets/image-20250327112445983.png)

![image-20250327112515565](./assets/image-20250327112515565.png)

![image-20250327112614035](./assets/image-20250327112614035.png)

























