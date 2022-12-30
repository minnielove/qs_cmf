## SubBuilder

<big>**此功能需要提供action genQsSubBuilderRowToJs**</big>
```
用于异步处理点击添加新字段所需HTML

trait类  \Qscmf\Builder\TSubBuilder 已实现此方法，可在需要的控制器中引入
```

```
使用技巧：
可以给.text-right .btn-sm元素添加”afterQsSubBuilderRowToJs“事件，处理新字段添加后的动作。
```

```php
  $('body').on('afterQsSubBuilderRowToJs', '.text-right .btn-sm', function() {
     // dosomething
  });
```

#### 用法

#### 只读模式
1. 实例SubTableBuilder对象时可控制隐藏/显示 删除、添加新字段 按钮，默认是false，即展示 
   ```php
   $sub_builder = new \Qscmf\Builder\SubTableBuilder(true);
   ```
   
2. 表单项单个设置为只读
  ```php
   $sub_builder = new \Qscmf\Builder\SubTableBuilder(true);
   $sub_builder->addTableHeader('text', '10%');
   $sub_builder->addFormItem('text', 'text', '', true);
   ```

3. 表单项统一设置为只读
   ```php
   $sub_builder = new \Qscmf\Builder\SubTableBuilder(true);
   $sub_builder->setColReadOnly(true);
   ```

#### addTableHeader
```blade
该方法用于加入一个子表单项的标题

参数
$name 名称
$width 该项占用整行宽度的比例
$tip 列提示文字，默认为''
```
```php
$sub_builder = new \Qscmf\Builder\SubTableBuilder();
$sub_builder = $sub_builder
        ->addTableHeader('标题', '30%', '提示')
        ->addTableHeader('摘要', '30%', '提示');
```

#### addFormItem
```blade
该方法用于加入一个子表单项的内容

参数
$name item名
$type item类型（查看支持类型）
$options item options
$readonly 是否开启只读模式，默认关闭，false
$extra_class item项额外样式
$extra_attr item项额外属性
$auth_node 列权限点，需要先添加该节点，若该用户无此权限则unset该列；格式为：模块.控制器.方法名，如：['admin.Box.allColumns']

若auth_node存在多个值，支持配置不同逻辑（logic值为and或者or）判断是否显示该列，默认为and：
and：用户拥有全部权限则显示该列，格式为：
['node' => ['模块.控制器.方法名','模块.控制器.方法名'], 'logic' => 'and']
or：用户一个权限都没有则隐藏该列，格式为：
['node' => ['模块.控制器.方法名','模块.控制器.方法名'], 'logic' => 'or']
```
```php
$sub_builder = new \Qscmf\Builder\SubTableBuilder();
$sub_builder = $sub_builder
        ->addTableHeader('标题', '30%')
        ->addTableHeader('摘要', '30%')
        ->addFormItem('id', 'hidden')
        ->addFormItem('title', 'text')
        ->addFormItem('summary', 'textarea');
```

#### setData
```blade
设置子表单项的数据

该方法需要设置表单项属性，建议使用 setFormData 方法直接设置表单值
```
```php
$data = [
    ['name' => 'id', 'type' => 'hidden', 'value' => 1],
    ['name' => 'title', 'type' => 'text', 'value' => 'title'],
    ['name' => 'summary', 'type' => 'textarea', 'value' => 'summary']
];

$sub_builder = new \Qscmf\Builder\SubTableBuilder();
$sub_builder = $sub_builder
        ->addTableHeader('标题', '30%')
        ->addTableHeader('摘要', '30%')
        ->addFormItem('id', 'hidden')
        ->addFormItem('title', 'text')
        ->addFormItem('summary', 'textarea')
        ->setData($data);
```

#### setFormData
```blade
设置子表单的数据
```
```php
$data = [
    ['title' => 'title1', 'summary' => 'summary1'],
    ['title' => 'title2', 'summary' => 'summary2'],
    ['title' => 'title3', 'summary' => 'summary3'],
];

$sub_builder = new \Qscmf\Builder\SubTableBuilder();
$sub_builder = $sub_builder
        ->addTableHeader('标题', '30%')
        ->addTableHeader('摘要', '30%')
        ->addFormItem('id', 'hidden')
        ->addFormItem('title', 'text')
        ->addFormItem('summary', 'textarea')
        ->setData($data);
```

#### makeHtml
```blade
返回所有表单项的html，可以根据需要嵌入FormBuilder
```

#### 类型说明

+ 支持类型
1. checkbox
> + 多选框，目前支持单个
> 
2. text
> + 文本类型
> 
3. hidden
> + 隐藏输入框
> 
4. select
> + 下拉选择
>
5. select2
> + 下拉选择，支持模糊搜索
> + 说明：
>  + select2新增了自定义标签功能
>  ```php
>  $subBuilder = new \Qscmf\Builder\SubTableBuilder();
>  $subBuilder = $subBuilder -> addTableHeader('关键词', '30%')
>                  -> addFormItem('keywords_id', 'select2', [
>                      'tags' => true,
>                      'options' => [ 1 => '测试', 2 => '测试2']
>                      ]);
>  
>  $builder = new FormBuilder();
>  $builder->addFormItem('keywords_id', 'self', '关键词','',$subBuilder->makeHtml());
>  ```
>  + 数据源分组显示
>  ```php
>   $options = [
>       ['text' => '分类一', 'children' => [['id' => '1', 'text' => '选项1'],['id' => '2', 'text' => '选项2']]],
>       ['text' => '分类二', 'children' => [['id' => '3', 'text' => '选项3'],['id' => '4', 'text' => '选项4']]],
>       ['text' => '分类三', 'children' => [['id' => '5', 'text' => '选项5'],['id' => '6', 'text' => '选项6']]],
>  ];
>  ```
>
6. textarea
> + 多行文本
> 
7. date
> + 日期组件
> 
8. num
> + 数字输入框