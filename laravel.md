Laravel笔记
一、 composer的安装：
--------------------------------------------------------------
  1.Composer是什么？
    是 PHP 用来管理依赖（dependency）关系的工具。
    你可以在自己的项目中声明所依赖的外部工具库（libraries），
    Composer 会帮你安装这些依赖的库文件。
  2.网址：http://www.phpcomposer.com/
     
     中国全量镜像：http://pkg.phpcomposer.com/
        启用本镜像服务命令：
            composer config -g repo.packagist composer https://packagist.phpcomposer.com
            或
            composer config repo.packagist composer https://packagist.phpcomposer.com
  3.Composer常用命令：
        composer -v  查看版本
        composer selfupdate 更新composer

注意：推荐使用杰哥编写的word文档（laravel安装文档.docx）
        
二、安装Laravel框架
------------------------------------------------------------------
    中国权威官方：http://www.golaravel.com/（版本说明和新版本预告）
    laravel学院：http://laravelacademy.org/（下载手册）
    laravel-china：https://laravel-china.org/（中文手册）

    对运行环境的要求： 
        PHP版本 >= 5.6.4
        PHP扩展：OpenSSL
        PHP扩展：PDO
        PHP扩展：Mbstring
        PHP扩展：Tokenizer


    通过 Composer Create-Project 命令安装 Laravel：
       命令：composer create-project laravel/laravel --prefer-dist                 

       (--prefer-dist从压缩包中获取资源)
    
三、本地域名解析与apapche虚拟主机配置（window下）
------------------------------------------------------------------
    1. 打开：C:\Windows\System32\drivers\etc目录中的hosts文件：
        配置信息：127.0.0.1 自定义主机名
        
    2. 在apache的httpd-vhosts.conf配置文件中配置
        <VirtualHost *:80>
            ServerAdmin zhangtao@lampbrother.net
            DocumentRoot "虚拟主机目录位置"
            ServerName 虚拟主机名
            ErrorLog "logs/虚拟主机名-error.log"
            CustomLog "logs/虚拟主机名-access.log" common
        </VirtualHost>
    
四、应用程序结构介绍:
-----------------------------------------------------------------------
    详见手册中《系统架构》的应用程序结构
    
五、HTTP 路由
-----------------------------------------------------------------------
    1. 基本路由：
        Route::get('/', function()
        {
            return 'Hello World';
        });
        Route::post('foo/bar', function()
        {
            return 'Hello World';
        });
        Route::put('foo/bar', function()
        {
            //
        });
        Route::delete('foo/bar', function()
        {
            //
        });
        Route::match(['get', 'post'], '/', function() //多种请求注册路由
        {
            return 'Hello World';
        });
    2. 路由参数
        Route::get('user/{id}', function($id)
        {
            return 'User '.$id;
        });

六. 环境配置与数据库连接
---------------------------------------------------------------------------
    修改：项目下的.env文件
    
        
六、laravelDebug安装与调试命令
--------------------------------------------------------------------------
    网址：https://github.com/barryvdh/laravel-debugbar
    
    安装命令：composer require barryvdh/laravel-debugbar
    进入：config/app.php文件
        配置：
            Barryvdh\Debugbar\ServiceProvider::class,
            'Debugbar' => Barryvdh\Debugbar\Facade::class,
            
    执行命令：php artisan vendor:publish --provider="Barryvdh\Debugbar\ServiceProvider"
    
 
 
七、控制器的创建
----------------------------------------------------------------------------
    创建一个RESTful资源控制器
    命令：php artisan make:controller StuController
    
    命令：php artisan make:controller StuController --resource 
        --resource创建一个资源控制器
    
    控制器中代码
    //在控制器中查询数据,并加载模板输出
    public function index()
    {
        $list = \DB::table('stu')->get();
        return view('stu.index',["list"=>$list]);
    }
    
    在web.php的路由文件中配置
    Route::get('stu/index', 'StuController@index');
    
    public function index()
    {
        //$list = \DB::table('stu')->get();
        $list = \DB::table('stu')->paginate(5);
        
       
        return view('stu.index',["list"=>$list]);
    }
    
    public function create()
    {
        return view("stu.create");
    }
    
    public function store(Request $request)
    {   
        //dd($request);
        $input = $request->all();
        unset($input['_token']);
       
        $id = \DB::table('stu')->insertGetId($input);
        return "添加成功！id号".$id;       
    }
    
    public function update()
    {
        return "update";
    }
    
    public function show($id)
    {
        $stu = \DB::table('stu')->where("id","=",$id)->first();
        dd($stu);
    }

    public function destroy($id)
    {
        return "delete".$id;
    }
    
    
   八、Laravel 中Request请求对象的使用
-----------------------------------------------------------
    1. 使用方式：
        1.1 通过 Facade
            在控制器中使用： use Request导入
            在控制器的方法中获取参数信息：$name = Request::input('name');
            
        1.2 通过依赖注入（推荐）
            在控制器中使用：use Illuminate\Http\Request; 导入
            在控制器的方法中使用参数注入request对象
                public function store(Request $request)
                {
                    $name = $request->input('name');
                }
                
    2. 取得输入数据:
        2.1 $name = Request::input('name'); 获取请求参数name的值
        2.2 $name = Request::input('name', 'Sally'); 获取参数name的值，若没有则使用Sally默认值
        2.3 if (Request::has('name')){ ... }  判断是否有此参数。
        
        2.4 Request::all();  获取所有参数值
        
        2.5 获取部分参数值
            $data = $request->only("name","id"); //获取部分参数值
            $data = $request->except("name"); //获取指定外部分参数值
            
        2.6 获取数组中的值
  
  九.  Laravel中的响应:Response
--------------------------------------------------------
    1. 基本响应
        1.1 从路由返回字串
                Route::get("/hh",function(){
                    return "Hello World!";
                });
                
        1.2 自定义响应:
              在控制器中使用response: use Illuminate\Http\Response;
              控制器方法中的代码
               $content="Hello Laravel!";
               $status = 200;
               $value = "text/html";
               return (new Response($content, $status))->header('Content-Type', $value);
        
        1.3 在响应送出视图  
               return response()->view('hello')->header('Content-Type',"text/html");
               
                
    2. 重定向
        2.1 return redirect('user/login');
        
        
 十 视图
---------------------------------------------------------
    视图被保存在 resources/views 文件夹内 
    实例创捷一个vv.php视图文件
    
    //在控制器的方法中加载视图方式:
        1. return view("vv"); //加载视图
        2. return view("vv",['name'=>"zhangsan","age"=>20]); //加载视图,并携带参数
        3. return view("vv")->with("name","lisi")->with("age",30); //通过with携带参数值
        
    在视图中如何输出
        <body>
            <h2>Laravel框架视图测试</h2>
            姓名:<?php echo $name; ?>   年龄:<?php echo $age; ?>
        </body>
    

 十一 模板引擎:--Blade
---------------------------------------------------
    Blade 模板后缀名都要命名为 .blade.php
    通过模板引擎在视图中输出
        <body>
            <h2>Laravel框架视图测试</h2>
            姓名:{{ $name }}   年龄:{{ $age }}
        </body>