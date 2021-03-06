# CircularAnim

首先来看一个UI动效图。
![动效来自Dribbble](https://d13yacurqjgara.cloudfront.net/users/62319/screenshots/1945593/shot.gif)
效果图是是Dribbble上看到的，[原作品在此。](https://dribbble.com/shots/1945593-Login-Home-Screen)

我所实现的效果如下([视频在此](http://v.youku.com/v_show/id_XMTY1MjMxMTk3Ng==.html?x&sharefrom=android))：
![CircularAnim.png](http://upload-images.jianshu.io/upload_images/2066824-05a997acd73a17f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/360/h640)
视频传到优酷了，2M左右，[点这里观看。](http://v.youku.com/v_show/id_XMTY1MjMxMTk3Ng==.html?x&sharefrom=android)

### 使用方法
为了使用起来简单，我将动画封装成CircularAnimUtil.

现在，让按钮隐藏只需一行代码，如下：
> CircularAnimUtil.hideAsCircular(mChangeBtn);

同理，让按钮伸展开：
> CircularAnimUtil.showAsCircular(mChangeBtn);

水波般铺满指定颜色并启动一个Activity:
> CircularAnimUtil.startActivityAsCircular(MainActivity.this, EmptyActivity.class, view, R.color.colorPrimary);

这里，你还可以放图片：
> CircularAnimUtil.startActivityAsCircular(MainActivity.this, EmptyActivity.class, view, R.mipmap.img_huoer_black);

也许在显示或隐藏视图时，你想要设置半径和时长，你可以调用这个方法：
> 显示：showAsCircular(View myView, float startRadius, long durationMills)
隐藏：hideAsCircular(final View myView, float endRadius, long durationMills) 

以及，你可以在startActivity时带上Intent:
startActivityAsCircular(Activity thisActivity, Intent intent, View triggerView, int colorOrImageRes)

还可以startActivityForResult:
> startActivityForResultAsCircular(Activity thisActivity, Intent intent, Integer requestCode, View triggerView, int colorOrImageRes)

同理，startActivity同样可以设置时长。

用起来非常的方便，一切逻辑性的东西都由帮助类搞定。

### 源码
下面贡献源码。你可以直接新建一个CircularAnimUtil的类，然后把下面的代码复制进去就OK了。
另外，[GitHub Demo 地址在此](https://github.com/XunMengWinter/CircularAnim)，欢迎Star,欢迎喜欢，欢迎关注，哈哈哈 ^ ^ ~

```
 import android.animation.Animator;
 import android.animation.AnimatorListenerAdapter;
 import android.annotation.SuppressLint;
 import android.app.Activity; 
import android.content.Intent; 
import android.os.Bundle; 
import android.view.View;
 import android.view.ViewAnimationUtils;
 import android.view.ViewGroup;
 import android.widget.ImageView;  

/**  * 对 ViewAnimationUtils.createCircularReveal() 方法的封装. 
 * <p/>
  * Created on 16/7/20. 
 * GitHub: https://github.com/XunMengWinter 
 * 
 * @author ice
  */
 public class CircularAnimUtil {

      public static final long PERFECT_MILLS = 618;
     public static final int MINI_RADIUS = 0;
  
    /**
      * 向四周伸张，直到完成显示。
      */
     @SuppressLint("NewApi")
     public static void showAsCircular(View myView, float startRadius, long durationMills) {
         if (android.os.Build.VERSION.SDK_INT < android.os.Build.VERSION_CODES.LOLLIPOP) {
             myView.setVisibility(View.VISIBLE); 
            return;
         }  
        int cx = (myView.getLeft() + myView.getRight()) / 2;
         int cy = (myView.getTop() + myView.getBottom()) / 2;  
        int w = myView.getWidth(); 
        int h = myView.getHeight();  
        // 勾股定理 & 进一法
         int finalRadius = (int) Math.sqrt(w * w + h * h) + 1;
          Animator anim = 
                ViewAnimationUtils.createCircularReveal(myView, cx, cy, startRadius, finalRadius); 
        myView.setVisibility(View.VISIBLE); 
        anim.setDuration(durationMills);
         anim.start(); 
    }  

    /**
      * 由满向中间收缩，直到隐藏。
      */ 
    @SuppressLint("NewApi") 
    public static void hideAsCircular(final View myView, float endRadius, long durationMills) { 
        if (android.os.Build.VERSION.SDK_INT < android.os.Build.VERSION_CODES.LOLLIPOP) { 
            myView.setVisibility(View.INVISIBLE); 
            return;
         }  
        int cx = (myView.getLeft() + myView.getRight()) / 2; 
        int cy = (myView.getTop() + myView.getBottom()) / 2; 
        int w = myView.getWidth(); 
        int h = myView.getHeight();  
        // 勾股定理 & 进一法
         int initialRadius = (int) Math.sqrt(w * w + h * h) + 1;  
        Animator aim =
                 ViewAnimationUtils.createCircularReveal(myView, cx, cy, initialRadius, endRadius); 
        anim.setDuration(durationMills);
         anim.addListener(new AnimatorListenerAdapter() { 
            @Override
             public void onAnimationEnd(Animator animation) {
                 super.onAnimationEnd(animation);
                 myView.setVisibility(View.INVISIBLE);
             }
         });  
        anim.start(); 
    }  

    /**
      * 从指定View开始向四周伸张(伸张颜色或图片为colorOrImageRes), 然后进入另一个Activity, 
     * 返回至 @thisActivity 后显示收缩动画。
      */
     @SuppressLint("NewApi") 
    public static void startActivityForResultAsCircular( 
            final Activity thisActivity, final Intent intent, final Integer requestCode, final Bundle bundle, 
            final View triggerView, int colorOrImageRes, final long durationMills) {  
        if (android.os.Build.VERSION.SDK_INT < android.os.Build.VERSION_CODES.LOLLIPOP) { 
            thisActivity.startActivity(intent); 
            return;
         }
  
        int[] location = new int[2];
         triggerView.getLocationInWindow(location); 
        final int cx = location[0] + triggerView.getWidth() / 2;
         final int cy = location[1] + triggerView.getHeight() / 2;
         final ImageView view = new ImageView(thisActivity); 
        view.setScaleType(ImageView.ScaleType.CENTER_CROP); 
        view.setImageResource(colorOrImageRes);
 
        final ViewGroup decorView = (ViewGroup) thisActivity.getWindow().getDecorView(); 
        int w = decorView.getWidth();
         int h = decorView.getHeight();
         decorView.addView(view, w, h);
         final int finalRadius = (int) Math.sqrt(w * w + h * h) + 1; 

        Animator aim = ViewAnimationUtils.createCircularReveal(view, cx, cy, 0, finalRadius); 
        anim.setDuration(durationMills); 
        anim.addListener(new AnimatorListenerAdapter() {
             @Override 
            public void onAnimationEnd(Animator animation) {
                 super.onAnimationEnd(animation);  

                if (requestCode == null) 
                    thisActivity.startActivity(intent); 
                else if (bundle == null) 
                    thisActivity.startActivityForResult(intent, requestCode); 
                else 
                    thisActivity.startActivityForResult(intent, requestCode, bundle);  

                // 默认渐隐过渡动画.
                 thisActivity.overridePendingTransition(android.R.anim.fade_in, android.R.anim.fade_out);  
                // 默认显示返回至当前Activity的动画. 
                triggerView.postDelayed(new Runnable() { 
                    @Override
                     public void run() { 
                        Animator aim =
                                 ViewAnimationUtils.createCircularReveal(view, cx, cy, finalRadius, 0);
                         anim.setDuration(durationMills);
                         anim.addListener(new AnimatorListenerAdapter() { 
                            @Override
                             public void onAnimationEnd(Animator animation) {
                                 super.onAnimationEnd(animation);
                                 try { 
                                    decorView.removeView(view);
                                 } catch (Exception e) { 
                                    e.printStackTrace();
                                 } 
                            }
                         });
                         anim.start();
                     } 
                }, 1000);  
            } 
        });
         anim.start();
     }   


    /*下面的方法全是重载，用简化上面方法的构建*/


       public static void startActivityForResultAsCircular( 
            Activity thisActivity, Intent intent, Integer requestCode, View triggerView, int colorOrImageRes) { 
        startActivityForResultAsCircular(thisActivity, intent, requestCode, null, triggerView, colorOrImageRes, PERFECT_MILLS);
     }

      public static void startActivityAsCircular( 
            Activity thisActivity, Intent intent, View triggerView, int colorOrImageRes, long durationMills) {
         startActivityForResultAsCircular(thisActivity, intent, null, null, triggerView, colorOrImageRes, durationMills);
     }  

    public static void startActivityAsCircular(
             Activity thisActivity, Intent intent, View triggerView, int colorOrImageRes) { 
        startActivityAsCircular(thisActivity, intent, triggerView, colorOrImageRes, PERFECT_MILLS);
     }  

    public static void startActivityAsCircular(Activity thisActivity, Class<?> targetClass, View triggerView, int colorOrImageRes) { 
        startActivityAsCircular(thisActivity, new Intent(thisActivity, targetClass), triggerView, colorOrImageRes, PERFECT_MILLS);
     }  

    public static void showAsCircular(View myView) { 
        showAsCircular(myView, MINI_RADIUS, PERFECT_MILLS); 
    }  

    public static void hideAsCircular(View myView) { 
        hideAsCircular(myView, MINI_RADIUS, PERFECT_MILLS); 
    }

  }
```

### 后记
另外，有木有手机版或者Mac版好用的Gif转换器推荐，表示好难找。
And有没有傻瓜式发布项目到JCenter的教程推荐？看过几篇都不管用。囧 ~ 
