**ViewModel 组件**

https://www.youtube.com/watch?v=mpO-aEXhX78&t=2s

为了帮助开发者 便于处理生命周期变化，同时让应用程序，易于维护和测试。推出了架构组件的生命周期管理库。



viewModel 是一个提供和管理UI界面数据，并且可感知生命周期的组件。

ViewModel 的一大特点是 它不会应为设置变更而销毁。



当用户旋转手机屏幕，发生设置改变，造成activity 被销毁，然后重建。如果在这个过程中，没有很好的处理 保存界面数据 并重新恢复这些数据的操作，用户可能会看到错误的ui界面，后者闪退。

通过使用ViewModel 可以轻松的解决上面的问题。只需要把曾经保存在Ui界面的数据，保存在viewModel 当中。设置变更时，viewModel 不会被销毁，自然而然 保存的数据也依然存在。这样做，不但帮助我们避免了数据丢失，也是更值得推荐的软件开发模式。

在以往的android 开发过程中，数据逻辑对象和变量常常被保存在activity 和fragment 里。

随着activity 代码 变得越来越冗长，维护这些类也就变得更加困难。这样做，为我们之后的软件开发以及维护布下了一个陷阱。

同时这一做法也违背了单一职责的涉及原则。通过使用viewModel 可以轻松的划分界限。让每个类各司其职。viewModel 负责管理界面数据。ui组件则显示数据和获取用户操作。

如果你的应用程序需要存储大量的数据，我们还推荐你创建一个Repository 类，作为唯一的数据层接口，具体内容参见架构指南。不过我们还应该注意，不要重蹈activity的覆辙。避免在viewModel 里，实现过多的职责。可以创建一个presenter的类，或者实现一个更饱满的clean architecture 架构。





注意：不要把context 传递给viewModel. 也就是说，viewModel 里不应该有activity,fragment,view 的引用。

viewModel 的存在范围不同于界面组件。

viewModel 存在时，对应的activity 可能会被销毁并重建很多次。假设在viewModel 里保存了activity 的引用，当用户旋转手机屏幕，activity 被销毁，你的viewModel 就引用了一个不复存在的activity 对象。也就是说 出现了内存泄漏的问题。如果需要一个context 对象，可以传入application 对象。



viewModel 不可以替代onSaveInstanceState().在配置变更时候，viewModel 会被保存，但是遇到资源紧缺的情况，你的应用程序会被系统关闭，viewModel 也会随之销毁，我们应该尽量把Ui的数据保存在ViewModel 里，这会帮助我们省去配置变更之后重载这些数据的麻烦。

另一方面 onSaveInstacneState 可以保存小量的数据， 当应用被强制关闭之后，可以使用它来恢复ui界面。

两个viewModel 的特点：

1，viewModel可以用来共享多个fragment 中的数据，fragment 不需要对彼此进行引用，也不需要依赖一个parentActivity 。使用viewModel 就可以分享数据了。

2，viewModel 可以和另一个生命周期组件共同使用。从而创建响应式的ui界面。viewModel 里可以保存LiveDat。LiveData可以自动通知观察者Observer,来完成activity和fragment 的更新，

```
onCreate(){
	var model =ViewModelProviders.of(this).get(MyViewModel.class);
	model.getUsers().observer(this,Observers{ users->
		//更新ui.    
	})
}
```

ViewModel处理业务逻辑，并在屏幕旋转，Activity被销毁重建的时候，依然存在。

ViewModel组件的源码相当简单，它的目的就是建立一个UI和数据的隔离带。ViewModel为Activity或Fragment提供数据访问接口，同时将UI层的指令传递给数据层。简单点说，ViewModel组件的源码就是围绕如何获取一个ViewModel来写的。ViewModelProviders.of获取ViwModelProvider，ViewModelProvider.get获取ViewModel。其中ViewModel的构造方式可以由ViewModelProviders.of的Factory参数自行定义。support包中的AppCompatActivity已经整合了LifeCycle和ViewModel组件。
