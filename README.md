KUtils(代码来源第三方,只做代码搬运工)
========
android快速开发常用第三方库整合,集成了优雅的日志打印(可自动格式化json,xml,日志输出无字符长度4000的限制),两行代码调用EventBus事件分发,okgo网络访问一行代码实现文件上传下载带进度,上送json xml 等参数,可设置缓存模式以及SSL认证等,万能的RecyclerView适配器BaseQuicklyAdapter,实现上啦刷新,下拉加载,item不同布局,一行代码设置头布局和脚布局.

 # 一. 新增KLog 日志打印
 ## 使用方式:
 ```Java
     //全局只需初始化一次
     KLog.init(BuildConfig.LOG_DEBUG, "KLog");

     KLog.d("");
     KLog.xml("");//打印xml数据 自动格式化   看图
     KLog.json("");//打印json数据 自动格式化 看图
  ```
  ## 效果图
![image](https://github.com/devzwy/KUtils/raw/master/Screenshot/KLogImage.png)

# 二. 新增EventBus事件分发

## 使用方式:
```Java
        //注册监听
         EventBus.getDefault().register(this);
         //发出事件
         EventBus.getDefault().post(new User("张三",26),"张三");
          //接收事件
         @Subscriber(tag = "张三", mode = ThreadMode.MAIN)
             public void OnEventBus_ZhangSan(User user) {
                 KLog.d("EventBus使用tag张三接收到User:" + user);
             }
          //注销事件
         EventBus.getDefault().unregister(this);
```
## 效果图
![image](https://github.com/devzwy/KUtils/raw/master/Screenshot/MainAty.png)
![image](https://github.com/devzwy/KUtils/raw/master/Screenshot/TwoAty.png)


# 三. 新增okgo网络访问

## 使用方式:
```Java
//全局只需初始化一次
OkGo.init(this);
        //以下都不是必须的，根据需要自行选择,一般来说只需要 debug,缓存相关,cookie相关的 就可以了
        OkGo.getInstance()
                // 打开该调试开关,打印级别INFO,并不是异常,是为了显眼,不需要就不要加入该行
                // 最后的true表示是否打印okgo的内部异常，一般打开方便调试错误
                .debug("OkGo", Level.INFO, true);
        //如果使用默认的 60秒,以下三行也不需要传
//                .setConnectTimeout(OkGo.DEFAULT_MILLISECONDS)  //全局的连接超时时间
//                .setReadTimeOut(OkGo.DEFAULT_MILLISECONDS)     //全局的读取超时时间
//                .setWriteTimeOut(OkGo.DEFAULT_MILLISECONDS)    //全局的写入超时时间

        //可以全局统一设置缓存模式,默认是不使用缓存,可以不传,具体其他模式看 github 介绍 https://github.com/jeasonlzy/
//                .setCacheMode(CacheMode.NO_CACHE)

        //可以全局统一设置缓存时间,默认永不过期,具体使用方法看 github 介绍
//                .setCacheTime(CacheEntity.CACHE_NEVER_EXPIRE)

        //可以全局统一设置超时重连次数,默认为三次,那么最差的情况会请求4次(一次原始请求,三次重连请求),不需要可以设置为0
//                .setRetryCount(3)

        //如果不想让框架管理cookie（或者叫session的保持）,以下不需要
//              .setCookieStore(new MemoryCookieStore())            //cookie使用内存缓存（app退出后，cookie消失）
//                .setCookieStore(new PersistentCookieStore())        //cookie持久化存储，如果cookie不过期，则一直有效

        //可以设置https的证书,以下几种方案根据需要自己设置
//                .setCertificates()                                  //方法一：信任所有证书,不安全有风险
//              .setCertificates(new SafeTrustManager())            //方法二：自定义信任规则，校验服务端证书
//              .setCertificates(getAssets().open("srca.cer"))      //方法三：使用预埋证书，校验服务端证书（自签名证书）
//              //方法四：使用bks证书和密码管理客户端证书（双向认证），使用预埋证书，校验服务端证书（自签名证书）
//               .setCertificates(getAssets().open("xxx.bks"), "123456", getAssets().open("yyy.cer"))//

        //配置https的域名匹配规则，详细看demo的初始化介绍，不需要就不要加入，使用不当会导致https握手失败
//               .setHostnameVerifier(new SafeHostnameVerifier())

        //可以添加全局拦截器，不需要就不要加入，错误写法直接导致任何回调不执行
//                .addInterceptor(new Interceptor() {
//                    @Override
//                    public Response intercept(Chain chain) throws IOException {
//                        return chain.proceed(chain.request());
//                    }
//                })

        //这两行同上，不需要就不要加入
//                .addCommonHeaders(headers)  //设置全局公共头
//                .addCommonParams(params);   //设置全局公共参数


//简单请求
 OkGo.post("url").params("key","v").execute(new AbsCallback<User>() {
            @Override
            public void onSuccess(User user, Call call, Response response) {

            }

            @Override
            public User convertSuccess(Response response) throws Exception {
                return null;
            }
        });
```
- [OkGo详细使用文档](https://github.com/devzwy/KUtils/blob/master/Word/README_OKGO.md)

# 四. 新增BaseQuicklyAdapter

## 使用方式:
```Java
//定义适配器
public abstract class MyAdapter extends BaseQuickAdapter<MainTab,BaseViewHolder> {


    public MyAdapter(@LayoutRes int layoutResId, @Nullable List<MainTab> data) {
        super(layoutResId, data);
    }
}

//初始化
 private MyAdapter mAdapter = new MyAdapter(R.layout.item_main_tab, null) {
        @Override
        protected void convert(BaseViewHolder helper, MainTab item) {
            helper.setText(R.id.tv_tab, item.getTabName());
            helper.addOnClickListener(R.id.tv_tab);
        }
    };

//设置适配器及填充数据
  mRvMain.setLayoutManager(new LinearLayoutManager(this));//设置rv布局走向
        mAdapter.isFirstOnly(false);//item的加载动画是否仅在第一次加载时生效
        mAdapter.openLoadAnimation(BaseQuickAdapter.ALPHAIN);//设置加载动画
        mRvMain.setAdapter(mAdapter);

        //添加数据
        List<MainTab> l = new ArrayList<>();
        l.add(new MainTab("测试EventBus事件分发", 0));
        l.add(new MainTab("新功能1", 1));
        l.add(new MainTab("新功能2", 2));
        l.add(new MainTab("新功能3", 3));
        l.add(new MainTab("新功能4", 4));

        mAdapter.setNewData(l);

        //处理item点击事件
        mRvMain.addOnItemTouchListener(new OnItemChildClickListener() {
            @Override
            public void onSimpleItemChildClick(BaseQuickAdapter adapter, View view, int position) {
                //item中的单控件点击事件
                switch (mAdapter.getData().get(position).getId()) {
                    case 0:
                        //测试EventBus事件分发
                        startActivity(new Intent(MainActivity.this, TwoActivity.class));
                        break;
                    case 1:

                        break;
                    case 2:

                        break;
                    case 3:

                        break;
                }
            }
        });
        mRvMain.addOnItemTouchListener(new OnItemClickListener() {
            @Override
            public void onSimpleItemClick(BaseQuickAdapter adapter, View view, int position) {
                //item的点击事件

            }
        });
```
- [BaseQuicklyAdapter详细使用文档](https://github.com/devzwy/KUtils/raw/master/Word/README_BaseQuicklyAdapter)

   #### Luban   preferences