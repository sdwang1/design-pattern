## 案例场景
OO适配器，顾名思义，就是将一个接口转换成另一个接口，以符合客户期望。现实中适配器的例子比比皆是，比如：
![](https://github.com/sdwang1/design-pattern/blob/master/blogimg/Adapter_real_world.png)
假设你已有一个软件系统，希望它能和一个新的厂商类库搭配使用，但是这个新厂商所设计出的接口，不同于旧厂商的接口。你不想改变现有的代码解决这个问题（为了系统和平稳定），而且你也不能改变厂商的代码，所以该怎么做？这个可以写个类，将新厂商接口转换成你所期望的接口，让这个适配器如同中间人样工作，它能将客户发出的请求转换成厂商类能理解的请求。
![](https://github.com/sdwang1/design-pattern/blob/master/blogimg/Adapter_sample_factory.png)
举个具体实际的例子，假设你有个继承了MediaPlayer的AudioPlayer实体类，它能自动播放mp3格式的多媒体文，现还有另一个继承自AdvancedMediaPlayer的MoreAdvancedMediaPlayer实体类，它能播放vlc and mp4格式的多媒体文件。我们想要AudioPlayer也能播放其他格式的多媒体文件，为此新建适配器类——MediaAdapter，它继承自MediaPlayer，并用AdvancedMediaPlayer objects转换成客户期望的接口：
![](https://github.com/sdwang1/design-pattern/blob/master/blogimg/Adapter_sample_player.png)


让我们再靠近一点：首先，MediaAdapter要实现想要转换成的类型接口（MediaPlayer），也就是客户所期望看到的接口；其次取得要适配的对象的引用（$advancedMediaPlayer）；最后通过适配对象实现接口（MediaPlayer）中所有方法。
## 适配器模式解析
现在我们已经知道什么是适配器了，让我们后退一步，再次看看各部分之间的关系：
![](https://github.com/sdwang1/design-pattern/blob/master/blogimg/Adapter_parse.png)


1.客户通过目标接口【MediaPlayer的play接口】调用适配器的方法对适配器发出请求；2.适配器使用被适配者【AdvancedMdeiaPlayer】接口把请求转换成被适配者的一个或多个调用接口；3.客户端收到调用结果，但并未察觉这一切是适配器在起转换作用。
## 定义适配器模式
**适配器模式，将一个类的接口转换成客户期望的另一个接口。适配器让原本接口不兼容的类可以合作无间。**
它让客户从实现的接口解藕，如果一段时间后，我们想改变接口【比如再换个厂商】，适配器可以将改变的地方封装起来，客户不必为了应对不同的接口而每次跟着修改。
![](https://github.com/sdwang1/design-pattern/blob/master/blogimg/Adapter_Pattern_ouml.png)

Q1: 既然适配器模式的工作是将一个类接口转换成另一个，那么**一个适配器只能封装一个类吗？** 我们知道这个世界其实复杂多了，你可能遇到一些情况，需要让一个适配器包装多个被适配者，这设计另一个模式，被称为外观模式（Facade Pattern），人们常常易于将外观模式和适配器模式混为一谈，我们稍后详细说明。

Q2: **万一我的系统中新旧并存，旧的部分期望旧的厂商接口，但我们却已经使用新厂商的接口编写了这一部分，这个时候该怎么办？** 这里使用适配器，那里却使用未包装的接口，这实在让人感到混乱。如果我只固守旧的代码，完全不管适配器会不会好些？ 我们可以创建一个双向适配器【同时实现所涉及的两个接口】，这样这个适配器可以当作旧接口，或者当作新接口使用。
## 对象和类的适配器
现在尽管已经定义了这个模式，但“冰山之下才是真相”，实际上有“两种”适配器：对象适配器和类适配器，模式定义中的类图是对象适配器图，让我们看下类适配器的类图【多重继承】：
![](https://github.com/sdwang1/design-pattern/blob/master/blogimg/Adapter_Pattern_cuml.png)
看起来很熟悉吗？没错，唯一的差别就在于适配器继承了Target和Adaptee，而对象适配器则利用组合的方式将请求传送给被适配者。
## Real-World code in php
```php
<?php
//早期集合类型中的Enumeration   Adaptee
abstract class Enumeration
{
    abstract public function hasMoreElements();
    abstract public function nextElement();
}
//Sun推出了更新后的集合类      Target
abstract class SunIterator
{
    abstract public function hasNext();
    abstract public function next();
    abstract function remove();
}
//面对遗留代码，这些代码暴露出枚举类型接口，我们当然希望新代码中只使用迭代器
class EnumerationIterator extends SunIterator
{
    private $enum;
    public function __construct(Enumeration $enumeration)
    {
        $this->enum = $enumeration;
    }

    public function hasNext()
    {
        return $this->enum->hasMoreElements();
    }

    public function next()
    {
        return $this->enum->nextElement();
    }

    public function remove()
    {
        throw new \Exception("UnSupported Operation",1);
    }
}
```

