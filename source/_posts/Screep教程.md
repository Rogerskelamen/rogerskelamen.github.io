---
title: Screep教程
date: 2020-10-24 23:05:27
updated: 2020-10-26 22:34:30
author: Rogers Kelamen
index_img: https://s2.loli.net/2022/03/26/8MORdeFXylr4AID.png
banner_img: https://s2.loli.net/2022/03/26/WeBHgNGtlqyCpXa.png
tags:
- js
- steam
categories: games
---



# Screeps教程（已断更）

> Screeps 是一款面向编程爱好者的开源 MMO RTS 沙盒游戏，其核心机制是为您的单位编写 AI。您可以通过编写 JavaScript 来控制自己的殖民地。您的代码将 24/7 全天候不间断地运行在一个庞大世界中。不仅如此，这个世界同时也容纳了许许多多和您一样的玩家。<font color=#34b>——来自steam页面介绍原文</font>

嗯，既然是用JS游玩，那就太符合我现阶段研究JS的需求了😁。但是这个游戏有点需要记忆的东西（况且教程又是英文常规操作），于是就先写下了这篇教程（可能还能帮助到其他人吧）——说干就干。

# GET START

## Concepts

- <font size=4>Room</font>

  这是你的大本营，你的前期开荒操作都在这里

- <font size=4>spawn</font>

  你最开始产生unit（我个人理解为最初劳动力）的地方

  **每一把开局时room里就会有一个spawn，一般默认名字是Spawn1**

- <font size=4>creeps</font>

  那些个units在游戏中被称作为"creeps"

## Create your first creep

![](https://s2.loli.net/2022/03/26/VhplyDngN9UL7mB.png)

通过Game这个对象（全局对象？）我们可以定位到一个spawn（**使用Game下的`spawns[]`方法，传入一个spawn的名字**）

然后我们就可以用spawn下面的`spawnCreep()`方法创建一个creep了！（这里面有点对象迭代的思想）

*`spawnCreep()`中有几个参数，主要定义了这个creep的名字和它的一些属性*

比如你要是想要一个叫Harvester1的矿工work creep，你可以用以下代码：

```js
Game.spawns['Spawn1'].spawnCreep( [WORK, CARRY, MOVE], 'Harvester1' );
```

## View

![](https://s2.loli.net/2022/03/26/c3YWlHN6i9yxTJL.png)

在游戏窗口的最顶部，有四个可点击按钮，**view（视窗模式）是初始默认选中的，在此模式下你可以查看你的spawn，creeps等的属性信息，也可以查看地形、坐标等简单信息。**

**<font color=#34b>通过在view下选中一个spawn或creep就可以查看这个元素的基本信息。</font>**（信息会出现在右边栏）

![](https://s2.loli.net/2022/03/26/3gZOVAraDBwnhTt.png)

# Get Resource

> 当你制造出了第一个worker creep的时候，恭喜你，你可以开始挖矿了！

## Do it in script

如果你想要你的creep一直不停歇的挖矿，你要是还是用console显然是不够了的，这时候就得用上Script区域。

**Script区域里是你写好的script代码，当你运行代码时，它就会一直运行下去。**

> 正式开始了编程的旅程

## Go to work！

![](https://s2.loli.net/2022/03/26/qxmHQaopCUJIchF.png)

使用Game对象下的`creeps[]`来定位到一个creep（通过名字定位）

接着使用Room对象下的`find()`方法来找到一个资源点

### `FIND_SOURCES`

这是一个查找资源的一个内置常量，和之后的`ERR_NOT_IN_RANGE`一样。

**要是将这个常量写到`find()`方法中，则会选定到所有room中的资源<font color=#34b>（返回的是一个数组，索引值越大资源越远）</font>**

然后在module.exports.loop这个全局方法中，我们的creep会按照代码一直loop执行。

```js
module.exports.loop = function () {
    var creep = Game.creeps['Harvester1'];
    var sources = creep.room.find(FIND_SOURCES);
    if(creep.harvest(sources[0]) == ERR_NOT_IN_RANGE) {
        creep.moveTo(sources[0]);
    }
}
```

## Carry and come back

![](https://s2.loli.net/2022/03/26/VseilorTgpHAGbU.png)

想要满载的creep返回到spawn里去，我们可以用Creep下的`transfer()`方法

然后通过Creep下的`.store.getFreeCapacity()`我们可以判断它到底还能不能装下去，即用`.store.getFreeCapacity() > 0`。

最后我们的代码会变成这样：

```js
module.exports.loop = function () {
    var creep = Game.creeps['Harvester1'];

    if(creep.store.getFreeCapacity() > 0) {
        var sources = creep.room.find(FIND_SOURCES);
        if(creep.harvest(sources[0]) == ERR_NOT_IN_RANGE) {
            creep.moveTo(sources[0]);
        }
    }
    else {
        if( creep.transfer(Game.spawns['Spawn1'], RESOURCE_ENERGY) == ERR_NOT_IN_RANGE ) {
            creep.moveTo(Game.spawns['Spawn1']);
        }
    }
}
```

*稍微提一嘴，`moveTo()`方法可以让creep移动到参数中指定的地点。*

> 在去每一个地点时，我们都用到了常量`ERR_NOT_IN_RANGE`，这是为了判断这个资源或地点是否就在跟前。（<u>建议每次有移动时判断一下是否在附近，不然就移动不了了</u>）

# awsl

> 不要以为你的creep就会一直这么卖力的工作下去，它也会累死的！(ToT)/~~~

**每个creep都有一个生命周期：1500 game ticks**

# I need More!

> 既然知道你的creep会die，那么我们只能不停地制造新的creep了

还是熟悉的配方：

```js
Game.spawns['Spawn1'].spawnCreep( [WORK, CARRY, MOVE], 'Harvester2' );
```

warning：<font color=brown>你得首先攒足200能量点，不然只会返回ERR_NOT_ENOUGH_ENERGY（-6）</font>

*当我们的第二个creep被创造出来之后，我们的代码也要改改了*

```js
module.exports.loop = function () {
    for(var name in Game.creeps) {
        var creep = Game.creeps[name];

        if(creep.store.getFreeCapacity() > 0) {
            var sources = creep.room.find(FIND_SOURCES);
            if(creep.harvest(sources[0]) == ERR_NOT_IN_RANGE) {
                creep.moveTo(sources[0]);
            }
        }
        else {
            if(creep.transfer(Game.spawns['Spawn1'], RESOURCE_ENERGY) == ERR_NOT_IN_RANGE) {
                creep.moveTo(Game.spawns['Spawn1']);
            }
        }
    }
}
```

> 用for in循环实现了所有的creeps都去搬矿了

# Improve the code

## Create module

首先创建一个module来分开管理我们的代码，取名为`roleHarvester`（则它在script中的模块名为role.harvester）

里面有一个属性名为`run`，这个属性（其实是一个方法）可以放入入我们之前写好的代码块，之后在模块导入时我们就用这个`run`方法中的代码来执行。

```js
var roleHarvester = {

    /** @param {Creep} creep **/
    run: function(creep) {
	    if(creep.store.getFreeCapacity() > 0) {
            var sources = creep.room.find(FIND_SOURCES);
            if(creep.harvest(sources[0]) == ERR_NOT_IN_RANGE) {
                creep.moveTo(sources[0]);
            }
        }
        else {
            if(creep.transfer(Game.spawns['Spawn1'], RESOURCE_ENERGY) == ERR_NOT_IN_RANGE) {
                creep.moveTo(Game.spawns['Spawn1']);
            }
        }
	}
};

module.exports = roleHarvester;
```

*记住，要创建模块就要在main方法下面输入module的名字，然后在这个模块里面写上如上的代码*

## Use the Module

**在主模块（main module）之外，我们得建立一个用来引入模块的变量，用`require()`全局方法可以指明是哪个模块。**

之后我们就可以在主模块中用这个模块变量来省去很多工作量了：

```js
var roleHarvester = require('role.harvester');

module.exports.loop = function () {

    for(var name in Game.creeps) {
        var creep = Game.creeps[name];
        roleHarvester.run(creep);
    }
}
```

# Controller

> 通过controller，我们可以建造更多的建筑或工具，但是前提是你controller的level足够高。。

## upgrade it

![](https://s2.loli.net/2022/03/26/wAizMoT9eH46Ojt.png)

首先咱们还是得创建一个用来升级controller的creep，就先叫做Upgrader1

```js
Game.spawns['Spawn1'].spawnCreep( [WORK, CARRY, MOVE], 'Upgrader1' );
```

> 但是这个Upgrader1被产出之后，他还是做着和harvester一样的事。

<font size=4>new concept: Memory</font>

通过Memory，我们可以自定义每个creep的角色（role），然后使其做不同的工作。

```js
Game.creeps['Harvester1'].memory.role = 'harvester';
Game.creeps['Upgrader1'].memory.role = 'upgrader';
```

**你可以在每一个creep的状态页查看memory，也可以直接在下方的Memory栏中查看**

现在我们再定义一个module来使Upgrader1真正工作起来，发挥它本应的作用。

```js
var roleUpgrader = {

    /** @param {Creep} creep **/
    run: function(creep) {
	    if(creep.store[RESOURCE_ENERGY] == 0) {
            var sources = creep.room.find(FIND_SOURCES);
            if(creep.harvest(sources[0]) == ERR_NOT_IN_RANGE) {
                creep.moveTo(sources[0]);
            }
        }
        else {
            if(creep.upgradeController(creep.room.controller) == ERR_NOT_IN_RANGE) {
                creep.moveTo(creep.room.controller);
            }
        }
	}
};

module.exports = roleUpgrader;
```

**这里我们用到了creep的`upgradeController`方法，用这个方法去升级controller**

接着咱们把模块导入到main模块中去：

```js
var roleHarvester = require('role.harvester');
var roleUpgrader = require('role.upgrader');

module.exports.loop = function () {

    for(var name in Game.creeps) {
        var creep = Game.creeps[name];
        if(creep.memory.role == 'harvester') {
            roleHarvester.run(creep);
        }
        if(creep.memory.role == 'upgrader') {
            roleUpgrader.run(creep);
        }
    }
}
```

**<font color=brown>每过20000 ticks，controller会自动掉一级，掉完到0级你的room就没了</font>，所以至少确保有一个creep进行升级工作。**

# Build Structure

<font size=4>new concept: Extension</font>

Extension是建造更大的creep的必需品，但是你得先扩展spawn，<u>不然300能量点的储量怎么够呢</u>

## create Extension

![](https://s2.loli.net/2022/03/26/yQpN71YBT5Rnrlk.png)

首先，你必须要创建一个creep用来专门搞出extension，我们暂时就把这种creep叫做builder了，**然后直接在产生creep的命令中写上它的memory（其实之前的creep都可以这么做）**

```js
Game.spawns['Spawn1'].spawnCreep( [WORK, CARRY, MOVE], 'Builder1',
    { memory: { role: 'builder' } } );
```

## Create Builder Role

![](https://s2.loli.net/2022/03/26/83bykhnGYxsOQju.png)

