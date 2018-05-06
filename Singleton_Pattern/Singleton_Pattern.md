## 案例场景
单件模式的类图可以说是所有模式的类图中最简单的，事实上，它的类图只有一个类！这有什么用处？其实有些对象我们是只需要一个，比方说线程池，缓存，对话框，日志对象，注册表设置，充当打印机等，如果制造出多个实例，就会导致许多问题产生，例如程序行为异常，资源使用过量或者是不一致的结果。

或许的确有一些类应该只存在一个实例，但这需要独辟出个设计模式【比如简单的静态变量】来说明吗？举例来说，如果将对象赋值给一个全局/静态变量，那么你必须在程序一开始就创建好对象，万一这个对象非常耗费资源，而程序这次的执行过程中又一直没用到它，不就形成浪费了吗？稍后您会看到，利用单件模式我们可以在需要时才创建对象。
## 定义单件模式
**单件模式，确保一个类只有一个实例，并提供一个全局访问点。** 这种做法对资源敏感的对象特别重要。
![](https://github.com/sdwang1/design-pattern/blob/master/blogimg/Singleton_patter_uml.png)
假设你有一个巧克力锅炉，锅炉做的事就是把巧克力和牛奶融合在一起，然后煮沸冷却以制成巧克力棒。你要努力阻止不好的事情发生，比如锅炉没放原料的空烧，锅炉已满还继续放原料或者排出了500L的未煮沸的混合物：
```php
class ChocolateBoiler
{
    private $empty;
    private $boiled;
    private STATIC $uniqueInstance;
    PRIVATE function __construct()
    {
        $this->empty = true;
        $this->boiled = false;
    }

    PUBLIC STATIC function getInstance(){
        if (empty(self::$uniqueInstance)){
            self::$uniqueInstance = new ChocolateBoiler();
        }
        return self::$uniqueInstance;
    }

    public function fill(){
        if ($this->empty){
            $this->empty=false;
            $this->boiled=false;
            //在锅炉内填充巧克力和牛奶的混合物
        }
    }
}

/*********************************************/
$bolier = ChocolateBoiler::getInstance();
$bolier->fill();
$bolier->boil();
$bolier->drain();
```
## 处理多线程的麻烦
单件代码的巧克力锅炉原本是一切顺利的，但在将此代码投入多线程环境下【比如JVM】时，灾难就降临了:
![](https://github.com/sdwang1/design-pattern/blob/master/blogimg/Singleton_multi_thread.png)
解决多线程的灾难，可以有如下一些选择。。。。。。
1. 简单粗暴的做法——**只要把getInstance变成同步方法即可**，比如JAVA中可在getInstance方法前加synchronized关键字。事实上，只有第一次执行此方法时，才真正需要同步。换句话说，一旦设置好uniqueInstance变量，就不需要同步这个方法了，之后每次调用这个方法，同步都是累赘。
2. **可使用“急切”创建实例，而不是延迟创建实例的做法，** 大家都知道，同步会降低性能，如果getInstance()的程序使用在频繁运行的地方，你可能就要重新考虑了：如果应用程序总是创建并使用实例，或者在创建和运行时方面的负担不太繁重，可采用这种做法：
```php
class Singleton{
  private static $uniqueInstance = new Singleton();
  private function __construct(){}
  public static function getInstance(){
    return self::$uniqueInstance;
  }
}
```
3. **用“双重检查加锁”，在getInstance中减少同步，** 利用double-checked locking，首先检查实例是否已经创建过了，如果尚未创建“才”进行同步，这样只有第一次会同步，这正是我们想要的：
```java
public class Singleton{
  private volatile static Singleton uniqueInstance;
  private Singleton(){}
  public static Singleton getInstance(){
    if(uniqueInstance == null){
      synchronized (Singleton.class){
        if(uniqueInstance == null){
          uniqueInstance = new Singleton();
        }
      }
    }
    return uniqueInstance;
  }
}
```
