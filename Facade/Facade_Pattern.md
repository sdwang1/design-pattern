## 案例场景
你已经知道适配器模式是如何将一个类接口转换成另一个符合客户期望的接口的，现在我们要看一个改变接口的新模式，但是它改变接口的原因是为了简化接口，这个模式被巧妙的称为外观模式（Facade-Pattern），它将一个或多个类的复杂一切都隐藏在背后，只显露出一个干净美好的外观。
假设你土豪地建立自己的家庭影院，并且经过一番研究比较，你组装了一套杀手级系统，内含DVD播放器，摄影机，自动屏幕，环绕立体声，甚至还有爆米花机。看看这些组件的组成：
![](https://github.com/sdwang1/design-pattern/blob/master/blogimg/Facade_Homefilm.png)
你花了好几个星期布线，挂上投影机、连接所有装置并进行微调，现在你挑选一步DVD影片，准备开始感受电影的魔幻魅力。哎呀，忘了一件事：想看电影，必须先执行一些任务：1.打开爆米花机；2.开始爆米花；3.将灯光调暗；4.放下屏幕；5.打开投影机；6.将投影机输入切换到DVD并设置在宽屏模式；7.打开功放；8.将功放的输入设置为DVD环绕立体声，并将音量调到中；9.打开DVD播放器；10.开始播放DVD；让我们将这些任务写成类和方法的调用：
```
<?php
popper.on();           //打开爆米花机，开始爆米花
popper.pop();

lights.dim(10);

screen.down();

projector.on();        //打开投影机并设置为宽屏模式
projector.setInput(dvd);
projector.wideScreeenMode();

amp.on();              //打开功放，设置为DVD环绕立体声
amp.setDvd(dvd);
amp.setSurroundSound();
amp.setVolume(5);

dvd.on();
dvd.play(movie);
```
但还不只这样，看完电影后，你还要把这一切都关掉，怎么办？难道反向地把这一切动作再进行一次？？

你需要的正是一个外观模式，通过实现一个提供合理的接口的外观类，你可以将一个复杂的子系统变得容易使用。让我们看看外观如何运作： **我们创建一个名为HomeTheaterFacade的新类，它对外暴露出几个简单的方法，例如watchMovie(),endMovie()，这个外观类将家庭影院的诸多组件视为一个子系统，通过调用这个子系统来实现watchMovie()方法。**
![](https://github.com/sdwang1/design-pattern/blob/master/blogimg/Homefilm_uml.png)
外观只是提供你更直接的操作，并未将原来的子系统阻隔起来。如果你需要子系统类的更高层功能，还是可以使用原来的子系统。
## 定义外观模式
**外观模式，提供了一个统一的接口，用于访问子系统中的一群接口。外观定义了一个高层接口，让子系统更容易使用。** 这很容易理解，但是请务必记得模式的意图。这个定义清楚地告诉我们，外观的意图是提供一个简单的接口，好让子系统更易于使用。从这个模式的类图可以感受到这一点：
![](https://github.com/sdwang1/design-pattern/blob/master/blogimg/Facade_Pattern_uml.png)
## Dumb Questions
Q1: 除了能够提供一个比较简单的接口之外，外观模式还有其他优点吗？**外观模式也允许你将客户实现从任何子系统中解藕** ，比方说，你升级了家庭影院，采用了全新的和以前不一样接口的组件，如果当初你的客户代码是针对外观而不是针对子系统编写的，现在你就不需要改变客户代码。

Q2: 我可不可以这样说，适配器模式和外观模式之间的差异在于：适配器包装一个类，而外观模式可以代表许多类？不对呦，**外观和适配器都可以包装许多类，两种模式的差异不在于他们“包装”了几个类，而是在于它们的意图：外观模式的意图是简化接口，而适配器的意图是将接口转换成不同的接口。**
## “最少知识”原则
**最少知识原则：只和你的密友谈话。** 我们要减少对象之间的交互，只留下几个“密友”，这就要求当你在设计一个系统时，不管任何对象，你都要注意它所交互的类有哪些，并注意它和这些类是如何交互的，不要让太多的类耦合在一起，省的系统变成一个易碎的系统。

究竟要怎样才能避免呢？这个原则提供了一些方针：**就任何对象而言，在该对象的方法内，我们只应该调用属于以下范围方法：该对象本身、被当作方法的参数传递进来的对象；此方法所创建或实例化的任何对象；对象的任何组件；**
## Real-World code in php
```php
<?php
class HomeTheaterFacade{
  private $amp;
  private $tuner;
  private $dvd;
  private $cd;
  private $projector;
  private $lights;
  private $screen;
  private $popper;

  public function watchMovie($movie){
    echo "Get ready to watch a movie...$movie".PHP_EOL;
    $this->popper->on();          
    $this->popper->pop();
    $this->lights->dim(10);
    $this->screen->down();
    $this->projector->on();        
    $this->projector->setInput(dvd);
    $this->projector->wideScreeenMode();
    $this->amp->on();             
    $this->amp->setDvd(dvd);
    $this->amp->setSurroundSound();
    $this->amp->setVolume(5);
    $this->dvd->on();
    $this->dvd->play(movie);
  }

  public function endMovie(){
    echo "Shutting movie theater down".PHP_EOL;
    $this->popper->off();
    $this->lights->on();
    $this->screen->up();
    $this->projector->off();
    $this->amp->off();
    $this->dvd->stop();
    $this->dvd->eject();
    $this->dvd->off();
  }
}
```
