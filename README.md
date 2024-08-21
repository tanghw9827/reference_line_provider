## 1. 项目概述
项目将分为三部分工作进行展开，分别是参考线搜索相较于原生Apollo新特性、无图版本下参考线适配工作以及接入上游nn_path开发工作。
## 2. 功能开发新特性
### 2.1 Apollo潜在问题
原生Apollo的参考线主要是在HDmap基础上截取车前、车后一定距离的车道中心线，平滑后给下游横纵向规划提供局部规划的参考以及障碍物投影的容器。

**Apollo 在参考线方面的不足:**

- 在路口处提供参考线时，需要依赖路口的HD Map
- 没有静态障碍物避障及特定场景牵引功能（如匝道、变道场景的规划提供更类人的参考线）

**因此，功能开发主要在三方面：**

- 基于Hybrid_A_star搜索得到参考线实现不依赖HD地图的、带牵引功能的参考线
- 搜索过程绕行curb障碍物和静态障碍物
- 减少搜索带来的耗时, 启用异步任务生成参考线
### 2.2 效果展示
| 参考线搜索绕行静态障碍物 | [1.mp4](./case/1.mp4) |
| :-- | :-- |
| 参考线搜索绕行curb障碍物 | [2.mp4](./case/2.mp4) |
| 参考线搜索上匝道 | [3.mp4](./case/3.mp4) |
| 参考线搜索过无变道通过路口 | [4.mp4](./case/4.mp4)  |
| 参考线搜索通过复杂路口 | [5.mp4](./case/5.mp4) |
| 参考线搜索耗时优化 | ![image.png](https://cdn.nlark.com/yuque/0/2024/png/27299753/1724171595005-b93d9f12-94d7-4180-9dfd-d99d0c40b526.png#averageHue=%23fdfdfb&clientId=u6aebbfc4-5106-4&from=paste&height=155&id=u0792bcc4&originHeight=170&originWidth=505&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=33993&status=done&style=none&taskId=u582da744-1fcf-4339-a1ed-9ce074e323c&title=&width=459.09089914038185) |

## 3. 无图版本适配
### 3.1 目标

- 参考线模块在没有hdmap的情况下，在全场景生效。
- 适配目标：更新上游接口、适配无图数据、求解异常case
### 3.2 输入输出

- Depend on: MapService & pnc_map
- Provide: Routesegment & Reference_line

### 3.3 适配

- 上游接口更新
- 无图数据适配
  
### 3.4 异常case求解

- 路口无法绕行curb
- curb过多需要筛选
- 相邻车道长度不一致
- 获取路口边界异常

## 4. 接入上游nn_path
### 4.1 目标

- 无图+决策NN驱动下的参考线生成
- 适配目标：更新上游接口、适配决策NN数据、求解异常case
### 4.2 输入输出

- Depend on: MapService & Passages
- Provide: Reference_line
### 4.3 整体实现 

- 整体流程：确定搜索长度->确定搜索起点->确定搜索终点->确定搜索初始解->Hybrid A star搜索->平滑优化结果
### 4.4 异常case求解

- 参考线通过性
- 参考线压实线外切
- 参考线一致性
- 参考线不居中
