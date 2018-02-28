# Activity      
## Activity是什么        
   是一种可以包含用户界面的组件，主要用于和用户进行交互。一个应用程序中可以包含零个或者多个Activity。
## Intent               
* 1.显示启动 Intent it = new Intent(Context,Clazz);
* 2.隐式启动 Intent it = new Intent("action");/intent.setAction()/intent.addCategory();/intent.setData()        
## 活动的生命周期          
### 返回栈         
* 1.Android是使用任务栈来管理活动的，一个任务就是一组存放在栈里的活动的集合，这个栈也被称为返回栈。栈是一种先进后出的数据结构        
在默认情况下，每当我们启动一个新活动，它就会在返回栈中入栈，并且处于栈顶的位置。而每当我们按下Back键或者调用finish（）方法      
去销毁一个活动时，处于栈顶的活动会出栈，这时前一个入栈的活动就会处于栈顶。系统总会显示处于栈顶的活动给用户。