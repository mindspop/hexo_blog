layout: post
title: 基于 Backbone.js 改造的 TodoMVC 介绍
tags:
- JavaScript
- Backbone
- MVC
categories:
- JavaScript Tech
---
> 前提说明：本篇是在学习`Backbone`的时候，实战练习写了一个 TodoMVC，并在原有代码上基础做了部分改进工作。

## 主要的改进
* 引入模块开发并使用`CommonJS`规范
* 使用`Webpack`工具加载和打包模块
* 将 HTML 模板独立成单一模块文件，同时让模块 JS 文件加载对应的 HTML 模板文件
* 细化了`View`层的颗粒度，将 View 拆解成：AppView、FooterView、TodoView、TodoListView
* 优化了 View 中`render`方法监听的事件类型，防止当`Model`或`Collection`发生一次变化时，View 多次重复渲染。「原例子中简单监听了`all`事件」
<!--more-->
{% asset_img todomvc_structure.png %}

## TodoMVC 架构分析
### 数据层
1. Todo `Model`
	* 存储每一个 Todo Item 的`标题`和`是否完成状态`
	* 有一个业务逻辑方法：改变`是否完成状态`
2. Todos `Collection`
	* 是一组 Todo Model 的集合
	* 负责 Todo 列表的排序
	* 负责过滤 Todo 列表中的数据「过滤剩余和完成的 Todo Item」 
	
### View 层
尽可能最小粒度地划分 View，即保证每个独立的 View 对应一个 Model 或 Collection。复杂的 View 由简单的 View 组合而成。这样可以提高开发灵活性，也有利于后期维度。
这种细分 View 的方式可以理解为：

      1. view-a1 + view-a2 = view-a
      2. view-b1 + view-b2 + view-b3 + view-b4 = view-b
      3. view-a + view-b + view-c = view-d

这种划分的规则:
>   * 每一个最小单位的 View 都只负责处理自身的内容
>   * 由其它子 View 组合而成的 View 则负责统筹更小单位的 View「通常父层级的 View 负责创建、显示、隐藏或删除子级别 View」
>	* View 与 View 之间的通信，则依靠 Model 或 Collection 这个中间体「比如用户操作某个 View1 而影响另外一个 View2，代码实现上是通过修改 View2 对应的数据层来间接刷新 View2」

#### 本次 TodoMVC 中对 View 的细分处理如下：
1. AppView
	* 负责组合所有 APP 内的子 View「判断是否需要增加、显示或隐藏某个组件 View」
	
			initialize: function () {
			
				// 新建 footer 视图
				app.footerView = new FooterView({
					collection: app.todos
				});
		
				// 新建 todolist 视图
				app.todoListView = new TodoListView({
					collection: app.todos
				});
			},
		
			render           : function () {
				this.renderMain();
				this.renderFooter();
			},
		
			//渲染 main 部分视图
			renderMain       : function () {
				if (app.todos.length) {
					this.$list.append(app.todoListView.el);
					this.$main.show();
		
					// 设置批量操作按钮显示状态
					this.toggleCheck();
				} else {
					this.$main.hide();
				}
			},
		
			// 渲染 footer 部分视图
			renderFooter     : function () {
				if (app.todos.length) {
					this.$footer.append(app.footerView.el);
					this.$footer.show();
				} else {
					this.$footer.hide();
				}
			},
2. TodoView
	* 负责每一个 Todo Item 的 View
3. TodoListView
	* 负责一组 Todo Items 的 View
4. FooterView
	* 负责 Footer 部分的 View

### 关联数据层和 View 层
1. TodoView + Todo Model
	* 将 TodoView 的方法绑定到对应 Todo Model 的事件中
	
			initialize: function () {
				this.listenTo(this.model, {
					'change' : this.render,
					'destroy': this.remove,
					'visible': this.toggleVisible
				});
		
				this.render();
			},
			
	* 利用 Todo Model 的数据渲染 TodoView 的模板
	
			var itemTpl = require('../../template/item-tpl.ejs');
			var TodoView = Backbone.View.extend({
				template: itemTpl,
				render       : function () {
					...
					
					var tmpl = this.template(this.model.toJSON());
					this.$el.html(tmpl);
					
					...
					return this;
				},
				...
			)};
			
	* Todo View 中一些方法可以修改 Model 数据
	
			// 改变 Model complete 状态
			toggleCompleted: function () {
				this.model.toggleCompleted();
			},
		
			// 清除 Model
			clear         : function () {
				this.model.destroy();
			},
			
2. TodoListView + Todos Collection
	* 将 TodoListView 的方法绑定到对应 Todos Collection 的事件中
			
			initialize: function () {
				this.listenTo(this.collection, {
					'add'             : this.addOne,
					'reset'           : this.addAll,
					'change:completed': this.filterOne,
					'filter'          : this.filterAll,
					'destroy'         : this.render
				});
			},
			
	* TodoListView 可以访问 Todos Collection, 从而间接访问其中的 Model
			
			filterOne : function (todo) {
				todo.trigger('visible');
			},
			filterAll : function () {
				this.collection.each(this.filterOne, this);
			}
			
3. FooterView + Todos Collection
	* 将 FooterView 的方法绑定到对应 Todos Collection 的事件中
			
			initialize: function () {
				this.listenTo(this.collection, 'add remove reset change:completed', this.render);
				this.listenTo(this.collection, 'filter', this.setSelectedTab);
			},

	* FooterView 可以访问 Todos Collection
	
			// 清除已经完成的 todoitem
			clearCompleted: function () {
				_.invoke(this.collection.completed(), 'destroy');
			}
		
	* 利用 Todos Collection 的数据渲染 FooterView
			
			var footerTpl = require('../../template/footer-tpl.ejs');

			var FooterView = Backbone.View.extend({
				template: footerTpl,
				...
				render        : function () {
			
					// 提取当前所绑定的 collection 数据的状态
					var remaining = this.collection.where({
						completed: false
					}).length;
					var completed = this.collection.where({
						completed: true
					}).length;
					
					// 利用获取的 collection 数据渲染模板
					var footerView = this.template({
						remaining: remaining,
						completed: completed
					});
			
					this.$el.html(footerView);
			
					...
				},
				...
			});	
			
			
			
			
			