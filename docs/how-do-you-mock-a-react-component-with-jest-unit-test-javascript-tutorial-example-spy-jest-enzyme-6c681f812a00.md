# [单元测试]如何用 Jest 来模拟反应组件？

> 原文：<https://medium.com/hackernoon/how-do-you-mock-a-react-component-with-jest-unit-test-javascript-tutorial-example-spy-jest-enzyme-6c681f812a00>

![](img/b17b60b0778cc2eab76d03e07ba43291.png)

jest enzyme test unit component

> 您如何用 Jest 来模拟反应组件？

我们得到了一个组件，它包含了另一个外部组件`**InstallCom**` **和**`**UserCom**`**(**[*git hub*](https://gist.github.com/wahengchang/108ca55981f6600c252ad0cb9d4c518f)**):**

```
import { **InstallCom** } from 'installComponent'
import **UserCom** from 'userComponent'export class ShareCom extends Component {
   render() {
      return (
         <div>
            <**InstallCom** para1='title1'/>
            <**UserCom** para2='title2' />
         </div>
      )
   }
}
```

## 单元测试

在单元测试中，我们关心的是<sharecom>的功能性，而不是其他依赖的组件`**InstallCom**`**`**UserCom**`，出于模拟目的， ***jest*** 是很好的帮手。**</sharecom>

```
import {ShareCom} from '../somewhere/ShareCom'***jest.mock***('../somewhere/UserCom', () => ()=> <div id="mockUserCom">
   mockUserCom
</div>)***jest.mock***('installComponent', () => ({
   InstallCom: 'mockInstallCom'
})) describe('ShareCom', () => {
   it('should return correct component', () => {
      const wrapper = mount(
         <ShareCom 
            something={something}
         />
      ) expect(wrapper.find('mockInstallCom').length).toEqual(1)
      expect(wrapper.find('#mockUserCom').length).toEqual(1)
   })
})
```

## **参考:**

**—Git:[https://gist . github . com/wah engchang/108 ca 55981 f 6600 c 252 ad 0cb 9 D4 c 518 f](https://gist.github.com/wahengchang/108ca55981f6600c252ad0cb9d4c518f)**