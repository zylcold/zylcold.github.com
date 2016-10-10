---
layout: post
title: "使用Cocoapods发布"
description: ""
category: 
tags: [init]
---
{% include JB/setup %}


## 1. 创建spec

	pod spec creat

	example:

	Pod::Spec.new do |s|

	  s.name         = "ZYLCustomAlertView"
	  s.version      = "0.0.3"
	  s.summary      = "CustomAlertView"

	  s.description  = <<-DESC
	                   This is a CustomAlertView, That can Show a Custom View with animation.
	                   Supported Bottom, Center to Show.
	                   DESC
	  s.homepage     = "https://github.com/zylcold"

	  # ―――  Spec License  ――――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
	  s.license      = { :type => "MIT", :file => "LICENSE" }

	  # ――― Author Metadata  ――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
	  s.author             = { "zylcold" => "zylcold@163.com" }
	  s.social_media_url   = "https://zylcold.github.io"

	  # ――― Platform Specifics ――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
	  s.platform     = :ios, "7.0"

	  # ――― Source Location ―――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
	  s.source       = { :git => "https://github.com/zylcold/ZYLCustomAlertView.git", :tag => s.version }

	  # ――― Source Code ―――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #

	  s.source_files  = "ZYLCustomAlertView.{h,m}"
	  s.ios.framework  = 'UIKit'

	  # ――― Project Settings ――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
	  s.requires_arc = true

	end


## 2. 检查spec

	pod spec lint

## 3. 创建账户

	pod trunk register orta@cocoapods.org 'Orta Therox' --description='macbook air'

## 4. 提交spec

	pod trunk push [NAME.podspec]

## 5. 更新spec
	
	1> 修改spec
	2> pod spec lint
	3> pod trunk push NAME.podspec
