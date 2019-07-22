##AngularJS Expressions
####CONTENTS
[AngularJS Expressions vs. JavaScript Expressions](#)
[Example]()
[Context]()
[Forgiving]()
[No Control Flow Statements]()
[No function declarations or RegExp creation with literal notation
$event]()
[One-time binding]()
&nbsp;&nbsp;&nbsp;&nbsp;[Reasons for using one-time binding]()
&nbsp;&nbsp;&nbsp;&nbsp;[Value stabilization algorithm]()
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Special case for object literals]()
&nbsp;&nbsp;&nbsp;&nbsp;[How to benefit from one-time binding]()
AngularJS expressions are JavaScript-like code snippets that are mainly placed in interpolation bindings such as ```<span title="{{ attrBinding }}">{{ textBinding }}</span>```, but also used directly in directive attributes such as ```ng-click="functionExpression()"```.

For example, these are valid expressions in AngularJS:

1+2
a+b
user.name
items[index]

译：概述：
[AngularJS 表达式 VS Javascript 表达式]()
[举例]()
[上下文]()
[宽容]()
[无控制流程语句]()
[没有函数声明或使用文字表达时法创建正则]()
[一次性绑定]()
&nbsp;&nbsp;&nbsp;&nbsp;[用一次性绑定的原因]()
&nbsp;&nbsp;&nbsp;&nbsp;[值稳定算法]()
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[对象文字的特例]()
&nbsp;&nbsp;&nbsp;&nbsp;[如何从一次洗绑定中受益]()
AngularJS表达式类似于javascript片段代码，主要用于差值表达式中，例如```<span title="{{ attrBinding }}">{{ textBinding }}</span>```，但是也直接i 用于指令属性，如， ```ng-click="functionExpression()"```.

例如： 这是angularjs中的有效表达式
· 1+2
· a+b
`user.name
`items[index]

###AngularJS Expressions vs. JavaScript Expressions
AngularJS expressions are like JavaScript expressions with the following differences:

· __Context__: JavaScript expressions are evaluated against the global window. In AngularJS, expressions are evaluated against a scope object.

__Forgiving__: In JavaScript, trying to evaluate undefined properties generates ReferenceError or TypeError. In AngularJS, expression evaluation is forgiving to undefined and null.

__Filters__: You can use filters within expressions to format data before displaying it.

__No Control Flow Statements__: You cannot use the following in an AngularJS expression: conditionals, loops, or exceptions.

__No Function Declarations__: You cannot declare functions in an AngularJS expression, even inside ng-init directive.

__No RegExp Creation With Literal Notation__: You cannot create regular expressions in an AngularJS expression. An exception to this rule is ng-pattern which accepts valid RegExp.

__No Object Creation With New Operator__: You cannot use new operator in an AngularJS expression.

__No Bitwise, Comma, And Void Operators: You cannot use Bitwise, , or void operators in an AngularJS expression.

If you want to run more complex JavaScript code, you should make it a controller method and call the method from your view. If you want to eval() an AngularJS expression yourself, use the $eval() method.

· 译
### AngularJS表达式与Javascript表达式
·__上下文__：javascript表达式是依据全局的window对象进行评估的，在angularjs只中，表达式是依据scope 对象。
.__宽容__：在Javascript中方，尝试评估undefined属性会报```ReferenceError```或者```TypeError```。在AngularJS中，对于```undefined```或者```null```表达式是宽容的。
·__过滤器__：在展示之前，你可以在表达式中用<font color="blue">过滤器</font>来格式化数据。
.__无流程控制语句__：在AngularJs中，你不能应用如下语句，条件，循环，或者异常。
·__无函数声明__:你不能在AngularJs表达式中声明函数，甚至在```ng-init```指令内也是如此。
·__没有使用文字表达式法来创建正则__:你无法在AngularJS表达式中创建正则表达式，对于这个规则的一个例外就是可以接受有效```正则表达式```的ng-pattern。
·__用New操作符不会产生对象__：在angularjs表达式中，你不能使用```new```操作符；
·__没有 Bitwise, Comma,和Void 操作__:在AngularJS表达式中，你不能使用Bitwise, Comma,或者Void.

如果要运行更复杂的javascript代码，则应将为其设置控制器方法，并绑定在视图中，如果你自己想```eval()```一个AngularJS表达式，用```$eval()```方法.

原文：
###Example
<font color = 'blue'> index.html</font>
```
<span>
  1+2={{1+2}}
</span>
```
<font color='blue'>protractor.js</font>
```
it('should calculate expression in binding', function() {
  expect(element(by.binding('1+2')).getText()).toEqual('1+2=3');
});
```
输出：
1+2=3；
You can try evaluating different expressions here:
你可以在这里试着评估不同的表达式：
<font color='blue'>index.html</font>
```
<div ng-controller="ExampleController" class="expressions">
  Expression:
  <input type='text' ng-model="expr" size="80"/>
  <button ng-click="addExp(expr)">Evaluate</button>
  <ul>
   <li ng-repeat="expr in exprs track by $index">
     [ <a href="" ng-click="removeExp($index)">X</a> ]
     <code>{{expr}}</code> => <span ng-bind="$parent.$eval(expr)"></span>
    </li>
  </ul>
</div>
```

<font color='blue'>script.js</font>
```
angular.module('expressionExample', [])
.controller('ExampleController', ['$scope', function($scope) {
  var exprs = $scope.exprs = [];
  $scope.expr = '3*10|currency';
  $scope.addExp = function(expr) {
    exprs.push(expr);
  };

  $scope.removeExp = function(index) {
    exprs.splice(index, 1);
  };
}]);
```
<font color='blue'>protractor.js</font>
```
it('should allow user expression testing', function() {
  element(by.css('.expressions button')).click();
  var lis = element(by.css('.expressions ul')).all(by.repeater('expr in exprs'));
  expect(lis.count()).toBe(1);
  expect(lis.get(0).getText()).toEqual('[ X ] 3*10|currency => $30.00');
});
```

Expression:
```
3*10|currency
```

·[ X ] 3*10|currency => $30.00

原文：
###Context
AngularJS does not use JavaScript's eval() to evaluate expressions. Instead AngularJS's $parse service processes these expressions.

AngularJS expressions do not have direct access to global variables like window, document or location. This restriction is intentional. It prevents accidental access to the global state – a common source of subtle bugs.

Instead use services like $window and $location in functions on controllers, which are then called from expressions. Such services provide mockable access to globals.

It is possible to access the context object using the identifier this and the locals object using the identifier $locals.

·译
AngularJS不用javascript中的```eval()```来计算表达式，用AngularJs中的```$parse```服务来处理这些表达式。
AngularJs表达式不会直接处理像```window```,```document```,```location```,这样的全局对象，这种限制是故意的，它可以防止意外的访问全局对象-一种微妙错误的常见涞源。
相反，可以用那些可以在表达式中被调用的，像```$window```和```$location```这样的服务，这些服务提供对于全局变量的可模拟访问。可以使用标识符```this``` 和使用标识符```$locals```的locals对象访问上下文对象。

举例：
<font color='blue'>index.html</font>
```
<div class="example2" ng-controller="ExampleController">
  Name: <input ng-model="name" type="text"/>
  <button ng-click="greet()">Greet</button>
  <button ng-click="window.alert('Should not see me')">Won't greet</button>
</div>
```
<font color='blue'>script.js</font>
```
angular.module('expressionExample', [])
.controller('ExampleController', ['$window', '$scope', function($window, $scope) {
  $scope.name = 'World';

  $scope.greet = function() {
    $window.alert('Hello ' + $scope.name);
  };
}]);
```

<font color='blue'>protractor.js</font>
```
it('should calculate expression in binding', function() {
  if (browser.params.browser === 'safari') {
    // Safari can't handle dialogs.
    return;
  }
  element(by.css('[ng-click="greet()"]')).click();

  // We need to give the browser time to display the alert
  browser.wait(protractor.ExpectedConditions.alertIsPresent(), 1000);

  var alertDialog = browser.switchTo().alert();

  expect(alertDialog.getText()).toEqual('Hello World');

  alertDialog.accept();
});
```
Name: <input value="world"> <button>Greet</button><button>Wont`t greet</button>

原文：
###Forgiving
Expression evaluation is forgiving to undefined and null. In JavaScript, evaluating a.b.c throws an exception if a is not an object. While this makes sense for a general purpose language, the expression evaluations are primarily used for data binding, which often look like this:

```{{a.b.c}}```
It makes more sense to show nothing than to throw an exception if a is undefined (perhaps we are waiting for the server response, and it will become defined soon). If expression evaluation wasn't forgiving we'd have to write bindings that clutter the code, for example: ```{{((a||{}).b||{}).c}}```

Similarly, invoking a function ```a.b.c()``` on ```undefined```or ```null``` simply returns undefined.

译；
表达式评估对于undefined和null是宽容的，在js中，如果a不是对象，则对于a.b.c会抛出异常，虽然这对于通用语言有意义，表达式评估主要用于数据绑定，通常如下所示：
```{{a.b.c}}```
如果a未定义，什么都不显示比抛出异常要有意义（获取我们在等待服务响应，并且很快就会定义），如果表达式评估不是宽容的，我们必须编写使代码混乱的绑定， 比如：```{{((a||{}).b||{}).c}}```.

类似的，在undefined或者null上调用函数a.b.c()只返回undefined;

原文：
###No Control Flow Statements
Apart from the ternary operator (a ? b : c), you cannot write a control flow statement in an expression. The reason behind this is core to the AngularJS philosophy that application logic should be in controllers, not the views. If you need a real conditional, loop, or to throw from a view expression, delegate to a JavaScript method instead.

译：
###无控制流程语句
除了三元运算符（a? b: c）之外,你无法在表达式中编写控制流语句。这背后的原因是AngularJS理念的核心，即应用程序的逻辑应该在控制器中而不是在视图中。如果你需要真正的条件，循环，或者从试图中抛出，请委托Javascript方法。

原文：
###No function declarations or RegExp creation with literal notation
You can't declare functions or create regular expressions from within AngularJS expressions. This is to avoid complex model transformation logic inside templates. Such logic is better placed in a controller or in a dedicated filter where it can be tested properly.

译：
###没有函数声明，或者使用文字表达式的方法创建正则表达式
你无法在AngularJs表达式中声明函数或者创建正则表达式。这是为了避免模板内部复杂模型的转换逻辑，这种逻辑可以更好的放置在控制器或者专用过滤器中，以便可以正确的进行测试。

原文：
###$event
Directives like ngClick and ngFocus expose a $event object within the scope of that expression. The object is an instance of a jQuery Event Object when jQuery is present or a similar jqLite object.

<font color='blue'>index.html</font>
```
<div ng-controller="EventController">
  <button ng-click="clickMe($event)">Event</button>
  <p><code>$event</code>: <pre> {{$event | json}}</pre></p>
  <p><code>clickEvent</code>: <pre>{{clickEvent | json}}</pre></p>
</div>
```
<font color='blue'>script.js</font>
```
angular.module('eventExampleApp', []).
controller('EventController', ['$scope', function($scope) {
  /*
   * expose the event object to the scope
   */
  $scope.clickMe = function(clickEvent) {
    $scope.clickEvent = simpleKeys(clickEvent);
    console.log(clickEvent);
  };

  /*
   * return a copy of an object with only non-object keys
   * we need this to avoid circular references
   */
  function simpleKeys(original) {
    return Object.keys(original).reduce(function(obj, key) {
      obj[key] = typeof original[key] === 'object' ? '{ ... }' : original[key];
      return obj;
    }, {});
  }
}]);
```
<button>Event</button>
```
$event:

 
clickEvent:

{
  "isTrusted": true
}
```
Note in the example above how we can pass in $event to clickMe, but how it does not show up in {{$event}}. This is because $event is outside the scope of that binding.

译：
像ng-click和ng-focus这样的指令在该表达式范围内会暴露出$event对象。当Jquery存在或者类似jqLite对象，该对象就是jq对象的实例。

代码示例如上

请注意在上面的示例中，我们是如何将$event传递给clickMe,但是并没有在{{$event}}中显示出来，这是因为$event超出了该绑定的范围。

原文：
###One-time binding
An expression that starts with :: is considered a one-time expression. One-time expressions will stop recalculating once they are stable, which happens after the first digest if the expression result is a non-undefined value (see value stabilization algorithm below).

<font color='blue'>index.html</font>
```
<div ng-controller="EventController">
  <button ng-click="clickMe($event)">Click Me</button>
  <p id="one-time-binding-example">One time binding: {{::name}}</p>
  <p id="normal-binding-example">Normal binding: {{name}}</p>
</div>
```
<font color='script.js'></font>
```
angular.module('oneTimeBindingExampleApp', []).
controller('EventController', ['$scope', function($scope) {
  var counter = 0;
  var names = ['Igor', 'Misko', 'Chirayu', 'Lucas'];
  /*
   * expose the event object to the scope
   */
  $scope.clickMe = function(clickEvent) {
    $scope.name = names[counter % names.length];
    counter++;
  };
}]);
```
<font color='blue'>protractor</font>
```
it('should freeze binding after its value has stabilized', function() {
  var oneTimeBinding = element(by.id('one-time-binding-example'));
  var normalBinding = element(by.id('normal-binding-example'));

  expect(oneTimeBinding.getText()).toEqual('One time binding:');
  expect(normalBinding.getText()).toEqual('Normal binding:');
  element(by.buttonText('Click Me')).click();

  expect(oneTimeBinding.getText()).toEqual('One time binding: Igor');
  expect(normalBinding.getText()).toEqual('Normal binding: Igor');
  element(by.buttonText('Click Me')).click();

  expect(oneTimeBinding.getText()).toEqual('One time binding: Igor');
  expect(normalBinding.getText()).toEqual('Normal binding: Misko');

  element(by.buttonText('Click Me')).click();
  element(by.buttonText('Click Me')).click();

  expect(oneTimeBinding.getText()).toEqual('One time binding: Igor');
  expect(normalBinding.getText()).toEqual('Normal binding: Lucas');
});
}]);
```
<button>Click Me</button>
一次点击：
```
One time binding: Igor

Normal binding: Igor
```

多次点击后：
```
One time binding: Igor

Normal binding: Misko
```

译：
以：：开头的表达式被视为一次性绑定表达式。一次性绑定表达式在稳定之后会停止计算，如果表达式结果为非未定义的，则会发生在第一个digest之后（请参照下面值稳定算法）

原文：
###Reasons for using one-time binding
The main purpose of one-time binding expression is to provide a way to create a binding that gets deregistered and frees up resources once the binding is stabilized. Reducing the number of expressions being watched makes the digest loop faster and allows more information to be displayed at the same time.

译：
一次性绑定表达式的主要目的是提供一种创建绑定的方式，该绑定在稳定后取消注册并释放资源。减少正在watch的表达式的数量，使得digest循环更快并且允许同一时间显示衡多的信息。

原文：
###Value stabilization algorithm
One-time binding expressions will retain the value of the expression at the end of the digest cycle as long as that value is not undefined. If the value of the expression is set within the digest loop and later, within the same digest loop, it is set to undefined, then the expression is not fulfilled and will remain watched.

&nbsp;&nbsp;&nbsp;&nbsp;1.Given an expression that starts with ::, when a digest loop is entered and expression is dirty-checked, store the value as V
&nbsp;&nbsp;&nbsp;&nbsp;2.If V is not undefined, mark the result of the expression as stable and schedule a task to deregister the watch for this expression when we exit the digest loop
&nbsp;&nbsp;&nbsp;&nbsp;3.Process the digest loop as normal
&nbsp;&nbsp;&nbsp;&nbsp;4.When digest loop is done and all the values have settled, process the queue of watch deregistration tasks. For each watch to be deregistered, check if it still evaluates to a value that is not undefined. If that's the case, deregister the watch. Otherwise, keep dirty-checking the watch in the future digest loops by following the same algorithm starting from step 1
#####Special case for object literals
Unlike simple values, object-literals are watched until every key is defined. See http://www.bennadel.com/blog/2760-one-time-data-bindings-for-object-literal-expressions-in-angularjs-1-3.htm

译：
### 值稳定算法
只要该值未定义，一次性绑定表达式将在digest周期结束时保留该值。如果表达式的值在degist循环内设置，之后在同一个digest循环中，将其设置为undefined，则表达式不会被满足，并将保持监视状态。
给定以：：开头的表达式，当进入digest循环，并对表达式进行脏检查时，将该值存储为V,如果V不是undeinfed,将该表达式的结果标记为stable，并在我们退出digest循环时，安排任务取消注册此表达式的监视，正常处理digest 循环。
当digest循环结束，并且所有的值都被设置，处理监视撤销的任务队列。对于要取消注册的每一个监视，检查它是否仍然评估为未定义的值，如果是这种情况，取消监视，否则像步骤一一样开始相同的算法，在将来的digest循环中保持脏检查监视。
#####对象文字的特例？？？（onject literals）
与简单的值不同，object-literals会被监视，直到每个key都被定义。详见
http://www.bennadel.com/blog/2760-one-time-data-bindings-for-object-literal-expressions-in-angularjs-1-3.htm

原文：
###How to benefit from one-time binding
如何在一次性绑定中受益
If the expression will not change once set, it is a candidate for one-time binding. Here are three example cases.
如果表达式的值在设置之后不会改变，则他是一次性绑定的后选择。以下有三个案例：

When interpolating text or attributes:
插入文本或者属性时：
```
<div name="attr: {{::color}}">text: {{::name | uppercase}}</div>
```
When using a directive with bidirectional binding and parameters that will not change:
当在指令中使用双向绑定，并且参数不睡改变时：

```
someModule.directive('someDirective', function() {
  return {
    scope: {
      name: '=',
      color: '@'
    },
    template: '{{name}}: {{color}}'
  };
});
```
```
<div some-directive name="::myName" color="My color is {{::myColor}}"></div>
```
When using a directive that takes an expression:
使用带有表达式的指令时：
```
<ul>
  <li ng-repeat="item in ::items | orderBy:'name'">{{item.name}};</li>
</ul>
```
