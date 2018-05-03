## 案例场景
再回到pizza店。。。。。。还记得Factory Method中的pizza店吗？OOP的pizza店设计很棒：具有弹性的框架，而且遵循设计原则【依赖倒置原则】。通过导入新的框架，很多加盟店加入了pizza店阵营，但是有一些加盟店使用低价原料来增加利润。你必须采取一些手段，以免长此以往毁了OOP pizza店的品牌。

要如何确保每家加盟店使用高质量的原料？你打算建造一家生产原料的工厂，并将原料运送到各家加盟店。对于这个做法，现在还剩下一个问题：加盟店坐落于不同地区，杭州的辣椒酱和四川的辣椒酱肯定是不一样的。也就是说，所有pizza店的pizza都是使用相同的组件【面团，酱料，芝士等】制造而成，但是每个区域对于这些原料却有不同的实现。
我们可以建造一个工厂来生产原料PizzaIngredientFactory，这个工厂将负责创建原料家族中的每一种原料【面团，酱料，芝士等】，然后为每个区域创造一个工厂——继承自PizzaIngredientFactory，来实现每一个创建方法，最后将新的原料工厂整合进旧的PizzStore代码。再回到Pizza店：
![](https://github.com/sdwang1/design-pattern/blob/master/blogimg/Abstract_Factory_pizzaStore_uml.png)

我们曾经写过工厂方法的代码，有HangzhouCheesePizza和ZhengzhouCheesePizza类，比较一下这两个类，唯一区别在于使用的区域性原料，至于pizza做法都一样【面团+酱料+芝士】，其他的pizza也是如此。所以，其实我们不需要设计两个不同的类来处理不同风味的pizza，让原料工厂处理这种区域差异就可以了。

一连串的代码改变，我们到底做了些什么？我们引入了新类型的工厂，也就是所谓的抽象工厂，通过抽象工厂所提供的接口可以创建产品家族【比如文例中，制作pizza所需要的一切东西：面团，酱料，芝士，肉和蔬菜】，利用这个接口书写代码，我们的代码从实际工厂解耦，以便在不同上下文中实现各式各样的工厂，制造出各种不同的产品。同时因为代码从实际产品中解耦了，所以我们可以替换不同的工厂来取得不同的行为，例如取得大蒜番茄酱料，而不是取得番茄酱料。
## 定义抽象工厂模式
**抽象工厂模式，提供一个接口，用于创建相关或依赖对象的家族，而不需要明确制定具体类。**
抽象工厂允许客户端使用抽象的接口来创建一组相关的产品，而不需要知道实际产出的具体产品是什么。这样让客户从具体的产品中被解藕。让我们看看类图来了解其中的关系：
![](https://github.com/sdwang1/design-pattern/blob/master/blogimg/Abstract_Factory_uml.png)
这是一张相当复杂的类图，让我们从PizzaStore的观点来看一看它：
![](https://github.com/sdwang1/design-pattern/blob/master/blogimg/Abstract_Factory_pizzaStore.png)
## 工厂方法和抽象工厂的区别
不知你是否注意到，抽象工厂的每个方法实际上看起来都像是工厂方法（比如createDough,createSauce()等）。每个方法都被声明成抽象，而子类的方法覆盖这些方法来创建某些对象，这不正是工厂方法吗？

没错，抽象工厂的方法经常以工厂方法的方式实现，这很有道理。抽象工厂的任务是定义一个负责创建一组产品的接口，这个接口内每个方法都负责创建一个具体产品，同时我们利用实现抽象工厂的子类的提供这些具体的做法，所以在抽象工厂中利用工厂方法实现生产方法是相当自然的做法。

**抽象工厂和工厂方法都是负责创建对象【易混淆】，然而Factory Method使用的方法是继承【创建对象时需要扩展一个类，并覆盖他的工厂方法】，Abstract Factory则是通过对象的组合【提供一个创建产品家族的抽象类型，这个抽象类型的子类定义了产品被产生的方法，要想使用这个工厂，必须先实例化它，然后将它传入一些针对抽象类型所写的代码中】，相较于Factory Method它的另一个优点是把一群相关的产品集合了起来。**
## Real-World code in php
```php
abstract class PizzaIngredientFactory{
  public function createDough();
  public function createSauce();
  public function createCheese();
  public function createVeggies();
  public function createClam();
}

/************************************************/
class HangzhouPizzaIngredientFactory extends PizzaIngredientFactory{
  public function createDough(){
    return new ThinCrustDough();
  }

  public function createSauce(){
    return new MarinaraSauce();
  }

  public function createCheese(){
    return new ReggianoCheese();
  }
  public function createVeggies(){
    return [new Garlic(),new Mushroom()];
  }
  public function createClam(){
    return new FreshClams();
  }
}

abstract class Pizza
{
    protected $name;
    protected $dough;
    protected $sauce;
    protected $cheese;
    protected $veggies=[];
    protected $clam;

    abstract public function prepare();

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
class CheesePizza extends Pizza
{
    private $ingredientFactory;
    public function __construct($ingredientFactory)
    {
        $this->name = "Cheese Pizza";
        $this->ingredientFactory = $ingredientFactory;
    }
    public function prepare(){
      echo "Preparing ".$this->name.PHP_EOL;
      $this->dough =  $this->ingredientFactory->createDough();
      $this->sauce =  $this->ingredientFactory->createSauce();
      $this->cheese =  $this->ingredientFactory->createCheese();
    }
}

/************************************************/
class ClamPizza extends Pizza
{
    private $ingredientFactory;
    public function __construct($ingredientFactory)
    {
        $this->name = "Clam Pizza";
        $this->ingredientFactory = $ingredientFactory;
    }

    public function prepare(){
      echo "Preparing ".$this->name.PHP_EOL;
      $this->dough =  $this->ingredientFactory->createDough();
      $this->sauce =  $this->ingredientFactory->createSauce();
      $this->cheese =  $this->ingredientFactory->createCheese();
      $this->clam = $this->ingredientFactory->createClam();
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
        $ingredientFactory = new HangzhouPizzaIngredientFactory();
        switch ($type){
            case 'cheese':
                $pizza = new CheesePizza($ingredientFactory);
                break;
            case 'clam':
                $pizza = new ClamPizza($ingredientFactory);
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
      $ingredientFactory = new ZhengzhouPizzaIngredientFactory();
      switch ($type){
          case 'cheese':
              $pizza = new CheesePizza($ingredientFactory);
              break;
          case 'clam':
              $pizza = new ClamPizza($ingredientFactory);
              break;
      }
      return $pizza;
    }
}
```
