#### 如何开发composer扩展包
以下说明需要具备composer包开发的相关基础

+ 新增provider
> ```php
> class CustomProvider implements \Bootstrap\Provider {
>  
>      public function register(){
>          // 相关注册代码
>      }
>  }
> ```

+ 注册迁移目录
> ```php
> class CustomProvider implements \Bootstrap\Provider, \Bootstrap\LaravelProvider {
>  
>     public function register(){
>         // 相关注册代码
>     }
> 
>     public function registerLara(){
>          //注册迁移目录代码
>     } 
> 
>     // register的方法是由TP发起的，TP相关的常量，函数可以使用
>     // registerLara是由laravel发起的，这里不要使用TP的内置方法，仅可执行TP的注册迁移函数
>  }
> ```

+ 框架提供的注册接口 (RegisterContainer类)
> 1. registerController
>> + 说明：注册controller
>> + 参数：module_name 模块、 controller_name 控制器、controller_cls 需要映射的controller类名
>
> 2. registerListTopButton
>> + 说明: 注册列表按钮类型
>> + 参数：type 类型、 type_cls 继承\Qscmf\Builder\ButtonType\ButtonType的实现类
>
> 3. registerFormItem
>> + 说明：注册表单控件
>> + 参数：type 类型、 type_cls 继承\Qscmf\Builder\FormType\FormType的实现类
>
> 4. registerSymLink
>> + 说明: 注册软连接
>> + 参数：link_path 软连接绝对路径、 source_path 源绝对路径
>
> 5. registerListSearchType
>> + 说明: 注册列表搜索控件
>> + 参数: type 类型、 type_cls 继承\Qscmf\Builder\ListSearchType\ListSearchType的实现类
>
> 6. registerListRightButtonType
>> + 说明: 注册列表表格按钮
>> + 参数: type 类型、 type_cls 继承\Qscmf\Builder\ListRightButton\ListRightButton的实现类
>
> 7. registerMigration
>> + 说明：注册迁移文件目录
>> + 参数: paths 迁移文件存放的目录数组，只有一个目录时，可以只写一个字符串
>
> 8. registerHeadJs
>> + 说明: 
>>
>>   将组件的js注入dashboard_layout头部，
>>
>>   也可以通过在模板文件添加__QS_REGISTER_JS_TAG_BEGIN__和__QS_REGISTER_JS_TAG_END__指定js注入的位置
>>
>> + 参数: 
>> + src js连接地址
>> + async 是否异步加载（默认false）
>
> 9. registerListColumnType
>> + 说明: 注册ListBuilder column类型组件
>> + 参数: 
>> + type 类型
>> + type_cls 继承\Qscmf\Builder\ColumnType\ColumnType的实现类
>
> 10. registerHeadCss
>> + 说明: 
>>
>>   将组件的css注入dashboard_layout头部，
>>
>>   也可以通过在模板文件添加__QS_REGISTER_CSS_TAG_BEGIN__和__QS_REGISTER_CSS_TAG_END__指定css注入的位置
>>
>> + 参数: 
>> + src css连接地址
> 
> 11. registerBodyHtml
>> + 说明:
>>
>>   将组件的html注入dashboard_layout body的底部，
>>
>>   也可以通过在模板文件添加__QS_REGISTER_BODY_TAG_BEGIN__和__QS_REGISTER_BODY_TAG_END__指定html注入的位置
>>
>> + 参数:
>> + html 需要注入的html
> 
> 12. registerHeaderNavbarRightHtml
>> + 说明:
>>
>>   向dashboard_layout 的header元素注入右侧导航项
>>
>> + 参数:
>> + html 需要注入的html

+ 配置composer.json
> 在composer.json文件添加下面注册信息, 框架可通过该配置自动完成provider注册
> ```php
> "qscmf": {
>    "providers": [
>       #扩展包provider类名#
>    ]
> }
> ```

#### 扩展的自定义配置
读取扩展的自定义配置，可在扩展代码的任意位置通过该函数读取到用户定义的配置

配置文件: 放在根目录下的PackageConfig.php

packageConfig($package_name, $config)

参数：

package_name 扩展名

config 配置名 

举例:
```php
//PackageConfig.php
return [
     //quansitech/send-msg 扩展名
     //module 配置名
    'quansitech/send-msg' => [
        'module' => 'extendAdmin'
    ]
];
```


#### 如何开发表单控件
```php
use Think\View;
use Qscmf\Builder\FormType\FormType;

//实现 Qscmf\Builder\FormType\FormType 接口
//接口必须实现一个build函数，用于完成表单控件的渲染
class CustomType implements FormType{

    //form_type接收所有表单控件创建时需要的参数，如 name、title、tips、options等配置项
    public function build($form_type){
        $view = new View();
        $view->assign('form', $form_type);
        $content = $view->fetch(__DIR__ . '/custom.html');
        return $content;
    }
}
```

#### 如何开发列表按钮扩展
```php
use Illuminate\Support\Str;
use Qscmf\Builder\ButtonType\ButtonType;
use Think\View;

//必须继承\Qscmf\Builder\ButtonType\ButtonType抽象类
class CustomButton extends ButtonType{

    //实现抽象函数build，完成控件渲染
    // option接收所有按钮控件创建时需要的参数，如 type、attribute、tips、auth_node
    public function build(array $option){
        //默认配置值
        $my_attribute['type'] = 'custom';
        $my_attribute['title'] = '自定义类型';
        $my_attribute['target-form'] = 'ids';
        $my_attribute['class'] = 'btn btn-primary';
   
        //用户自定义覆盖默认配置
        if ($option['attribute'] && is_array($option['attribute'])) {
            $option['attribute'] = array_merge($my_attribute, $option['attribute']);
        }

        $gid = Str::uuid();
        $gid = str_repeat('-', '', $gid);
        $option['attribute']['id'] = 'modal-' . $gid;

        $view = new View();
        $view->assign('gid', $gid);
        //仅需渲染按钮外的内容，按钮的渲染由框架完成
        $content = $view->fetch(__DIR__ . '/custom.html');
        //返回渲染结果
        return <<<HTML
{$content}
HTML;
    }
}
```

#### 如何开发列表列字段扩展
```php
use Qscmf\Builder\ButtonType\Save\TargetFormTrait;
use Qscmf\Builder\ColumnType\ColumnType;
use Qscmf\Builder\ColumnType\EditableInterface;

// 必须继承Qscmf\Builder\ColumnType\ColumnType抽象类
// 可以通过实现接口Qscmf\Builder\ColumnType\EditableInterface来自定义该组件编辑状态下的html
// 自定义编辑状态html时，input标签的样式必须包括top button save类型的target_form，否则不会提交该列值
// Qscmf\Builder\ButtonType\Save\TargetFormTrait已经实现getSaveTargetForm()，即获取top button save类型的target_form，使用该trait类即可
class Num extends ColumnType implements EditableInterface
{
    use TargetFormTrait;

    public function build(array &$option, array $data, $listBuilder)
    {
        return $data[$option['name']];
    }

    public function editBuild(&$option, $data, $listBuilder){
        $class = "form-control input text ". $this->getSaveTargetForm();
        return "<input class='{$class}' type='number' name='{$option['name']}[]' value={$data[$option['name']]} />";
    }
}
```

```text
subBuilder共用ColumnType的列类型
```

```text
避免重复加载静态文件，需要提供以下方法引入不同模式下的静态资源。
```

***之后会强制ColumnType实现以下方法，需要升级对应组件***

+ 只读模式 registerCssAndJs
  
  ```php

    static public function registerCssAndJs():?array {
        return [
            "<script src='".asset('libs/cui/cui.extend.min.js')."' ></script>",
            "<script src='".asset('libs/bootstrap-datepicker/bootstrap-datepicker.js')."' ></script>",
            "<script src='".asset('libs/bootstrap-datepicker/locales/bootstrap-datepicker.zh-CN.min.js')."' ></script>",
            "<script src='".asset('libs/bootstrap-datetimepicker/locales/bootstrap-datetimepicker.zh-CN.js')."' ></script>",
        ];
    }
  ```
+ 可编辑模式 registerEditCssAndJs
  
  ```php
    static public function registerEditCssAndJs():?array {
        return [
            "<script src='".asset('libs/cui/cui.extend.min.js')."' ></script>",
            "<script src='".asset('libs/bootstrap-datepicker/bootstrap-datepicker.js')."' ></script>",
            "<script src='".asset('libs/bootstrap-datepicker/locales/bootstrap-datepicker.zh-CN.min.js')."' ></script>",
            "<script src='".asset('libs/bootstrap-datetimepicker/locales/bootstrap-datetimepicker.zh-CN.js')."' ></script>",
        ];
    }
  ```

#### 如何开发列表列右边按钮或者表单页按钮
```php
namespace Qs\ListRightButton\Modal;

use Qs\ModalButton\ModalButtonBuilder;
use Qscmf\Builder\ListRightButton\ListRightButton;

// 列表的右边按钮和表单页的按钮，都是处理一条具体的数据
// 所以它们所需的按钮类型应该是一样的，可以共同使用ListRightButton类型的按钮。

// 必须继承Qscmf\Builder\ListRightButton\ListRightButton抽象类

// 默认的颜色的样式目前有default、primary、warning、danger、success、info
// 由于列表页按钮和表单页的按钮样式不一致，使用mergeAttr合并属性，注入对应的样式类名

class Modal extends ListRightButton
{
    public function build(array &$option, array $data, $listBuilder)
    {
        $my_attribute['type'] = 'modal';
        $my_attribute['title'] = '对话框';
        $my_attribute['class'] = 'primary';
        $my_attribute['data-toggle'] = "modal";

        $builder = $option['options'] ?:$this->defaultModalBuilder();

        $gid = $builder->getGid();

        $my_attribute['data-target']="#".$gid.'Modal';

        if ($option['attribute'] && is_array($option['attribute'])) {
            $option['attribute'] = $listBuilder->mergeAttr($my_attribute, $option['attribute']);
        }

        $option['attribute']['id'] = $gid;

        return (string)$builder;

    }

    protected function defaultModalBuilder(){
        $builder = new ModalButtonBuilder();
        $builder->setTitle('defaultModal');

        return $builder;
    }
}
```

#### 如何开发列表搜索控件
```php
namespace Qs\ListSearchType\Select2;

use Qs\ListSearchType\Select2Builder;
use Think\View;
use Qscmf\Builder\ListSearchType\ListSearchType;

// 必须继承\Qscmf\Builder\ListSearchType\ListSearchType抽象类
class Select2 implements ListSearchType
{
    // 实现抽象函数build，完成控件渲染
    // item接收所有搜索控件创建时需要的参数，如 name、type、title、options、auth_node
    // 返回控件html
    public function build(array $item){
        $options = $item['options'] instanceof Select2Builder ? $item['options'] :
        $this->buildDefBuilder($item['options']);

        !$options->getPlaceholder() && $options->setPlaceholder($item['title']);

        $view = new View();
        $view->assign('item', $item);
        $view->assign('gid', $options->getGid());
        $view->assign('options', $options->getData());
        $view->assign('select2_opt', $options->toArray());
        $view->assign('value', I('get.'.$item['name']));
        $content = $view->fetch(__DIR__ . '/select2.html');
        return $content;
    }

    protected function buildDefBuilder(array $options):Select2Builder{
        return new Select2Builder($options);
    }
}
```

没有列出示例代码的组件扩展都与以上的扩展方法类似，可直接参考上面的代码

#### 扩展列表
+ [阿里云视频点播vod](https://github.com/quansitech/qscmf-formitem-vod)
+ [七牛音视频组件](https://github.com/quansitech/qscmf-formitem-qiniu)
+ [文件批量导出并打包（zip）](https://github.com/quansitech/qscmf-topbutton-download)
+ [xlsx导出excel](https://github.com/quansitech/qscmf-topbutton-export)
