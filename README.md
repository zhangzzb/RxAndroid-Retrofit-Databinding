# 浅谈 RxAndroid + Retrofit + Databinding
浅谈 RxAndroid + Retrofit + Databinding最近 RxAndroid 、MVP、MVVM 一直是 Android 程序猿茶余饭后的谈资，于是我也抱着凑热闹的态度试试了试水。这里就谈谈试水后的感受
什么是 RxAndroid ?要说什么是 RxAndroid ，得从 RxJava 说起。RxJava 在 GitHub 主页上的自我介绍是 “a library for composing asynchronous and event-based programs using observable sequences for the Java VM”（一个在 Java VM 上使用可观测的序列来组成异步的、基于事件的程序的库）。这就是 RxJava ，概括得非常精准。
RxJava 的本质可以压缩为异步这一个词。说到根上，它就是一个实现异步操作的库，而别的定语都是基于这之上的。
而RxAndroid是RxJava的一个针对Android平台的扩展，主要用于 Android 开发。

什么是 Retrofit ?Retrofit 是一套 RESTful 架构的 Android（Java） 客户端实现，基于注解，提供 JSON to POJO（Plain Ordinary Java Object ，简单 Java 对象），POJO to JSON，网络请求（POST，GET， PUT，DELETE 等）封装。
既然只是一个网络请求封装库，现在已经有了那么多的大家已经耳熟能详的网络请求封装库了，为什么还要介绍它呢，原因在于 Retrofit 是一套注解形的网络请求封装库，让我们的代码结构更给为清晰。它可以直接解析JSON数据变成JAVA对象，甚至支持回调操作，处理不同的结果。主要是 Retrofit 能很好的与 RxAndroid 配合使用。
1 Retrofit怎么用

虽然Retrofit官网已经说明了，我还是要按照我的思路说一下它的使用方法

比如你要请求这么一个api：

https://api.github.com/repos/{owner}/{repo}/contributors

查看github上某个repo的contributors，首先你要这样建一个接口：
public interface GitHub {
    @GET("/repos/{owner}/{repo}/contributors")
    Call<List<Contributor>> contributors(
        @Path("owner") String owner,
        @Path("repo") String repo);
}然后你还需要创建一个Retrofit对象：
public static final String API_URL = "https://api.github.com";
// Create a very simple REST adapter which points the GitHub API.
Retrofit retrofit = new Retrofit.Builder()
    .baseUrl(API_URL)
    .addConverterFactory(GsonConverterFactory.create())
    .build();
再用这个Retrofit对象创建一个GitHub对象：
// Create an instance of our GitHub API interface.
GitHub github = retrofit.create(GitHub.class);
// Create a call instance for looking up Retrofit contributors.
Call<List<Contributor>> call = github.contributors("square", "retrofit");
最后你就可以用这个Githb对象获得数据了：
// Fetch and print a list of the contributors to the library.
call.enqueue(new Callback<List<Contributor>>() {
    @Override
    public void onResponse(Response<List<Contributor>> response) {
        for (Contributor contributor : response.body()) {
            System.out.println(contributor.login + " (" + contributor.contributions + ")");
        }
    }
    @Override
    public void onFailure(Throwable t) {
    }
});
这个使用方式看上去和Volley的方式完全不一样，使用Volley时你必须先创建一个
Request对象，包括这个请求的Method，Url，Url的参数，以及一个请求成功和失败的Listener，然后把这个请求放到RequestQueue中，最后NetworkDispatcher会请求服务器获得数据。而Retrofit只要创建一个接口就可以了，太不可思议了！！而我要说，其实这两种方式本质上是一样的，只是这个框架描述HTTP请求的方式而已。因此，你可以发现上面的Github接口其实就是Retrofit对一个HTTP请求的描述
想更详细的了解 Retrofit，可以查看官方文档
什么是 MVVP ?MVC（Model-View-Controller）和 MVP（Model-View-Presenter）是最常见的软件架构之一，业界有着广泛应用，大家一定不陌生。但知道什么事 MVVP 的就不多了，它本身很容易理解，但是要讲清楚，这几个架构的区别就不容易了。

MVVM（Model-View-ViewModel），它采用双向绑定（data-binding）：View的变动，自动反映在 ViewModel，反之亦然。Angular 和 Ember 都采用这种模式。
而 Google 新推出的 Databinding 正是采用了这种模式。

RxAndroid + Retrofit + Databinding
上面已经分别介绍了 RxAndroid、Retrofit、Databinding ，想必大家也有了个初步的认识，那我们就看看 RxAndroid + Retrofit + Databinding 产生的“化学反应”。浅谈 RxAndroid + Retrofit + Databinding最近 RxAndroid 、MVP、MVVM 一直是 Android 程序猿茶余饭后的谈资，于是我也抱着凑热闹的态度试试了试水。这里就谈谈试水后的感受

什么是 RxAndroid ?要说什么是 RxAndroid ，得从 RxJava 说起。RxJava 在 GitHub 主页上的自我介绍是 “a library for composing asynchronous and event-based programs using observable sequences for the Java VM”（一个在 Java VM 上使用可观测的序列来组成异步的、基于事件的程序的库）。这就是 RxJava ，概括得非常精准。
RxJava 的本质可以压缩为异步这一个词。说到根上，它就是一个实现异步操作的库，而别的定语都是基于这之上的。
而RxAndroid是RxJava的一个针对Android平台的扩展，主要用于 Android 开发。
想更详细的了解 Retrofit，可以查看官方文档
上面已经分别介绍了 RxAndroid、Retrofit、Databinding ，想必大家也有了个初步的认识，那我们就看看 RxAndroid + Retrofit + Databinding 产生的“化学反应”。
扩展阅读
	1. RxJava / RxAndroid

		* 官方 WIKI：https://github.com/ReactiveX/RxJava/wiki

		* 极力推荐的代码家干货：http://gank.io/post/560e15be2dca930e00da1083


	2. Retrofit:

		* Retrofit 官方文档：http://square.github.io/retrofit/

		* Retrofit 使用介绍：http://www.cnblogs.com/angeldevil/p/3757335.html

		* Retrofit 离线缓存策略：http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2016/0115/3873.html


	3. Databinding

		* MVC，MVP 和 MVVM 的图示：http://www.ruanyifeng.com/blog/2015/02/mvcmvp_mvvm.html

		* DataBinding 用户指南：http://segmentfault.com/a/1190000002876984

		* 
Github 上比较全面的：https://github.com/LyndonChin/MasteringAndroidDataBinding




