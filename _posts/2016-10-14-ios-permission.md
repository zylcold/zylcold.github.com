---
layout: post
title: "iOS permission"
description: ""
category: 
tags: [iOS]
---
{% include JB/setup %}

## 访问相册权限

1. plist配置

	key: NSPhotoLibraryUsageDescription 
	
2. 请求权限

	#import <Photos/Photos.h>

	PHAuthorizationStatus status = [PHPhotoLibrary authorizationStatus];
    if(status == PHAuthorizationStatusNotDetermined) {
        [PHPhotoLibrary requestAuthorization:^(PHAuthorizationStatus status) {
            if(status == PHAuthorizationStatusAuthorized) {
                NSLog(@"-->success");
            }
        }];
    }
	
## 访问定位权限

1. plist配置
	key: NSLocationWhenInUseUsageDescription
2. 请求权限

	BOOL enable = [CLLocationManager locationServicesEnabled];
	    CLAuthorizationStatus status = [CLLocationManager authorizationStatus];
	    if(!enable || status < 3) {
	        self.locationManager = [[CLLocationManager alloc] init];
	        self.locationManager requestWhenInUseAuthorization];
	    }