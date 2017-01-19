# Android试图弹出动画：仿微博弹出icon

#### 场景
因为公司项目要做一个类似微博发布的弹出动画效果，而且个人也觉得这个效果很炫酷，所以在这里分享一下我的成果（O(∩_∩)O哈哈~）

废话不多多,先看看效果图

![image](https://raw.githubusercontent.com/hczcgq/-PopUpAnimation/master/device-2017-01-11-191910.gif)


#### 实现过程

实验这个效果就只有一个页面，具体layout文件如下：
```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/contentView"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_centerHorizontal="true"
    android:alpha="0.9"
    android:background="@mipmap/bg_dialog_publish"
    android:gravity="bottom|center_horizontal">

    <LinearLayout
        android:id="@+id/ll_close"
        android:layout_width="match_parent"
        android:layout_height="48dp"
        android:layout_alignParentBottom="true"
        android:background="@android:color/white"
        android:gravity="center">

        <ImageView
            android:layout_width="30dp"
            android:layout_height="30dp"
            android:layout_gravity="center"
            android:padding="8dp"
            android:src="@mipmap/ic_publish_delete" />
    </LinearLayout>


    <LinearLayout
        android:id="@+id/ll_link_des"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_above="@+id/ll_close"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="20dp"
        android:gravity="center">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="发布链接支持解析微信文章、B站、秒拍等链接"
            android:textColor="#7f7f7f"
            android:textSize="12sp" />
        <ImageView
            android:layout_width="12dp"
            android:layout_height="12dp"
            android:layout_gravity="center"
            android:layout_marginLeft="3dp"
            android:src="@mipmap/ic_why_tag_blue" />
    </LinearLayout>

    <LinearLayout
        android:id="@+id/weblink_window"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_above="@+id/ll_close"
        android:layout_alignLeft="@+id/video_window"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="80dp"
        android:layout_marginTop="40dp"
        android:gravity="center"
        android:orientation="vertical">

        <ImageView
            android:layout_width="60dp"
            android:layout_height="60dp"
            android:src="@mipmap/ic_publish_weblink" />

        <TextView
            android:textSize="15sp"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="10dp"
            android:text="链接"
            android:textColor="#606060" />
    </LinearLayout>

    <LinearLayout
        android:id="@+id/text_window"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_above="@+id/ll_close"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="80dp"
        android:layout_marginTop="40dp"
        android:gravity="center"
        android:orientation="vertical">

        <ImageView
            android:layout_width="60dp"
            android:layout_height="60dp"
            android:src="@mipmap/ic_publish_text" />

        <TextView
            android:textSize="15sp"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="10dp"
            android:text="文字"
            android:textColor="#606060" />
    </LinearLayout>


    <LinearLayout
        android:id="@+id/photo_window"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_above="@+id/weblink_window"
        android:layout_centerHorizontal="true"
        android:layout_marginLeft="40dp"
        android:layout_marginRight="40dp"
        android:gravity="center"
        android:orientation="vertical">

        <ImageView
            android:layout_width="60dp"
            android:layout_height="60dp"
            android:src="@mipmap/ic_publish_photo" />

        <TextView
            android:textSize="15sp"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="10dp"
            android:text="图片"
            android:textColor="#606060" />
    </LinearLayout>

    <LinearLayout
        android:id="@+id/video_window"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_above="@+id/weblink_window"
        android:layout_centerHorizontal="true"
        android:layout_toLeftOf="@+id/photo_window"
        android:gravity="center"
        android:orientation="vertical">

        <ImageView
            android:layout_width="60dp"
            android:layout_height="60dp"
            android:src="@mipmap/ic_publish_video" />

        <TextView
            android:textSize="15sp"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="10dp"
            android:text="视频"
            android:textColor="#606060" />
    </LinearLayout>

    <LinearLayout
        android:id="@+id/voice_window"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_above="@+id/weblink_window"
        android:layout_centerHorizontal="true"
        android:layout_toRightOf="@+id/photo_window"
        android:gravity="center"
        android:orientation="vertical">

        <ImageView
            android:layout_width="60dp"
            android:layout_height="60dp"
            android:src="@mipmap/ic_publish_voice" />

        <TextView
            android:textSize="15sp"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="10dp"
            android:text="语音"
            android:textColor="#606060" />
    </LinearLayout>

</RelativeLayout>
```
layout布局代码没什么好解释的，下面具体看看动画实现的效果部分：
```
public class PublishPopWindow extends PopupWindow implements View.OnClickListener {

    private View rootView;
    private RelativeLayout contentView;
    private Activity mContext;

    public PublishPopWindow(Activity context) {
        this.mContext = context;
    }

    public void showMoreWindow(View anchor) {
        LayoutInflater inflater = (LayoutInflater) mContext
                .getSystemService(Context.LAYOUT_INFLATER_SERVICE);
        rootView = inflater.inflate(R.layout.dialog_publish, null);
        int h = mContext.getWindowManager().getDefaultDisplay().getHeight();
        int w = mContext.getWindowManager().getDefaultDisplay().getWidth();
        setContentView(rootView);
        this.setWidth(w);
        this.setHeight(h - ScreenUtils.getStatusHeight(mContext));

        contentView = (RelativeLayout) rootView.findViewById(R.id.contentView);
        LinearLayout close = (LinearLayout) rootView.findViewById(R.id.ll_close);
        close.setBackgroundColor(0xFFFFFFFF);
        close.setOnClickListener(this);
        showAnimation(contentView);
        setBackgroundDrawable(mContext.getResources().getDrawable(R.drawable.translucence_with_white));
        setOutsideTouchable(true);
        setFocusable(true);
        showAtLocation(anchor, Gravity.BOTTOM, 0, 0);
    }

    /**
     * 显示进入动画效果
     * @param layout
     */
    private void showAnimation(ViewGroup layout) {
        //遍历根试图下的一级子试图
        for (int i = 0; i < layout.getChildCount(); i++) {
            final View child = layout.getChildAt(i);
            //忽略关闭组件
            if (child.getId() == R.id.ll_close) {
                continue;
            }
            //设置所有一级子试图的点击事件
            child.setOnClickListener(this);
            child.setVisibility(View.INVISIBLE);
            //延迟显示每个子试图(主要动画就体现在这里)
            Observable.timer(i * 50, TimeUnit.MILLISECONDS)
                    .subscribeOn(Schedulers.newThread())
                    .observeOn(AndroidSchedulers.mainThread())
                    .subscribe(new Action1<Long>() {
                        @Override
                        public void call(Long aLong) {
                            child.setVisibility(View.VISIBLE);
                            ValueAnimator fadeAnim = ObjectAnimator.ofFloat(child, "translationY", 600, 0);
                            fadeAnim.setDuration(300);
                            KickBackAnimator kickAnimator = new KickBackAnimator();
                            kickAnimator.setDuration(150);
                            fadeAnim.setEvaluator(kickAnimator);
                            fadeAnim.start();
                        }
                    });
        }

    }

    /**
     * 关闭动画效果
     * @param layout
     */
    private void closeAnimation(ViewGroup layout) {
        for (int i = 0; i < layout.getChildCount(); i++) {
            final View child = layout.getChildAt(i);
            if (child.getId() == R.id.ll_close) {
                continue;
            }
            Observable.timer((layout.getChildCount() - i - 1) * 30, TimeUnit.MILLISECONDS)
                    .subscribeOn(Schedulers.newThread())
                    .observeOn(AndroidSchedulers.mainThread())
                    .subscribe(new Action1<Long>() {
                        @Override
                        public void call(Long aLong) {
                            child.setVisibility(View.VISIBLE);
                            ValueAnimator fadeAnim = ObjectAnimator.ofFloat(child, "translationY", 0, 600);
                            fadeAnim.setDuration(200);
                            KickBackAnimator kickAnimator = new KickBackAnimator();
                            kickAnimator.setDuration(100);
                            fadeAnim.setEvaluator(kickAnimator);
                            fadeAnim.start();
                            fadeAnim.addListener(new Animator.AnimatorListener() {

                                @Override
                                public void onAnimationStart(Animator animation) {
                                }

                                @Override
                                public void onAnimationRepeat(Animator animation) {
                                }

                                @Override
                                public void onAnimationEnd(Animator animation) {
                                    child.setVisibility(View.INVISIBLE);
                                }

                                @Override
                                public void onAnimationCancel(Animator animation) {
                                }
                            });
                        }
                    });


            if (child.getId() == R.id.video_window) {
                Observable.timer((layout.getChildCount() - i) * 30 + 80, TimeUnit.MILLISECONDS)
                        .subscribeOn(Schedulers.newThread())
                        .observeOn(AndroidSchedulers.mainThread())
                        .subscribe(new Action1<Long>() {
                            @Override
                            public void call(Long aLong) {
                                dismiss();
                            }
                        });
            }
        }

    }

    @Override
    public void onClick(View v) {
        switch (v.getId()) {
            case R.id.video_window:
            case R.id.photo_window:
            case R.id.voice_window:
            case R.id.weblink_window:
            case R.id.text_window:
            case R.id.ll_link_des:
                goCreate();
                break;
            case R.id.ll_close:
                if (isShowing()) {
                    closeAnimation(contentView);
                }
                break;
            default:
                break;
        }
    }

    private void goCreate() {
        Observable.timer(500, TimeUnit.MILLISECONDS)
                .subscribeOn(Schedulers.newThread())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(new Action1<Long>() {
                    @Override
                    public void call(Long aLong) {
                        closeAnimation(contentView);
                    }
                });
    }
}
```
主要是一个popwindow弹出试图。具体解释一下里面的方法
- showMoreWindow()方法里面主要是popwindow的一些初始化操作。
- showAnimation()方法主要实现了进入的动画效果，过程就是遍历根试图，然后根据具体效果给每个子试图指定一个动画效果，这里的处理是延迟，每个子试图显示出来有一个时间差，最终就出现了我们想要的动画效果。这里我延迟处理是通过Rxjava的timer操作符实现的，更多Rxjava场景操作传送至[Rxjava使用场景](http://www.jianshu.com/p/e36ca4a1312d)
- closeAnimation() 方法和showAnimation差不多，不过这里是离开的动画，多出的部分一个是动画监听，动画结束后隐藏子试图，另外一个就是这段代码
```

if (child.getId() == R.id.video_window) {
    Observable.timer((layout.getChildCount() - i) * 30 + 80, TimeUnit.MILLISECONDS)
            .subscribeOn(Schedulers.newThread())
            .observeOn(AndroidSchedulers.mainThread())
            .subscribe(new Action1<Long>() {
                @Override
                public void call(Long aLong) {
                    dismiss();
                }
            });
}
```
这段代码主要的作用的关闭popwindow窗口，这里的R.id.video_window不一定是最后遍历到子试图，这里不是很重要，主要看效果。
- onClick() 就是点击监听了。

#### 附录
希望大家有什么建议和意见都可以提出。望斧正。

期待你们的关注，一起交流android

[GITHUB源码下载](https://github.com/hczcgq/-PopUpAnimation)







