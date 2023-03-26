# 从 Hyperledger Composer 迁移到 Convector 框架 Marbles 示例

> 原文：<https://medium.com/hackernoon/migrating-from-hyperledger-composer-to-convector-framework-marbles-example-13a24a74faad>

![](img/3708c3db5b5424d9605d44a0f2ac998d.png)

几周前，IBM [区块链](https://hackernoon.com/tagged/blockchain)团队的首席工程师西蒙·斯通宣布 [IBM 将大幅减少其在 Hyperledger Composer 项目](/worldsibu/hyperledger-composer-has-been-put-on-pause-what-are-your-options-now-41cc63026161)中的工作，这让许多拥有现有应用程序的开发者[处于一种不明朗的局面，不知道下一步该做什么](/worldsibu/hyperledger-composer-has-been-put-on-pause-what-are-your-options-now-41cc63026161)。

好消息是，在 [WorldSibu](https://worldsibu.io) 我们一直致力于一个智能合约框架，该框架**在 Hyperledger Fabric** 中本地运行，因此您不必考虑管理与 Fabric 直接通信的复杂性，但仍然可以在其上运行本地节点 JS 链码。此外，在此基础上，我们正在构建几个工具，以使开发人员的生活更加轻松，比如一个开箱即用的开发环境，就像您过去在 [Composer](https://hackernoon.com/tagged/composer) 中工作一样。

> 现在 Convector 已经支持 Hyperledger Fabric，我们计划很快增加对多个区块链技术的支持，敬请关注！

在这个例子中，我将向您展示如何轻松地从 Composer 迁移到 [**Convector**](https://worldsibu.io/convector) 。这不需要太多的努力就可以做到，因为我们有非常相似的概念。

两者的第一个区别是语言。Composer 决定使用其定制设计的语言来定义智能合约的不同部分。另一方面，Convector 是使用 [Typescript](http://www.typescriptlang.org/) 编写的，这是一种基于 Javascript 的超级 set 语言，由于其丰富的特性，如可选的强类型和抽象语法树(AST)生成来创建围绕代码的工具，它已经获得了广泛的关注。

在 Composer 中，业务网络模型基本上由三个组件定义:

*   **资产**:房屋和房源
*   **参与者:**买家和房主
*   **交易:**买卖房屋，创建和关闭列表

当我们设计 Convector 时，我们在这里停下来想:资产和参与者基本上是由不同属性复合而成的模型，甚至，参与者有时可以充当执行某个动作的人，或者该动作被执行的人！

我们想让事情尽可能简单，并让开发人员能够用一个更加软件开发友好的术语来表达他们的想法，因此我们决定在框架内创建两个基本概念:

*   **模型:**对现实世界中*某物*的引用，有形或无形，描述其属性。它也可以是网络的参与者。
*   控制器:一组关于你如何与模型互动的规则。

基于这两个基本概念，您可以构建链代码所需的一切，同时又有足够的灵活性以您需要的方式表达您的逻辑。

下面这段代码定义了 Composer 中的 Marbles 示例。

```
namespace org.hyperledger_composer.marblesenum MarbleColor {
  o RED
  o GREEN
  o BLUE
  o PURPLE
  o ORANGE
}enum MarbleSize {
  o SMALL
  o MEDIUM
  o LARGE
}asset Marble identified by marbleId {
  o String marbleId
  o MarbleSize size
  o MarbleColor color
  --> Player owner
}participant Player identified by email {
  o String email
  o String firstName
  o String lastName
}
```

在 Convector 中，您可以这样写:

```
export enum MarbleColor {
  RED,
  GREEN,
  BLUE,
  PURPLE,
  ORANGE
}export enum MarbleSize {
  SMALL,
  MEDIUM,
  LARGE
}export class Marble extends ConvectorModel<Marble> {
  @ReadOnly()
  @Required()
  public type = 'io.worldsibu.marbles.marble'; @Validate(yup.number())
  @Default(MarbleSize.MEDIUM)
  public size: MarbleSize; @Validate(yup.number())
  public color: MarbleColor; @Validate(yup.string().email())
  public owner: string;
}export class Player extends ConvectorModel<Player> {
  @Required()
  @ReadOnly()
  public type = 'io.worldsibu.marbles.player'; @Validate(yup.string().email())
  public id: string; @Validate(yup.string())
  public firstName: string; @Validate(yup.string())
  public lastName: string;
}
```

因为 Convector 运行在 Javascript 上，所以您可以使用表达式进行验证，并以一种更富于表现力的方式限制模型包含的数据。我们已经创建了一组有用的装饰器来帮助您实现这一点，并为您节省一些代码，同时保持模型的可读性(并降低风险！).一个模型必须有两个属性，一个是这个模型类的唯一标识符 **type** ，另一个是每个模型实例的唯一标识符 **id** 。如果您不指定 id 字段，Convector 将为您声明一个字符串字段。

## 链码逻辑

在 Composer 中，链码逻辑(也称为定义数据用途的规则)位于 3 个不同的层:ACL、事务模型和事务功能。

首先，从定义 ACL(访问控制列表)中的访问权限开始:

```
rule Default {
  description: "Allow all participants access to all resources"
  participant: "org.hyperledger_composer.marbles.player"
  operation: ALL
  resource: "org.hyperledger_composer.marbles.*"
  action: ALLOW
}
```

然后定义事务模型:

```
transaction TradeMarble {
  --> Marble marble
  --> Player newOwner
}
```

最后，定义事务函数:

```
async function tradeMarble(tradeMarble) {
  tradeMarble.marble.owner = tradeMarble.newOwner; const assetRegistry = await getAssetRegistry('org.hyperledger_composer.marbles.Marble'); await assetRegistry.update(tradeMarble.marble);
}
```

在 **Convector** 中，我们使用**控制器**简化了所有这些逻辑

```
@Controller('marble')
export class MarbleController extends ConvectorController {

  @Invokable()
  public async create(@Param(Marble) marble: Marble) {
    await marble.save();
  } @Invokable()
  public async trade(
    @Param(yup.string()) marbleId: string,
    @Param(yup.string().email()) newOwner: string
  ): Promise<void> {
    // use this.sender for authorization checks const marble = await Marble.getOne(marbleId); marble.owner = newOwner; await marble.save();
  }
}
```

您可以使用`@Invokable`和`@Param`装饰器来定义事务模型。任何可调用的方法都将作为事务公开，参数描述其签名。您不仅限于原语，您还可以传递**模型**，Convector 将为您解析所有复杂对象。

您可以使用 **this.sender** 来定义访问权限，这是发送者的指纹，对每个参与者都是唯一的。你可以用它做任何事情，如果你愿意，甚至可以将多个发送者连接到一个参与者。这是它提供的灵活性，因此您可以定义自己的规则。

最后，您为事务函数编写所有必要的额外逻辑。您可以在这里进行几乎所有类型的操作，只要它们是确定性的，并且除了分类帐数据之外不使用任何外部数据。为了查询总账，每个模型都有一些有用的 CRUD 方法，如`getOne`或`getAll`、`save`、`update`和`delete`。还有一些其他的，比如`history`，它检索该模型的更新历史，`clone`复制一个资产，或者`assign`为模型批量分配属性。

## 客户端访问

如果您想在 Composer 中测试上述方法，您通常会这样做:

```
const namespace = 'org.hyperledger_composer.marbles';
const factory = businessNetworkConnection.getBusinessNetwork().getFactory();const dan = factory.newResource(namespace, 'Player', 'daniel.selman@example.com');
dan.firstName = 'Dan';
dan.lastName = 'Selman';const simon = factory.newResource(namespace, 'Player', 'sstone1@example.com');
simon.firstName = 'Simon';
simon.lastName = 'Stone';const playerRegistry = await businessNetworkConnection.getParticipantRegistry(namespace + '.Player');
await playerRegistry.addAll([dan, simon]);const marble = factory.newResource(namespace, 'Marble', 'MARBLE_001');
marble.size = 'SMALL';
marble.color = 'RED';
marble.owner = factory.newRelationship(namespace, 'Player', dan.$identifier);const marbleRegistry = await businessNetworkConnection.getAssetRegistry(namespace + '.Marble');
await marbleRegistry.add(marble);const tradeMarble = factory.newTransaction(namespace, 'TradeMarble');
tradeMarble.newOwner = factory.newRelationship(namespace, 'Player', simon.$identifier);
tradeMarble.marble = factory.newRelationship(namespace, 'Marble', marble.$identifier);
await businessNetworkConnection.submitTransaction(tradeMarble);
```

在 Convector 中，我们有适配器能够在客户端应用程序中使用相同的控制器和型号。

```
const adapter = new FabricControllerAdapter(configuration);
const playerCtrl = new PlayerControllerClient(adapter);
const marbleCtrl = new MarbleControllerClient(adapter);const dan = new Player({
  id: 'daniel.selman@example.com',
  firstName: 'Dan',
  lastName: 'Selman'
});
await playerCtrl.register(dan);const simon = new Player({
  id: 'sstone1@example.com',
  firstName: 'Simon',
  lastName: 'Stone'
});
await playerCtrl.register(simon);const marble = new Marble({
  id: '1',
  size: MarbleSize.SMALL,
  color: MarbleColor.RED,
  owner: dan.id
});
await marbleCtrl.create(marble);await marbleCtrl.trade(marble.id, simon.id);
```

每个适配器都有自己的内部逻辑，例如， **FabricControllerAdapter** 处理与 Fabric 通信的网络配置文件，但 Convector 与 Fabric 没有紧密耦合，因此社区可以根据他们的需要创建不同的适配器。这正是我们计划在不久的将来通过用新的实现替换适配器来支持其他区块链的方式。
convector 中的适配器没有任何特殊处理，它们只是规定了与区块链层的通信，因此 Convector 中的标准适配器可以被开发人员或社区提供的自定义实现所替换。

## 结论

Composer 和 Convector 有很多相似之处，但他们选择了不同的建筑道路。使用 Convector 控制器，您几乎可以控制链代码中的所有逻辑，并且可以更加灵活地设计您认为适合您的用例的架构决策。

我们不断地向框架中添加新功能，我们很乐意听到您关于可能对社区有用的新功能的意见。您可以随时在我们的 github 中创建新问题，甚至[删除 PR。](https://github.com/worldsibu/convector)

*   [加入不和谐社区！](http://discord.gg/twRwpWt)
*   [获取代码！](https://github.com/worldsibu)