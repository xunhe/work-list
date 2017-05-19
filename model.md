Backbone模型包含应用程序的数据和与数据相关的逻辑处理，我们通过扩展Backbone.Model对象来创建模型：
## 构造函数初始化
当创建一个模型的新实例时，其构造函数，即initialize()方法会被调用：
```javascript
app.MenuModel = Backbone.Model.extend({
    initialize:function(){
        console.log("Initialize the Model");
    }
});
app.menu = new app.MenuModel();
```

## 默认值
创建Backbone模型时，Backbone允许我们为模型设置一组默认值，通过defaults属性即可设置；当实例化模型时，可传入一个参数对象，此对象即为模型的数据，传入参数值将覆盖所设置的默认值。
```javascript
app.MenuModel = Backbone.Model.extend({
    defaults:{
        title:'this is a menu',
        name:'最近会话',
        status:'active'
    }
});
app.menu = new app.MenuModel({
    status:'inactive'
});
console.log(app.menu.toJSON());
```
toJSON()方法可以返回模型的attributes属性浅拷贝的JSON格式，attributes属性是包含模型状态的内部散列表--通常JSON对象的形式表示在服务器上模型数据，在Model中通常是defaults中的属性名和值

## 属性操作
- Model.get()
Model.get()方法用于访问模型的属性：

```javascript
 app.MenuModel = Backbone.Model.extend({
    defaults: {
        title: 'this is a menu',
        name: '最近会话',
        status: 'active'
    }
});
app.menu = new app.MenuModel({
    status: 'inactive'
});
console.log(app.menu.get('status'));//inactive
```
- Model.set()
Model.set()方法用于设置模型的一个或多个属性值：

```javascript
 app.MenuModel = Backbone.Model.extend({
  defaults: {
      title: 'this is a menu',
      name: '最近会话',
      status: 'active'
  }
});
app.menu = new app.MenuModel({
  status: 'inactive'
});
console.log(app.menu.get('status'));//inactive
app.menu.set({
  status: 'active',
  title: 'menu'
});
console.log(app.menu.get('status'));//active
```

- Model.attributes
Backbone模型的attributes属性，是包含模型状态的一个内部散列表，使用Model.attributes可直接访问改模型的所有数据，其通常以服务器返回的JSOn对象数据形式存在：

```javascript
app.MenuModel = Backbone.Model.extend({
  defaults: {
      title: 'this is a menu',
      name: '最近会话',
      status: 'active'
  }
});
app.menu = new app.MenuModel({
  status: 'inactive'
});
console.log(app.menu.attributes);
//Object {status: "inactive", title: "this is a menu", name: "最近会话"}
```
强烈建议使用set方法更新模型attributes对象而不是直接修改attributes对象

- Model.toJSON()
Model.toJSON()方法返回模型attributes对象拷贝副本的JSON格式对象

```javascript
app.MenuModel = Backbone.Model.extend({
  defaults: {
      title: 'this is a menu',
      name: '最近会话',
      status: 'active'
  }
});
app.menu = new app.MenuModel({
  status: 'inactive'
});
console.log(app.menu.toJSON());
//Object {status: "inactive", title: "this is a menu", name: "最近会话"}
console.log(JSON.stringify(app.menu));
//{"status":"inactive","title":"this is a menu","name":"最近会话"}
```
注：当给JSON.stringify()传递带toJSON()方法的对象时，其处理得是该对象执行toJSON()方法后返回的值，而不是原始传入的对象

```javascript
var a = {
  data:{
      name:'惊鸿',
      title:'sj'
  },
  toJSON:function(){
      return 'sds';
  }
};
console.log(JSON.stringify(a));//'sds'
```
## 模型id与cid
### id
id是模型的特殊属性，可以是任意字符串(整型id或者UUID)。在属性中设置的id会被直接拷贝到model属性上。也可以在集合(collections)中通过id获取特定model。
```javascript
app.MenuModel = Backbone.Model.extend({
    defaults:{
        title:'this is a menu',
        name:'最近会话',
        status:'active'
    }
});
app.menu = new app.MenuModel({
    status:'inactive',
    id:1
});
console.log(app.menu.id);
```
这个时候传递的id的值会做为实例的id值，而不是放入attributes属性中。

- idAttribute

模型的唯一标识符，被存储在id属性下，可以通过设置Model的idAttribute与一个唯一值key对应，从而形成一个从该key到模型id的一个映射。

```javascript
app.MenuModel = Backbone.Model.extend({
    defaults:{
        title:'this is a menu',
        name:'最近会话',
        status:'active'
    },
    idAttribute:'idx',
    initialize:function(){

    }
});
app.menu = new app.MenuModel({
    idx:'001',
    id:1
});
console.log(app.menu.id);//'001'
```
这个时候id更改的id不生效，只有通过idx设置的生效,idx可以是任何自定义的key

### cid
cid，即客户端id，是model创建的时候自动产生的唯一标识符。客户端id在model尚未保存到服务器之前便存在，此时model可能仍不具有最终的id，但已经需要在用户界面可见。
```javascript
console.log(app.menu.cid);//c1或者其他
```
## 模型变化监听
通过监听模型上的change事件，可以检测到模型的变化，既可以监听到整个模型的改变，也可以监听模型单个属性的变化；通常在模型Initialize()函数中添加监听器：
```javascript
app.MenuModel = Backbone.Model.extend({
        defaults: {
            title: 'this is a menu',
            name: '最近会话',
            status: 'active'
        },
        initialize: function() {
            //监听模型变化
            this.on('change', function(model) {
                console.log(model);
                console.log('模型已改变');
            });
            //监听模型单个属性变化
            this.on('change:name', function(model) {
                console.log('菜单名已修改');
            });
        }

    });
    app.menu = new app.MenuModel();
    app.menu.set({
        title: 'menu',
        status: 'inactive'
    });
    console.log(app.menu.get('title'));//menu
    console.log(app.menu.get('status'));//inactive
    app.menu.set({
        name: '常用联系人',
    });
    console.log(app.menu.get('name'));//常用联系人
```
> 注意：在改变模型内的属性的时候，先出发的是模型的单个属性变化的事件，最后触发整个模型的变化的事件

## 验证
调用Model.validate()方法进行模型验证，在设置属性值之前对其进行验证，默认，在使用save()方法或者调用set()方法时设置了{validate:true}参数以持久化数据模型均会触发验证；验证通过将不返回值，验证失败将触发'invalid'事件，此方法返回一个错误值，并用此方法返回的值设置模型的validationError属性，同时模型上的属性不会持久化到服务器：

```javascript
app.MenuModel = Backbone.Model.extend({
        defaults: {
            title: 'this is a menu',
            //name: '最近会话',
            status: 'active'
        },
        validate: function(attr) {
            if (attr.name == undefined ) {
                //返回错误值
                return '菜单名不能为空';
            }
        },
        initialize: function() {
            //监听invalid事件
            this.on('invalid', function(model, err) {
                console.log('title: ' + model.get('title'), '; error: ' + err);
            });
        }
    });
    app.menu = new app.MenuModel();
    console.log((app.menu).toJSON());
    //Object {title: "this is a menu", status: "active"}

    //set()方法且设置{validate: true}参数
    app.menu.set({
        title: 'menu',
    }, {validate: true});
    console.log(app.menu.get('title'));//this is a menu

     //set()方法但不设置{validate: true}参数
    app.menu.set({
        title: 'menu',
    });
    console.log(app.menu.get('title'));//menu
```
> 注：validate函数接收的参数对象是在执行set()或save()方法后模型的值对象，因为需要在set方法或者save方法中设定validate的值为true



