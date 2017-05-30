Host Element
===
在介绍 HostListener 和 HostBinding 属性装饰器之前，我们先来了解一下 host element (宿主元素)。

宿主元素的概念同时适用于指令和组件。对于指令来说，这个概念是相当简单的。应用指令的元素，就是宿主元素。假设我们已声明了一个 `HighlightDirective` 指令 `(selector: '[exeHighlight]')`：
```html
<p exeHighlight>
   <span>高亮的文本</span>
</p>
```
上面 html 代码中，p 元素就是宿主元素。如果该指令应用于自定义组件中如：
```html
<exe-counter exeHighlight>
    <span>高亮的文本</span>
</exe-counter>
```
此时 `exe-counter` 自定义元素，就是宿主元素。

HostListener
===
`HostListener` 是属性装饰器，用来为宿主元素添加事件监听。

`HostListenerDecorator` 装饰器定义
==
```
export interface HostListenerDecorator {
    (eventName: string, args?: string[]): any;
    new (eventName: string, args?: string[]): any;
}
```
`HostListenerDecorator` 装饰器应用
==
counting.directive.ts
```typescript
import { Directive, HostListener } from '@angular/core';

@Directive({
    selector: 'button[counting]'
})
class CountClicks {
    numberOfClicks = 0;

    @HostListener('click', ['$event.target'])
    onClick(btn: HTMLElement) {
        console.log('button', btn, 'number of clicks:', this.numberOfClicks++);
    }
}
```
app.component.ts

```typescript
import { Component} from '@angular/core';

@Component({
  selector: 'exe-app',
  styles: [`
    button {
      background: blue;
      color: white;
      border: 1px solid #eee;
    }
  `],
  template: `
    <button counting>增加点击次数</button>
  `
})
export class AppComponent {}
```
以上代码运行后浏览器显示的结果：



此外，我们也可以监听宿主元素外，其它对象产生的事件，如 window 或 document 对象。具体示例如下：

highlight.directive.ts
===
```typescript
import { Directive, HostListener, ElementRef, Renderer } from '@angular/core';

@Directive({
    selector: '[exeHighlight]'
})
export class ExeHighlight {
    constructor(private el: ElementRef, private renderer: Renderer) { }

    @HostListener('document:click', ['$event'])
    onClick(btn: Event) {
        if (this.el.nativeElement.contains(event.target)) {
            this.highlight('yellow');
        } else {
            this.highlight(null);
        }
    }

    highlight(color: string) {
        this.renderer.setElementStyle(this.el.nativeElement, 'backgroundColor', color);
    }
}
```
app.component.ts
```typescript
import { Component} from '@angular/core';

@Component({
  selector: 'exe-app',
  template: `
    <h4 exeHighlight>点击该区域，元素会被高亮。点击其它区域，元素会取消高亮</h4>
  `
})
export class AppComponent {}
```
以上代码运行后浏览器显示的结果：



Host Event Listener
===
我们也可以在指令的 `metadata` 信息中，设定宿主元素的事件监听信息，具体示例如下：

counting.directive.ts
```typescript
import { Directive } from '@angular/core';

@Directive({
    selector: 'button[counting]',
    host: {
      '(click)': 'onClick($event.target)'
    }
})
export class CountClicks {
    numberOfClicks = 0;

    onClick(btn: HTMLElement) {
        console.log('button', btn, 'number of clicks:', this.numberOfClicks++);
    }
}
```
HostBinding
===
HostBinding 是属性装饰器，用来动态设置宿主元素的属性值。

HostBinding 装饰器定义
===
```typescript
export interface HostBindingDecorator {
    (hostPropertyName?: string): any;
    new (hostPropertyName?: string): any;
}
```
HostBinding 装饰器应用
===
button-press.directive.ts
```typescript
import { Directive, HostBinding, HostListener } from '@angular/core';

@Directive({
    selector: '[exeButtonPress]'
})
export class ExeButtonPress {
    @HostBinding('attr.role') role = 'button';
    @HostBinding('class.pressed') isPressed: boolean;

    @HostListener('mousedown') hasPressed() {
        this.isPressed = true;
    }
    @HostListener('mouseup') hasReleased() {
        this.isPressed = false;
    }
}
```
app.component.ts
```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'exe-app',
  styles: [`
    button {
      background: blue;
      color: white;
      border: 1px solid #eee;
    }
    button.pressed {
      background: red;
    }
  `],
  template: `
    <button exeButtonPress>按下按钮</button>
  `
})
export class AppComponent { }
```
以上代码运行后浏览器显示的结果：



Host Property Bindings
===
我们也可以在指令的 metadata 信息中，设定宿主元素的属性绑定信息，具体示例如下：

button-press.directive.ts
```typescript
import { Directive, HostListener } from '@angular/core';

@Directive({
    selector: '[exeButtonPress]',
    host: {
      'role': 'button',
      '[class.pressed]': 'isPressed'
    }
})
export class ExeButtonPress {
    isPressed: boolean;

    @HostListener('mousedown') hasPressed() {
        this.isPressed = true;
    }
    @HostListener('mouseup') hasReleased() {
        this.isPressed = false;
    }
}
```
* 我有话说  
1.宿主元素属性和事件绑定风格指南

优先使用 `@HostListener` 和 `@HostBinding` ，而不是 `@Directive` 和 `@Component` 装饰器的 `host` 属性：

对于关联到 `@HostBinding` 的属性或关联到 `@HostListener` 的方法，要修改时，只需在指令类中的一个地方修改。 如果使用元数据属性 host，你就得在组件类中修改属性声明的同时修改相关的元数据。