根据 HarmonyOS 官方文档 [状态管理最佳实践](https://developer.harmonyos.com/cn/docs/documentation/doc-guides/arkts-state-management-0000001724480726)：

1. 父子组件通信优先使用 ‌**回调函数 + @Link**‌

2. 跨层级组件使用 `@Provide/@Consume`

3. 复杂场景使用 ‌**Observable 模式**‌

   

# @Link双向同步

![image-20250318214444975](./assets/image-20250318214444975.png)

![image-20250318215431604](./assets/image-20250318215431604.png)



# @Provide & @Consume

![image-20250318214802493](./assets/image-20250318214802493.png)

![image-20250318215216186](./assets/image-20250318215216186.png)



# @Observed & @ObjectLink



![image-20250318215529962](./assets/image-20250318215529962.png)

![image-20250318215931225](./assets/image-20250318215931225.png)

![image-20250318215658116](./assets/image-20250318215658116.png)

![image-20250318215710903](./assets/image-20250318215710903.png)

> - 父组件
>
> ![image-20250318215856795](./assets/image-20250318215856795.png)

> - 重新构造类
>   - ![image-20250318220116208](./assets/image-20250318220116208.png)
>   - ![image-20250318220221222](./assets/image-20250318220221222.png)



## 问题：多个位子都要用，如何共同响应

> ![image-20250318220835192](./assets/image-20250318220835192.png)
>
> 怎么办？
>
> ![image-20250318220900792](./assets/image-20250318220900792.png)
> ![image-20250318221051495](./assets/image-20250318221051495.png)
> ![image-20250318221114431](./assets/image-20250318221114431.png)



## 代码优化：

> ![image-20250318221239354](./assets/image-20250318221239354.png)
>
> - ![image-20250318221333820](./assets/image-20250318221333820.png)
> - ![image-20250318221432226](./assets/image-20250318221432226.png)