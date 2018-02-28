# 自定义View       
## Android LayoutInflater原理分析       
*  LayoutInflater初始化        
    LayoutInflater layoutInflater = LayoutInflater.from(Context);   实际是Context.getSystemService（        
    Context.LAYOUT_INFLATER_SERVICE);           
    layoutInflater.inflate(resourceId,root，attachToRoot);
    
    1. resourceId :布局文件的id
    2. root 给该view的外层再包一层父布局，如果不需要传入null
    3. 是否绑定到父布局     
    
    a. 如果root为null，attachToRoot将失去作用，设置与否没有意义。      
    b. 如果root不为null，attachToRoot设置成true，则会给加载的布局文件指定一个父布局，即root。
    c. 如果root不为null，attachToRoot设置成false，则会将布局文件最外层的所有layout属性进行设置，当该View被添加到父      
    布局中，这些Layout的将会生效。          
    d. 如果root不为空，attachToRoot不设置，attachToRoot的默认值为true。       
      
**注意:** 如果给一个View设置宽高时，只有它的父View存在时，才生效。 

## View的绘制      

*   onMeasure()     
    用于测量View的大小。View的绘制从ViewRoot的performTraversals（）方法中开始，在其内部调用View的measure（）方法。       
    measure（）方法接收两个参数，widthMeasureSpec和heightMeasureSpec，这两个参数分别确定视图的宽度和高度的规格大小。        
    MeasureSpec的值specSize和specMode共同组成，其中specSize记录的是大小，specMode记录的是规格，specMode一共有三种        
    类型：     
    1. EXACTLY      
    表示父视图希望子视图的大小应该是有specSize的值来决定，默认系统会按照这个规则来设置子视图的大小，开发人员当然也可以按照     
    自己的意愿设置成任意大小。       
    2. AT_MOST      
    表示子视图最多只能是specSize中制定的大小，开发人员应该尽可能小的去设置这个视图，并且保证不会超出specSize。系统默认会      
    按照这个规则来设置子视图，开发人员可以按照自己的意愿去设置大小。        
    3. UNSPECIFIED      
    表示开发人员可以将视图按照自己的意愿设置成任意的大小，没有任何限制。
    
*   onLayout()      
    用于给视图进行布局，确定视图的位置。ViewRoot 的performTraversals（）方法会在调用View的measure方法结束之后调用View       
    的layout（）方法。
    layout（left，top，right，bottom）；
    
*   onDraw()        
    对视图进行绘制
    