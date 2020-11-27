MVC（Model-View-Controller）：分离了View和Model的职责，但是View和Model还可以直接通信。View接受用户操作，交给Controller来处理，但是具体的处理逻辑是由Model来实现，Model处理完通知View来刷新界面。所以MVC模式中的M和V有直接关联的关系



MVP（Model-View-Presenter）：为了解决MVC中M和V的关联问题，MVP模式就出现了，View接受用户操作，交给Presenter来处理，但是具体的处理逻辑是由Model来实现，Model处理完利用接口回调的方式把接口回调到Presenter，然后Presenter将结果回调到View上刷新界面。



MVVM（Model-View-ViewModel）：