## Accessibility Service

*   继承关系：
    ```
    public abstract class AccessibilityService extends Service
    java.lang.Object
       ↳ 	android.content.Context
      	   ↳ 	android.content.ContextWrapper
      	  	   ↳ 	android.app.Service
      	  	  	   ↳ 	android.accessibilityservice.AccessibilityService
    ```

*   功能描述：

    Accessibility services主要帮助残疾人士更好的使用Android设备或者Android
    App。运行在后台和接收AccessibilityEvent被触发时的系统回调，这些事件
    主要是一些用户界面的状态转变，包括：焦点的改变、button的点击 等等，同时
    能够很容易的获取当前激活窗口的内容。

*   生命周期:

    Accessibility Service由于继承自Service，所以遵从系统service的生命周期，
    Accessibility Service的开启，需要用户去设置中心给App开启此开关。当服务
    被绑定之后，onServiceConnected()会被触发，用户可以重写onServiceConnected()
    该函数。当设置中心的开关关闭的时候 disableSelf()会被调用。

*   声明：

    和其他service一样，Accessibility Service同样需要在AndroidManifest.xml
    中声明，此外它有两个设置是必须要配置的：
    1. android:permission="android.permission.BIND_ACCESSIBILITY_SERVICE"
    2. action android:name="android.accessibilityservice.AccessibilityService"

    如果不设置这两个选项，系统将会忽略Accessibility Service,声明示例：
    ```
    <service android:name=".MyAccessibilityService"
             android:permission="android.permission.BIND_ACCESSIBILITY_SERVICE">
         <intent-filter>
             <action android:name="android.accessibilityservice.AccessibilityService" />
         </intent-filter>
         . . .
     </service>

     ```
*   配置：
    Accessibility Service能够配置在一定时间内 监听哪些事件、应用包名、窗口变化
    meta-data 必须配置两个参数，配置示例：
    ```
    <service android:name=".MyAccessibilityService">
         <intent-filter>
             <action android:name="android.accessibilityservice.AccessibilityService" />
         </intent-filter>
         <meta-data android:name="android.accessibilityservice" android:resource="@xml/accessibilityservice" />
     </service>
     ```

    ```
    <accessibility-service
         android:accessibilityEventTypes="typeViewClicked|typeViewFocused"
         android:packageNames="foo.bar, foo.baz"
         android:accessibilityFeedbackType="feedbackGeneric"
         android:notificationTimeout="100"
         android:canRetrieveWindowContent="true"
     />
     ```
     **字段说明：**

     **accessibilityEventTypes**

     监听事件类型，例如：typeViewClicked view被点
     击，typeViewFocused view获取到焦点，如果监听所有事件：typeAllMask

     **packageNames**

     监听应用所对应的包名，不设置默认所有应用，如果多个应用可以用 ，分割

     **accessibilityFeedbackType**

     监听反馈方式,比如是语音播放,还是震动。feedbackGeneric 通用的反馈

    **notificationTimeout**

     响应时间的设置

     **canRetrieveWindowContent**

     表示该服务能否访问活动窗口中的内容.也就是如果你希望在服务中获取窗体
     内容的化,则需要设置其值为true.

     **其他更多详尽参见官方文档：**
      []() https://developer.android.com/reference/android/accessibilityservice/AccessibilityServiceInfo

*   常用函数

    | 方法        | 描述     |
    | --------   | -----   |
    |disableSelf()| 禁用当前服务,也就是在服务可以通过该方法停止运行|
    |findFoucs(int falg)|查找拥有特定焦点类型的控件|
    |getRootInActiveWindow()|如果配置能够获取窗口内容,则会返回当前活动窗口的根结点|
    |getSeviceInfo()|获取当前服务的配置信息|
    |onAccessibilityEvent(AccessibilityEvent event)|有关AccessibilityEvent事件的回调函数.系统通过sendAccessibiliyEvent()不断的发送AccessibilityEvent到此处|
    |performGlobalAction(int action)|执行全局操作,比如返回,回到主页,打开最近等操作|
    |setServiceInfo(AccessibilityServiceInfo info)| 	设置当前服务的配置信息|
    |getSystemService(String name)|获取系统服务|
    |onKeyEvent(KeyEvent event)|如果允许服务监听按键操作,该方法是按键事件的回调,需要注意,这个过程发生了系统处理按键事件之前|
    |onServiceConnected()|系统成功绑定该服务时被触发,也就是当你在设置中开启相应的服务,系统成功的绑定了该服务时会触发,通常我们可以在这里做一些初始化操作|

    AccessibilityEvent常用函数:

    | 方法        | 描述    |
    | ---------  | -----  |
    |getEventType()|事件类型|
    |getSource()|获取事件源对应的结点信息|
    |getClassName()|获取事件源对应类的类型,比如点击事件是有某个Button产生的,那么此时获取的就是Button的完整类名|
    |getText()|获取事件源的文本信息,比如事件是有TextView发出的,此时获取的就是TextView的text属性.如果该事件源是树结构,那么此时获取的是这个树上所有具有text属性的值的集合|
    |isEnabled()|事件源(对应的界面控件)是否处在可用状态|
    |getItemCount()|如果事件源是树结构,将返回该树根节点下子节点的数量|

    __注意:__ 系统不断的产生各种事件,有些是界面控件产生的,有些是系统产生的.
    对于由界面控件的产生的事件,通常我们将该控件称之为事件源.并不是所有
    的事件都能通过getSource()方法获取到事件源,比如像通知消息类型的事件
    (TYPE_NOTIFICATION_STATE_CHANGED).

    AccessibilityNodeInfo常用函数:

    | 方法        | 描述    |
    | --------    | -----   |
    |findAccessibilityNodeInfosByViewId(String str)|通过View的id查找对应的控件|
    |findAccessibilityNodeInfosByText(String str)|通过View的text或者description查找对应的控件|