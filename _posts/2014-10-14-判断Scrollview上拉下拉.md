---
layout: post
title: 判断Scrollview上拉下拉
date: 2014-10-14 12:03:30
tags: ScrollView滚动
excerpt: "ScrollView滚动问题."
comments: true
---

#关于判断ScrollView上拉下拉问题

{% highlight Objective-c %}
PushListView.h
    
@interface PushListView : UIView<UITableViewDelegate,UITableViewDataSource
{
	     CGFloat contentOffsetY;
	
	     CGFloat oldContentOffsetY;
	
	     CGFloat newContentOffsetY;
	}
{% endhighlight %}

{% highlight Objective-c %}
PushListView.m
    
	//开始拖拽视图
- (void)scrollViewWillBeginDragging:(UIScrollView *)scrollView
	{

    contentOffsetY = scrollView.contentOffset.y;

	}
{% endhighlight %}

{% highlight Objective-c %}
	 // 滚动时调用此方法(手指离开屏幕后)
- (void)scrollViewDidScroll:(UIScrollView *)scrollView{
    //NSLog(@"scrollView.contentOffset:%f, %f", scrollView.contentOffset.x, scrollView.contentOffset.y);
    newContentOffsetY = scrollView.contentOffset.y;
    if (newContentOffsetY > oldContentOffsetY && oldContentOffsetY > contentOffsetY) {  // 向上滚动
        NSLog(@"up");
    } else if (newContentOffsetY < oldContentOffsetY && oldContentOffsetY < contentOffsetY) { // 向下滚动
        NSLog(@"down");
    } else {
        NSLog(@"dragging");
    }

    if (scrollView.dragging) {  // 拖拽
        NSLog(@"scrollView.dragging");
        NSLog(@"contentOffsetY: %f", contentOffsetY);
        NSLog(@"newContentOffsetY: %f", scrollView.contentOffset.y);
        if ((scrollView.contentOffset.y - contentOffsetY) > 5.0f) {  
        	// 向上拖拽
            // 隐藏导航栏和选项栏
            // [self layoutView];
            [_viewController.navigationController setNavigationBarHidden:YES animated:YES];
            //[_viewController.navigationController setToolbarHidden:!hidden animated:YES];
            _viewController.tabBarController.tabBar.hidden = YES;
        } else if ((contentOffsetY - scrollView.contentOffset.y) > 5.0f) {   		// 向下拖拽
            // 显示导航栏和选项栏
            //[self layoutView];
            [_viewController.navigationController setNavigationBarHidden:NO animated:YES];
            //[_viewController.navigationController setToolbarHidden:NO animated:YES];
        } else {
        
        }
    }
}
{% endhighlight %}

{% highlight Objective-c %}
// 完成拖拽(滚动停止时调用此方法，手指离开屏幕前)	
- (void)scrollViewDidEndDragging:(UIScrollView *)scrollView willDecelerate:(BOOL)decelerate{
	    // NSLog(@"scrollViewDidEndDragging");
	    oldContentOffsetY = scrollView.contentOffset.y;
	}
{% endhighlight %}
