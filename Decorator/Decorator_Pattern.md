## 案例场景
星巴克是以扩张速度最快而闻名的咖啡连锁店，因为扩张速度实在太快了，他们准备更新订单系统，以合乎他们的饮料供应要求。他们原先的类设计是这样的... ...
![](https://github.com/sdwang1/design-pattern/blob/master/blogimg/Decorator_Startbuzz.png)
购买咖啡时，可以要求在其中加入各种调料，例如蒸奶(Streamed Milk)、豆浆(Soy)、摩卡(Mocha)或者覆盖奶泡。星巴克会根据所加入的调料收取不同的费用，所以订单系统必须考虑到这些调料部分。直观地，你可能会从Beverage基类下手，利用实例变量和继承追踪这些调料（牛奶，豆浆，摩卡，奶泡... ...）:
![](https://github.com/sdwang1/design-pattern/blob/master/blogimg/Decorator_Startbuzz_ingredient.png)
思考下，当哪些需求或因素改变时会影响这个直观的设计？ 【调料价格改变，新的调料，双倍摩卡咖啡】
## 开放-关闭原则
**类应该对外扩展开放，对修改关闭。** 我们的目标是允许类容易扩展【接受新功能来应对改变的需求】，在不修改现有代码的情况下就可搭配新的行为。虽然似乎有点矛盾，但的确有一些技术可以允许在不直接修改代码的情况下对其进行扩展【观察者模式可以动态增加主题而不修改观察者代码】。

在选择需要被拓展的代码部分时要小心【遵循开放-关闭原则通常会引入新的抽象层次，增加代码复杂度】。每个地方都采用开放-关闭原则是一种浪费，也没必要，还会导致代码变得复杂且难以理解。
## 认识装饰者模式
我们已经了解利用继承无法完全解决问题，在星巴克遇到的问题有：类数量爆炸，设计死板，以及基类加入新的功能并不适用于所有的子类。所以在这里要采用不一样的做法：我们要以饮料为主体，然后运行时以调料来“装饰”饮料。比方说顾客想要摩卡和奶泡深培咖啡，那么：
![](https://github.com/sdwang1/design-pattern/blob/master/blogimg/Decorator_Breverage_sample.png)
OK，这是目前所知道的一切：
- 装饰者和被装饰对象有相同的超类型；
- 既然装饰者和被装饰对象有相同的超类型，所以在任何需要原始对象（被包装）的场合，可以用装饰过的对象代替它；
- **装饰者可以在所委托被装饰者的行为之前/之后，加上自己的行为，以达到特定的目的；**
- 对象可以在任何时候被装饰，所以可以在运行时动态地，不限量地用你喜欢的装饰者来装饰对象。

## 定义装饰者模式
**装饰者模式，动态地将责任附加到对象上，若要扩展功能，装饰者提供了比继承更有弹性的替代方案**。 UML类图如下：
![](https://github.com/sdwang1/design-pattern/blob/master/blogimg/Decorator_uml.png)
好吧，让星巴克饮料也能符合此框架：
![](https://github.com/sdwang1/design-pattern/blob/master/blogimg/Decorator_Startbuzz_uml.png)
看看类图，CondimentDecorator扩展自Beverage类，这么做的重点在于装饰者和被装饰者必须是一样的类型，**在这里我们利用继承达到“类型匹配”【因为装饰者必须能取代被装饰者】，而不是“获得行为”。当我们将装饰者与组件组合时，就是在加入新的行为，所得到的新行为并不是继承自超类，而是由组合对象得来的。** 如果依赖继承，那么类的行为只能在编译时静态决定，即行为如果不是来自超类，就是子类覆盖后的版本，反之利用组合，可以把装饰者混合着用。。。而且是在运行时。
## Real-World code in php
```php
abstract class Beverage
{
    protected $description="Unknown Beverage";

    /**
     * @return string
     */
    public function getDescription()
    {
        return $this->description;
    }

    abstract public function cost();
}

/*****************************************************/
class Espresso extends Beverage
{
    public function __construct()
    {
        $this->description="Espresso";
    }

    public function cost()
    {
        return 1.99;
    }
}

/*****************************************************/
class HouseBlend extends Beverage
{
    public function __construct()
    {
        $this->description="House Blend Coffee";
    }

    public function cost()
    {
       return .89;
    }
}

/*****************************************************/
abstract class CondimentDecorator extends Beverage
{
    abstract public function getDescription();
}

/*****************************************************/
class Mocha extends CondimentDecorator
{
    private $beverage;

    public function __construct(Beverage $beverage)
    {
        $this->beverage = $beverage;
    }
    public function getDescription()
    {
        return $this->beverage->getDescription()." , Mocha";
    }

    public function cost()
    {
        return .20 + $this->beverage->cost();
    }
}

/*************************************************************/
$beverage = new Espresso();
$beverage = new Mocha($beverage);
$beverage = new Mocha($beverage);
$beverage = new Whip($beverage);
```
## Dumb Questions
Q1: **如果将代码针对特定种类的具体组件（例如HouseBlend）做一些特殊的事（比如打折）** ，那么这样的设计是否恰当，因为一旦用装饰者包装HouseBlend就会造成类型改变？的确如此，如果把代码写成依赖具体的组件类型，那么装饰者就会导致程序出问题，此时你应考虑你的应用架构，以及装饰者是否合适。

Q2: 还记得上节的“Adapter Pattern”嘛，它允许客户使用新的库和子集合，无须改变“任何”代码。装饰者似乎也可以做到，可让“新行为”加入类中，而无需修改现有代码，那么 **可认为适配器只是一种装饰者的变体【都是用来包装对象】吗？才不是这样，适配器“一定会”进行接口的转换【use Adaptee】，但装饰者“绝不会”这么做【不改变接口，但加入责任】** 。它们虽然纸上看起来很类似，其实意图差异颇大。
