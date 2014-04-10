---
layout: post
title: "Objective-C&amp;XCode注释的正确写法"
date: 2014-02-07 18:02
comments: true
categories: iOS
---

注释对于C体系的同学来说还是不太规范，对于XCode的文档系统来说，规范还是有帮助的，能在QuickHelp等提示中反应出来。所以还是多注意下会比较好。

推荐以下两种写法(主是方法注释，其它的以后遇到再补充吧)

{% codeblock %}
/**
 *	@brief update table
 *
 *	@param 	model 	you want to update the entity
 *	@param 	where 	can use NSString or NSDictionary or nil
                    when "where" is nil : update the value based on rowid column or primary key column
 *
 *	@return	the updated was successful
 */
-(BOOL)updateToDB:(NSObject *)model where:(id)where;
{% endcodeblock %}

{% codeblock %}
/**
 @brief   每个Section的高度，默认为 0
 @param   niDropDown: NIDropDown，用于区分不同的NI
 @param   indexPath:  定位Section
 @return  高度
 */
- (CGFloat)niDropDown:(NIDropDown *)niDropDown heightForHeaderInSection:(NSInteger)section;
{% endcodeblock %}