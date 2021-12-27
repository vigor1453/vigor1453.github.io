---
layout: post
title: "第一次gamejam学到的"
date: 2019-10-07
tags: ["unity"]
comments: true
author: oneman233
excerpt: "有关Unity"
---

* unity基本组成元素是sprite
    
    添加图片的时候已经默认添加了sprite
    
    可以直接在屏幕右侧添加组件

* rigid body是刚体组件，可以在gravity scale里按需要调整重力大小
    
    防止在z轴上的旋转可以在constraints里勾选freeze rotation

* Input类的GetAxis方法可以获取当前位置，传入参数是字符串，分别有"Horizontal"和"Vertical"
    
    调用Vector2类的第一个和第二个元素可以使用.x和.y

* 三种update方式的区别：
  * MonoBehaviour.Update 更新：当MonoBehaviour启用时，其Update在每一帧被调用。
  * MonoBehaviour.FixedUpdate 固定更新：当MonoBehaviour启用时，其 FixedUpdate在每一帧被调用。
    * 处理Rigidbody时，需要用FixedUpdate代替Update。
    * 例如:给刚体加一个作用力时，你必须把作用力应用在FixedUpdate里的固定帧，而不是Update中的帧。(两者帧长不同)
  * MonoBehaviour.LateUpdate 晚于更新：当Behaviour启用时，其LateUpdate在每一帧被调用。
    * LateUpdate是在所有Update函数调用后被调用，可用于调整脚本执行顺序。
    * 例如:当物体在Update里移动时，跟随物体的相机可以在LateUpdate里实现。

* 新版unity不支持rigidbody来调用自己，必须要使用GetComponent()，并且必须写在方法内部。

* 实现附加只需要把project中的c#脚本拖动到hierarchy中的图片素材上即可，否则默认是移动主摄像机。

* 一定要两个刚体才可以讨论碰撞，不可移动的刚体可以认为质量无限大，使用polygon（多边形）碰撞比edge（边缘）碰撞要精确。

* 关于获得键盘事件可以参考：
    ```c++
    void Speed_Control()
    {
        if (Input.GetKeyDown(check_space))
        {
            speed = new Vector2(30, 30);
        }
        else if (Input.GetKeyUp(check_space))
        {
            speed = new Vector2(80, 80);
        }
    }
    ```

* 勾选"IsTrigger"属性表示该碰撞体用于触发事件，并将被物理引擎所忽略。
    
    意味着，子弹将穿过触碰到的对象，而不会阻碍对象的移动，触碰的时候将会引发"OnTriggerEnter2D"事件。

* Edit->Project Settings->Editor，将Default Behavior Mode设置成2D模式，这样拖入工程的图片会自动变换为Sprite类型。

* bullet的trigger检测代码：
    ```c++
    private void OnTriggerEnter2D(Collider2D collision)
    {
        enemy e = collision.gameObject.GetComponent();
        if (e == null)
            return;
        if (e.get_hp() <= damage)//敌人血量不足
        {
            Destroy(e.gameObject);//消灭敌人
        }
        else
        {
            e.set_hp(damage);
        }
        Destroy(gameObject);//碰撞后消除子弹
    }
    ```

* enemy的trigger检测代码：
    ```c++
    private void OnTriggerEnter2D(Collider2D collision)
    {
        player p = collision.gameObject.GetComponent();
        if (p == null)
            return;
        Destroy(p.gameObject);//摧毁玩家
        Destroy(gameObject);//摧毁敌人
    }
    ```

* 永远记得消除gameobject，否则一直消除的是脚本。

* 2D转3D有可能导致物体消失。取决于摄像机位置。

* 建立prefab只需要把hierarchy里面的物体拖动回来即可。此时prefab默认带好了图片，创建方式如下：
    
    `Instantiate(Resources.Load("Prefab/bullet"));`

    重载的时候可以带上初始位置和旋转角度:

    `Instantiate(Resources.Load("Prefab/bullet"), player_pos, rotation);`

    玩家位置需要实时获取，旋转角度默认无参就可以。

* 获取玩家位置需要使用transform，如下：
    
    `Vector3 player_pos = GameObject.Find("player").transform.position;`

* 控制上下左右移动：
    `transform.localPosition = Vector3.MoveTowards(transform.localPosition, transform.localPosition + dif, 0.5f*Time.time);`

    最后一个参数是最大移动距离。

* 获取指定gameObject的方式
  * `GameObject.Find("名字")`
  * `GameObject.FindGameObjectWithTag(tag)`
  * `gameObject.transform.Find("名字")`

* getkey才可以实现键盘按下后一直移动。

* 构建场景时可以使用Ctrl+Shift+B 然后add current 然后Ctrl+S。
    一定要把所有场景加入工程才可以运作。

* 把脚本绑定到画布上，并且在画布的inspector中设置各个按钮变量对应的是哪个按钮。
    
    此外还需要加入Event System！！！！！

    按钮需要box collider，尺寸需要手动修改。

    按钮还需要在onclick当中设置调用哪个函数，同时左下角是绑定到canva上面使用的，此时可以直接调用canvas上挂载的脚本的public函数。

* 可以改变canvas的渲染方式为摄像机渲染并选中当前场景的摄像机，这样就能在场景出现时才制作canvas。

* 可以调整canvas的scaler的UI scale Mode使得其大小由屏幕决定。

----

2020.7.11更新：

[视角控制和camera移动](https://blog.csdn.net/whyistao/article/details/51731241)

[鼠标控制视角](http://www.manongjc.com/detail/6-mnlokikvzpykskl.html)

[绘制用户准星](https://gameinstitute.qq.com/community/detail/111910)

[鼠标固定在屏幕中央](https://zhidao.baidu.com/question/1755376541210902548.html)

[collider和trigger](https://www.jianshu.com/p/f99463f0578d)

[Highlight System 5.0使用](https://www.jianshu.com/p/d7568c2e2151)

[Unity开始结束页面](https://blog.csdn.net/zxm_jimin/article/details/90300945)

[Unity UI的button添加onclick事件](https://blog.csdn.net/huhbca/article/details/90731817?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.compare&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.compare)

[切换camera](https://blog.csdn.net/liujunjie612/article/details/45847877)

[鼠标的锁定和隐藏](https://jingyan.baidu.com/article/b2c186c80cc8b0c46ef6ff80.html)

[将图片转换为Sprite](https://blog.csdn.net/Jeffxu_lib/article/details/100140791)

[利用鼠标滚轮调整视野大小](https://blog.csdn.net/yang_sheng_21/article/details/78805430)

[Unity Destroy方法](https://www.cnblogs.com/fws94/p/11416789.html)

[Unity Text换行问题](https://blog.csdn.net/zjw1349547081/article/details/53390609)

[Unity Text显示时间](https://baijiahao.baidu.com/s?id=1602897711149294950&wfr=spider&for=pc)

(Text文字模糊可以使用overflow，让文字大小不受Text大小影响)

[鼠标点击选中物体，再点击放下物体](https://blog.csdn.net/qq_34406755/article/details/103664311)

[prefab无法移动](https://blog.csdn.net/shenmifangke/article/details/70239776)

[各种键盘事件](https://blog.csdn.net/cbbbc/article/details/51251279)

[Unity旋转物体](https://blog.csdn.net/weixin_44370124/article/details/90368859)

[C#创建数组](https://www.cnblogs.com/Sandon/p/5421506.html)

[更改Button的Text](https://www.jianshu.com/p/dabed0093422)

[隐藏Button（隐藏Button下的物体即可）](https://blog.csdn.net/maoliran/article/details/64440291)

[隐藏物体](https://www.cnblogs.com/vuciao/p/10604265.html)

[动态添加Button的OnClick事件](https://blog.csdn.net/yzx5452830/article/details/77259445)

[C#字符串求子串](https://zhidao.baidu.com/question/146062818.html)

[Unity中Playerprefs使用（本地持久化类）](https://www.cnblogs.com/planezhong/p/10061977.html)