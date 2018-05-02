---
title: Factory Pattern
date: 2018-04-28 16:21:46
tags:
---
## 案例场景
假设你有一个Pizza店，里面会卖一些不同风味的Pizza，比如榴莲Pizza，培根Pizza，芝士Pizza等等。作为OOPer，你应该知道 **针对接口而非实现编程可以隔离掉以后系统可能发生的一大堆改变（因为针对接口而写，通过多态它可以与任何新类实现该接口）。** 那么身为Pizza店主人，你的代码可能这么写：
![](https://github.com/sdwang1/design-pattern/blogimg/origin_pizza_store.png)

<!--more-->

当顾客下单时，根据指定pizza的类型，我们实例化正确的具体类，然后将其赋值给pizza实例变量，一旦我们有了一个pizza，就可以准备馅皮（每个Pizza子类型都知道如何准备自己），然后烘烤，切片，装盒，最后完美售出。

随后，你发现你所有的竞争者都已经在他们的菜单中加入了一些流行风味的pizza：素食pizza，蛤蜊pizza，显然为了盈利，你也要把这些风味加进你的菜单中，而最近榴莲pizza卖的不好，你决定将它从菜单中去掉：
```php
public function orderPizze($type){
   if($type=='cheese'){
     pizza = new CheesePizza();
   }elseif($type=="liulan"){
     pizza = new LiulianPizza();
   }elseif($type=='clam'){
     pizza = new ClamPizza();
   }elseif($type=='veggie'){
     pizza = new VeggiePizza();
   }
   pizza.prepare();
   pizza.bake();
   pizza.cut();
   pizza.box();
   return pizza;
}
```
很显然由于增加更多的pizza类型，无法让orderPizza()对外修改关闭。幸运的是，我们已经知道哪些会改变，哪些不会改变，该是时候实现封装了。我们将创建对象移到orderPizza()之外，移到一个专职负责人——工厂中，我们先来建立一个简单pizza工厂：
![](https://github.com/sdwang1/design-pattern/blogimg/simpleFactory_pizza_store.png)
在某些简单场景中或许会直接粗暴的把创建对象这part移到PizzStore中新增方法createPizza()中负责，而更规范化的SimplePizzaFactory似乎只是把问题搬到另一个对象罢了，问题依然存在，这样做有什么好处呢？
别忘了，SimplePizzaFactory可以有很多的客户（除了目前只看到的OrderPizza方法），比如PzzaShopMenu可利用这个工厂来取得pizza价钱和描述。所以把创建Pizza的代码封装进一个类，当以后实现改变时，只需修改这个类即可。
## 定义简单工厂
简单工厂其实不是一个设计模式，反而比较像是一种编程习惯（有些开发人员的确把这个编程习惯误以为“工厂模式”（Factory Pattern））。但不要因为简单工厂不是一个“真正的”设计模式就忽略了它的用法，它也是经常被实际使用的哟，新的pizza店类图如下：
![](https://github.com/sdwang1/design-pattern/blogimg/simpleFactory_pizza_uml.png)
## 场景推进
OOP pizza店经营有方，现在大家都希望在自家附近有OOP pizzaStore的加盟店。为确保加盟店的运营质量，你希望这些店都使用你那些经过时间考验的代码。那么第一个问题——区域差异呢？每家加盟店都可能想要提供不同风味的pizza（比如北方偏好饼厚馅咸，南方偏好饼薄馅多），这受到开店地点及该地区pizza美食家口味的影响。如果利用SimpleFactory，写出两种不同的工厂NorthFactory,SouthFactory：
![](https://github.com/sdwang1/design-pattern/blogimg/simpleFactory2_pizza_uml.png)
在推广SimpleFactory时，你发现加盟店的确是采用你的工厂创建pizza，但是其他部分却开始采用他们自创的流程：烘烤做法有差异，不要切片，使用其它厂商的盒子。再想想这个问题，你真的希望能建立一个框架，把加盟店和创建pizza捆绑在一起的同时又保持一定的弹性。
有个做法可让pizza制作活动局限与pizzaStore类，而同时又能让这些加盟店可以自由的制作该区域的风味：
**把createPizza方法放回到PizzaStore中，不过将其设置为抽象方法，然后为每个区域风味创建一个PizzaStore的子类。**
![](https://github.com/sdwang1/design-pattern/blogimg/Factory_method_pizza_uml.png)
## 定义工厂方法模式
**工厂方法模式，定义了创建对象的接口，但由子类决定要实例化的类是哪一个。工厂方法让类把实例化推迟到子类。**
工厂方法模式能够封装具体类型的实例化，这里所谓的“决定实例化类”，并不是指模式允许子类本身在运行时做决定，而是指在编写创建者类时，不需要知道实际创建的产品是哪一个，选择了使用哪个子类，自然就决定了实际创建的产品是什么。
对于简单工厂和工厂方法之间的差异，他们看起来很类似，难道差别仅在于在工厂方法中，返回pizza的类是子类吗？ **子类的确看起来很像简单工厂，简单工厂把全部的事情放在一个地方处理完了，然而工厂方法却是创建一个框架，让子类决定要如何实现。简单工厂的做法，可以将对象的创建封装起来，但是简单工厂不具备工厂方法的弹性，因为简单工厂不能变更正在创建的产品。**
## Real-World code in php
```php
abstract class Pizza
{
    protected $name;
    protected $dough;
    protected $sauce;
    protected $toppings = [];

    public function prepare(){
        echo "Preparing ".$this->name.PHP_EOL;
        echo "Tossing dough...\n";
        echo "Adding sauce...\n";
        echo "Adding toppings:\n";
        foreach ($this->toppings as $item)
            echo " ".$item;
    }

    public function bake(){
        echo "Bake for 25 minutes at 350\n";
    }

    public function cut(){
        echo "Cutting the pizza into diagonal slice\n";
    }

    public function box(){
        echo "Place pizza in official PizzaStore box\n";
    }

    /**
     * @return mixed
     */
    public function getName()
    {
        return $this->name;
    }
}

/************************************************/
class HangzhouCheesePizza extends Pizza
{
    public function __construct()
    {
        $this->name = "Hangzhou Style Sauce and Cheese Pizza";
        $this->dough = "Thin Dough";
        $this->sauce = "Marinara Sauce";

        $this->toppings = ['Grated Reggiano Cheese'];
    }
}

/************************************************/
class ZhengzhouCheesePizza extends Pizza
{

    public function __construct()
    {
        $this->name = "Zhengzhou Style Deep Dish Cheese Pizza";
        $this->dough = "Extra Fat Crust Dough";
        $this->sauce = "Plum Tomato Sauce";

        $this->toppings = ["Shredded Mozzarella Cheese"];
    }

    public function box()
    {
        echo "Cutting the pizza into square slices\n";
    }
}

/************************************************/
/************************************************/
abstract class PizzaStore
{
    public function orderPizza($type){
        $pizza = $this->createPizza($type);

        $pizza->prepare();
        $pizza->bake();
        $pizza->cut();
        $pizza->box();
        return $pizza;
    }

    abstract function createPizza($type);
}

/************************************************/
class HangzhouPizzaStore extends PizzaStore
{
    public function createPizza($type)
    {
        $pizza = null;
        switch ($type){
            case 'cheese':
                $pizza = new HangzhouCheesePizza();
                break;
            case 'liulian':
                $pizza = new HangzhouLiulianPizza();
                break;
        }
        return $pizza;
    }
}

/************************************************/
class ZhengzhouPizzaStore extends PizzaStore
{

    public function createPizza($type)
    {
        $pizza = null;
        switch ($type){
            case 'cheese':
                $pizza = new ZhengzhouCheesePizza();
                break;
            case 'liulian':
                $pizza = new ZhengzhouLiulianPizza();
                break;
        }
        return $pizza;
    }
}
```
## 设计原则
工厂方法模式可以启发我们一个OO设计原则————**要依赖抽象，不要依赖具体类【Dependency Inversion Principle】**.
这个原则听起来是不是很像“针对接口编程，不针对实现编程”？的确相似，然而这里更强调“抽象”，这个原则说明了：**不能让高层组件依赖低层组件，而且不管高层或低层组件，“两者”都应该依赖于抽象。** 比如本文章案例，PizzStore是“高层组件”，而pizza实现是“低层组件”，很清楚地，PizzaStore依赖这些具体Pizz类。如果不应用“依赖倒置原则”，pizzaStore在自己的orderPizza方法中，直接实例化这些具体类型，那么一个“非常依赖的pizzaStore”代码就如下所示：
![](https://github.com/sdwang1/design-pattern/blogimg/Dependency_pizza_store.png)
想要遵循依赖倒置原则，工厂方法并非是唯一的技巧，但却是最有威力的技巧之一。

