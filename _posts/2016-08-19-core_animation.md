---
layout: post
title: "Core_Animation.md"
description: ""
category: 
tags: [Animation]
---
{% include JB/setup %}

# 核心动画（Core Animation）


<!-- create time: 2014-10-20 13:26:35  -->
    fromwork：QuartzCore in iOS 2.0 and later
    
    使用它需要先添加QuartzCore.framework
    引入对应的框架<QuartzCore/QuartzCore.h>
    (IOS7.0之后，系统已经默认添加)
    
[图层的核心动画](#anchor0)

[常用的KeyPath](#anchor1)  

[UIView动画](#anchor2)    

[Block动画](#anchor3)

[UIImageView的帧动画](#anchor4)

[UIActivityIndicatorView](#anchor5)

[常用的KeyPath](#anchor6)

<h2 id ="anchor0">图层的核心动画</h2>   
## 图层的核心动画（CAAnimation）

    其4个实现类
    CABasicAnimation    -- 
                          |- CAPropertyAnimation --
    CAKeyframeAnimation --                         |
                                                   |
    CAAnimationGroup  ------------------------------- CAAnimation
                                                   |
    CATransition      -----------------------------
    
    
    
* 常用属性（通用属性）

    1. duration：动画的持续时间
    2. repeatCount：动画的重复次数
    3. repeatDuration：动画的重复时间
    5. fillMode：决定当前对象在非active时间段的行为.比如动画开始之前,动画结束之后
    6. beginTime：可以用来设置动画延迟执行时间，若想延迟2s，就设置为CACurrentMediaTime()+2，CACurrentMediaTime()为图层的当前时间
    
    以上来自CAMediaTiming协议的属性
    
    7. timingFunction：速度控制函数，控制动画运行的节奏
    8. delegate：动画代理
    4. removedOnCompletion：默认为YES，代表动画执行完毕后就从图层上移除，图形会恢复到动画执行前的状态。如果想让图层保持显示动画执行后的状态，那就设置为NO，不过还要设置fillMode为kCAFillModeForwards
    
    
<h3>CAPropertyAnimation</h3>

    CAAnimation的子类，也是个抽象类，要想创建动画对象，应该使用它的两个子类：
        CABasicAnimation和CAKeyframeAnimation



<h4 id ="anchor1">常用的keyPath</h4>
##### CATransform3D Key Paths
    transform.rotation.x      NSNumber object
    transform.rotation.y      NSNumber object
    transform.rotation.z      NSNumber object
    transform.rotation       NSNumber object
    transform.scale.x        NSNumber object
    transform.scale.y        NSNumber object      
    transform.scale.z        NSNumber object
    transform.scale            NSNumber object
    transform.translation.x    NSNumber object
    transform.translation.y     NSNumber object
    transform.translation.z    NSNumber object
    transform.translation/NSValue object containing an NSSize or CGSize data type

##### CGPoint Key Paths

    position.x
    position.y
    position   CGPoint 
        
##### CGRect Key Paths

    bounds.origin     CGPoint.
    bounds.origin.x
    bounds.origin.y
    bounds.size       CGSize.
    bounds.size.width
    bounds.size.height

### CABasicAnimation
    
   * 常用属性
     1. keyPath：通过指定CALayer的一个属性名称为keyPath(NSString类型)，并且对CALayer的这个属性的值进行修改，达到相应的动画效果。比如，指定@”position”为keyPath，就修改CALayer的position属性的值，以达到平移的动画效果
     2. fromValue：keyPath相应属性的初始值
     3. toValue：keyPath相应属性的结束值随着动画的进行，在长度为duration的持续时间内，keyPath相应属性的值从fromValue渐渐地变为toValue
     
#### ⚠注意事项

    如果fillMode=kCAFillModeForwards和removedOnComletion=NO，
    那么在动画执行完毕后，图层会保持显示动画执行后的状态。
    但在实质上，图层的属性值还是动画执行前的初始值，并没有真正被改变。
    比如，CALayer的position初始值为(0,0)，
    CABasicAnimation的fromValue为(10,10)，toValue为(100,100)，
    虽然动画执行完毕后图层保持在(100,100)这个位置，实质上图层的position还是为(0,0)   
     

### CAKeyframeAnimation

 * 属性解析：
     1. values：就是上述的NSArray对象。里面的元素称为”关键帧”(keyframe)。动画对象会在指定的时间(duration)内，依次显示values数组中的每一个关键帧
     2. path：可以设置一个CGPathRef\CGMutablePathRef,让层跟着路径移动。path只对CALayer的anchorPoint和position起作用。如果你设置了path，那么values将被忽略
     3. keyTimes：可以为对应的关键帧指定对应的时间点,其取值范围为0到1.0,keyTimes中的每一个时间值都对应values中的每一帧.当keyTimes没有设置的时候,各个关键帧的时间是平分的

> CABasicAnimation可看做是最多只有2个关键帧的CAKeyframeAnimation


### CAAnimationGroup

    CAAnimation的子类，可以保存一组动画对象，将CAAnimationGroup对象加入层后，组中所有动画对象可以同时并发运行
  
  * 属性解析：
    animations：用来保存一组动画对象的NSArray
    默认情况下，一组动画对象是同时运行的，也可以通过设置动画对象的beginTime属性来更改动画的开始时间
    
 * 注意，动画的时间，次数，应该由CAAnimationGroup对象进行设置。
 
 
 
### CATransition
    CAAnimation的子类，用于做转场动画，能够为层提供移出屏幕和移入屏幕的动画效果。iOS比Mac OS X的转场动画效果少一点

    UINavigationController就是通过CATransition实现了将控制器的视图推入屏幕的动画效果
    
  * 属性解析:
    1. type：动画过渡类型
    2. subtype：动画过渡方向
    3. startProgress：动画起点(在整体动画的百分比)
    4. endProgress：动画终点(在整体动画的百分比)
                                              

  * CATransition的使用
  
  objective-c:
  
        CATransition *anim = [CATransition animation];
        anim.type = @“cube”; // 动画过渡类型
        anim.subtype = kCATransitionFromTop; // 动画过渡方向
        anim.duration = 1; // 动画持续1s
        // 代理，动画执行完毕后会调用delegate的animationDidStop:finished:
        anim.delegate = self;

 
        /*******中间穿插改变layer属性的代码**********/   
        
        [layer addAnimation:anim forKey:nil];
        
        
<h2 id ="anchor2">UIView动画</h2> 


    UIKit直接将动画集成到UIView类中，当内部的一些属性发生改变时，UIView将为这些改变提供动画支持
    执行动画所需要的工作由UIView类自动完成，但仍要在希望执行动画时通知视图，
    为此需要将改变属性的代码放在[UIView beginAnimations:nil context:nil]
    和[UIView commitAnimations]之间
    
### 常见方法解析:

        + (void)setAnimationDelegate:(id)delegate
        //设置动画代理对象，当动画开始或者结束时会发消息给代理对象
        + (void)setAnimationWillStartSelector:(SEL)selector
        //当动画即将开始时，执行delegate对象的selector，
        //并且把beginAnimations:context:中传入的参数传进selector
        + (void)setAnimationDidStopSelector:(SEL)selector
        //当动画结束时，执行delegate对象的selector，
        //并且把beginAnimations:context:中传入的参数传进selector
        + (void)setAnimationDuration:(NSTimeInterval)duration
        //动画的持续时间，秒为单位
        + (void)setAnimationDelay:(NSTimeInterval)delay
        //动画延迟delay秒后再开始
        + (void)setAnimationStartDate:(NSDate *)startDate
        //动画的开始时间，默认为now
        + (void)setAnimationCurve:(UIViewAnimationCurve)curve
        //动画的节奏控制
        动画的节奏控制，跟CAAnimation的timingFunction属性类似
        typedef NS_ENUM(NSInteger, UIViewAnimationCurve) {
            UIViewAnimationCurveEaseInOut,    // slow at beginning and end
            UIViewAnimationCurveEaseIn,       // slow at beginning
            UIViewAnimationCurveEaseOut,     // slow at end
            UIViewAnimationCurveLinear
        };
        + (void)setAnimationRepeatCount:(float)repeatCount
        //动画的重复次数
        + (void)setAnimationRepeatAutoreverses:(BOOL)repeatAutoreverses
        //如果设置为YES,代表动画每次重复执行的效果会跟上一次相反      
 
        + (void)setAnimationTransition:(UIViewAnimationTransition)transition 
                forView:(UIView *)view cache:(BOOL)cache
         设置视图view的过渡效果, transition指定过渡类型, cache设置YES代表使用视图缓存，性能较好
         
         
         typedef NS_ENUM(NSInteger, UIViewAnimationTransition) {
            UIViewAnimationTransitionNone,
            UIViewAnimationTransitionFlipFromLeft,
            UIViewAnimationTransitionFlipFromRight,
            UIViewAnimationTransitionCurlUp,
            UIViewAnimationTransitionCurlDown,
        };
        
        
        
        
        
<h2 id ="anchor3">Block动画</h2>   


    + (void)animateWithDuration:(NSTimeInterval)duration delay:(NSTimeInterval)delay options:(UIViewAnimationOptions)options animations:(void (^)(void))animations completion:(void (^)(BOOL finished))completion
        参数:
        duration：动画的持续时间
        delay：动画延迟delay秒后开始
        options：动画的节奏控制
        animations：将改变视图属性的代码放在这个block中
        completion：动画结束后，会自动调用这个block
        
        动画的节奏控制枚举常量
        UIViewAnimationOptionCurveEaseInOut
        UIViewAnimationOptionCurveEaseIn               
        UIViewAnimationOptionCurveEaseOut
        UIViewAnimationOptionCurveLinear 
        
        
    + (void)transitionWithView:(UIView *)view duration:(NSTimeInterval)duration options:(UIViewAnimationOptions)options animations:(void (^)(void))animations completion:(void (^)(BOOL finished))completion
        参数解析:
        duration：动画的持续时间
        view：需要进行转场动画的视图
        options：转场动画的类型
        animations：将改变视图属性的代码放在这个block中
        completion：动画结束后，会自动调用这个block
        
        转场动画的类型
        UIViewAnimationOptionTransitionNone
        UIViewAnimationOptionTransitionFlipFromLeft 
        UIViewAnimationOptionTransitionFlipFromRight   
        UIViewAnimationOptionTransitionCurlUp 
        UIViewAnimationOptionTransitionCurlDown 
        UIViewAnimationOptionTransitionCrossDissolve 
        UIViewAnimationOptionTransitionFlipFromTop 
        UIViewAnimationOptionTransitionFlipFromBottom

    + (void)transitionFromView:(UIView *)fromView toView:(UIView *)toView duration:(NSTimeInterval)duration options:(UIViewAnimationOptions)options completion:(void (^)(BOOL finished))completion
        方法调用完毕后，相当于执行了下面两句代码：
        // 添加toView到父视图
        [fromView.superview addSubview:toView]; 
        // 把fromView从父视图中移除
        [fromView.superview removeFromSuperview];
        参数解析:
        duration：动画的持续时间
        options：转场动画的类型
        animations：将改变视图属性的代码放在这个block中
        completion：动画结束后，会自动调用这个block
        
<h2 id ="anchor4">UIImageView的帧动画</h2>       

        UIImageView可以让一系列的图片在特定的时间内按顺序显示
        相关属性解析:
        animationImages：要显示的图片(一个装着UIImage的NSArray)
        animationDuration：完整地显示一次animationImages中的所有图片所需的时间
        animationRepeatCount：动画的执行次数(默认为0，代表无限循环)
        相关方法解析:
        - (void)startAnimating; 开始动画
        - (void)stopAnimating;  停止动画
        - (BOOL)isAnimating;  是否正在运行动画   
        
<h2 id ="anchor5">UIActivityIndicatorView</h2>       

        是一个旋转进度轮，可以用来告知用户有一个操作正在进行中，一般用initWithActivityIndicatorStyle初始化
        方法解析:
        - (void)startAnimating; 开始动画
        - (void)stopAnimating;  停止动画
        - (BOOL)isAnimating;  是否正在运行动画
        UIActivityIndicatorViewStyle有3个值可供选择：
        UIActivityIndicatorViewStyleWhiteLarge   //大型白色指示器    
        UIActivityIndicatorViewStyleWhite      //标准尺寸白色指示器    
        UIActivityIndicatorViewStyleGray    //灰色指示器，用于白色背景