---
title: angular2学习
date: '2020-10-21'
spoiler: learn angular2.
---

## quickStart Demo
### part1 and part2
1. angular系统库自带`@angular`开头,最主要的库`@angular\core`;<br>
   其他相对重要的库:`@angular/common,@angular/router,@angular/http`;
2. **import**注意点:
   * 引入系统自带库,只需要直接写module名称。<br>`import { Component } from '@angular/core';`
   * 引入自己的文件,需要带路径。<br>`import { AppComponent } from './app.component';`
   * 引入路径时,`./`表示引入的module与当前的文件在**同一目录**下。
3. 常用的标签:`@Injectable[类似于JAVA中的@Autowired], @Input, @Output, @RouterConfig,@Component`
   * 绑定标签
     * property[]
      1. 标签
       ```
           Element property
           Component property
           Directive property
       ```
      2. DEMO
       ```
           <img [src] = "heroImageUrl">
           <hero-detail [hero]="currentHero"></hero-detail>
           <div [ngClass] = "{selected: isSelected}"></div>
       ```
     * event()
       1. 标签
       ```
            Element property
            Component property
            Directive property
       ```
       2. DEMO
       ```
           <button (click) = "onSave()">Save</button>
           <hero-detail (deleteRequest)="deleteHero()"></hero-detail>
           <div (myClick)="clicked=$event">click me</div>
       ```
     * 双向绑定[()]
       1. 标签
       `Event and property`
       2. DEMO
       ```
          <input [(ngModel)]="heroName">
       ```
     * Attribute[]
       1. 标签
       `Attributes(the exception)`
       2. 语法:`attr.`
       3. DEMO
       ```
          <button [attr.aria-label]="help">help</button>
       ```
     * Class[]
       1. 标签
       `class property`
       2. 语法:`[class]=value`【替换所有的class值】;`class.`:替换局部class名称
       3. DEMO
       ```
          <div [class.special]="isSpecial">Special</div>
       ```
       4. NgClass代替
       ```
           setClasses() {
             let classes =  {
               saveable: this.canSave,      // true
               modified: !this.isUnchanged, // false
               special: this.isSpecial,     // true
             };
             return classes;
           }
           <div [ngClass]="{cssClass: setClass()}"></div>
       ```
     * Style[]
       1. 标签
       `style property`
       2. 语法:`style.value`
       3. DEMO
       ```
          <button [style.color] = "isSpecial ? 'red' : 'green'">
       ```
       4. NgStyle代替
       ```
           setStyles() {
             let styles = {
               // CSS property names
              'font-style':  this.canSave      ? 'italic' : 'normal',  // italic
               'font-weight': !this.isUnchanged ? 'bold'   : 'normal',  // normal
               'font-size':   this.isSpecial    ? '24px'   : '8px',     // 24px
             };
             return styles;
           }
           <div [ngStyle]="{style: setStyles()}">
             This div is italic, normal weight, and extra large (24px).
           </div>
       ```
     * template
       1. 引用变量#
       ```
           <!-- phone refers to the input element; pass its `value` to an event handler -->
           <input #phone placeholder="phone number">
           <button (click)="callPhone(phone.value)">Call</button>
           <!-- fax refers to the input element; pass its `value` to an event handler -->
           <input ref-fax placeholder="fax number">
           <button (click)="callFax(fax.value)">Fax</button>
       ```
       2. form表单【theForm变量可以用来校验表单的值`theForm.form.valid`】
       ```
           <form (ngSubmit)="onSubmit(theForm)" #theForm="ngForm">
             <div class="form-group">
               <label for="name">Name</label>
               <input class="form-control" required ngControl="firstName"
                 [(ngModel)]="currentHero.firstName">
             </div>
             <button type="submit" [disabled]="!theForm.form.valid">Submit</button>
           </form>
       ```
       3. 定义input和output属性
       `@Input(),@Output()`:单个属性;
       `inputs,outputs`: 多个属性
       4. 操作符
       * |:以|后面的形式输出
       ```
           <!-- Pipe chaining: convert title to uppercase, then to lowercase -->
           <div>
             Title through a pipe chain:
             {{title | uppercase | lowercase}}
           </div>
           <!-- 以JSON格式输出-->
           <div>{{currentHero | json}}</div>
       ```
       * ?.: 防止变量或对象为null时,输出异常
       ```
          The current hero's name is {{currentHero?.firstName}}
       ```
4. 数据绑定【**只能在properties和events中使用**】:
   * `{{value}}`: 绑定页面的值,JS变量值的绑定
   * `[property]=value`: 绑定属性或对象的值,大多数用在自定义组件中。<br>例如：`<div [hidden]="!submitted">`
   * `(event) = "handler"`:绑定事件,主要对应函数
   * `[(ngModel)] = "property"`: 双向绑定。`[ngModel]`:设置值;`(ngModelChange)`:修改值触发。
   * `ngSubmit`: form表单提交
   * `#heroForm`: 表示模板中引用的变量
   * `ngControlName`： 认证和表单元素跟踪
   * `valid`属性：用于判断input输入控件是否认证来显示或隐藏错误提示信息
   * 总结【其中`[],()`与`bind-,on-bindon-`效果一致】
        * 数据到视图
          1. 语法
          ```angular
          {{expression}}
          [target]="expression"
          bind-target="expression"
          ```
          2. 绑定类型
          ```
          Interpolation
          Property
          Attribute
          Class
          Stype
          ```
        * 视图到数据绑定
          1. 语法
          ```
          (target)="statement"
          on-target="statement"
          ```
          2. 绑定类型
          `event`
        * 双向绑定
          1. 语法
          ```
          [(target)] = "expression"
          bind-target="expression"
          ```
          2. 绑定类型:双向绑定

5. 事件定义: `$event`【用于绑定所有事件变量】,DOM元素的event事件;
   * `$event.target.value`: 获取HTML元素的value
   * `(keyup.enter)="values=box.value"`:过滤enter键
   * `(ngModelChange) = "model.name = $event"`
   * `$event.target`: 获取HTML元素
   * `自定义事件`:EventEmitter。事件触发:`EventEmitter.emit()`
6. 依赖注入
  * Angular2的依赖注入是在main.ts的`bootstrap(AppComponent);`中注入的
  * **providers**：出现在@Component的providers: [HeroService]，为当前类注入HeroService类，在directives中的类都可以使用已注入的服务类。
  * 依赖隐含创建：
  ```
  injector = ReflectiveInjector.resolveAndCreate([Car, Engine, Tires]);
  let car = injector.get(Car);
  ```
  * 建议在每个service类中都使用**@Injectable()**
  * `providers: [Logger]`=>`{[provide:Logger,useClass:Logger]}`.
  * `@Optional`: 可选择的。例如:
  ```
      constructor(@Optional() private logger: Logger) {
        if (this.logger) {
          this.logger.log(some_message);
        }
      }
  ```
  在logger不需要注入时,需要添加null值
### part3
  1. 将详情页以组件的形式展现<br/>
  2. **@Input**:用于页面和JS文件的对象绑定
### part4
  1. ngOnInit在AppComponent启动时调用
  2. **provider** 在AppComponent调用Service服务时需要在provider中定义;
  3. 利用Promise.resolve处理异步请求,并用.then方法处理异步回调结果;
### part5
  1. routerLink:按routerConfig中的名字匹配;
  2. router.navigate(['routerConfig中的name', {id: this.selectedHero.id}]);
  3. 配置带参数的config
  ```
      {
         path: '/detail/:id',
         name: 'HeroDetail',
         component: HeroDetailComponent
      }
  ```
  4. html标记语言(如碰到<template></template>这种标记,需要将星号的标签将"*"去掉)
  >  `(click)`:点击事件;<br>
  `<input [(ngModel)]="hero.name" placeholder="name"/>`: 为文本赋值并绑定<br>
  * _*ngIf_: 条件语句隐藏对应的DOM元素;在**template**中需要将*去掉,例如:
  ```
      <template [ngIf]="currentHero">
        <hero-detail [hero]="currentHero"></hero-detail>
      </template>
  ```
  * _*ngFor_
    * 绑定循环索引i(初始化为0)`<li *ngFor="let hero of heroes;let i=index">{{i+1}}-{{hero-name}}</li>`
    * 防止DOM修改元素
    ```
        trackByHeroes(index: number, hero: Hero) { return hero.id; }
        <div *ngFor="let hero of heroes; trackBy:trackByHeroes">({{hero.id}}) {{hero.fullName}}</div>
        <div *ngFor="let hero of heroes" *ngForTrackBy="trackByHeroes">({{hero.id}}) {{hero.fullName}}</div>
    ```
  * _ngSwitch_: 类似于java的switch
  ```
      <span [ngSwitch]="toeChoice">
        <span *ngSwitchWhen="'Eenie'">Eenie</span>
        <span *ngSwitchWhen="'Meanie'">Meanie</span>
        <span *ngSwitchWhen="'Miney'">Miney</span>
        <span *ngSwitchWhen="'Moe'">Moe</span>
        <span *ngSwitchDefault>other</span>
      </span>
  ```
  5. 使用Router步骤:<br>1. `import Router`;
  <br>2.定义构造器
  <br>`constructor(private router: Router,private heroService: HeroService) { }`
  <br>`this.router.navigate(['HeroDetail', { id: this.selectedHero.id }]);`: 带参数跳转
### part6
  1. Http Promise
  > http.get

    ```js
        this.http.get(this.heroesUrl)
                   .toPromise()
                   .then(response => response.json().data)
                   .catch(this.handleError);
    ```
  - http.get返回RxJS Observable对象。
  - **Observables**用于存储异步数据流。
  - 将Observable对象转换为Promise对象,需要引入`'rxjs/add/operator/toPromise'`
  - 取出值:`.then(response => response.json().data)`
  - 异步错误处理: `this.handleError`。
  ```js
      private handleError(error: any) {
        console.error('An error occurred', error);
        // 以友好的方式返回
        return Promise.reject(error.message || error);
      }
  ```

    > http POST

    ```
        this.http
                 .post(this.heroesUrl, JSON.stringify(hero), {headers: headers})
                 .toPromise()
                 .then(res => res.json().data)
                 .catch(this.handleError);
    ```

  - 设置header格式

    ```
        let headers = new Headers({
                      'Content-Type': 'application/json'});
    ```

  - 将对象转化为字符串:`JSON.stringify`

## Attribute Directive

### ElementRef
定义构造函数，注入ElementRef,这样可以访问DOM element。

```Angular2
  constructor(el: ElementRef){
    el.nativeElement.style.backgroundColor = 'yellow';
  }
```

### @HostListener
用于监听DOM element的事件

```Angular2
// 鼠标进入时触发
@HostListener('mouseenter') onMouseEnter() {
  this.highLight('yellow');
}
// 鼠标离开时触发
@HostListener('mouseleave') onMouseLeave() {
  this.highLight('blue');
}
```

### @Input
将前后台的变量传值进行动态绑定
```Angular2
    // 后台绑定
    @Input() set defaultColor(colorName:string) {
        this._defaultColor = colorName || this._defaultColor;
    }

    // 对应前台输入的color值
    @Input('myHighlight') highlightColor:string;

```
前台使用:
1. 'color'在`[myHighlight]="color"`是绑定的数据源
2. 'myHighlight'在`[myHighlight]="color"`是绑定的标签【必须】
```html
    <p [myHighlight]="color" [defaultColor]="'violet'">Highlight me!</p>
```
