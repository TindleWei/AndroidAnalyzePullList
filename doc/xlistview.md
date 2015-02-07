Android原理——下拉刷新的多种实现
---------------------------
先说4.x的ListView：  

在XListView中，
实现接口 OnScrollListener;  
OnScrollListener来自android.widget.AbsListView,是一个接口类：

	public interface OnScrollListener {

        public static int SCROLL_STATE_IDLE = 0;

        public static int SCROLL_STATE_TOUCH_SCROLL = 1;

        public static int SCROLL_STATE_FLING = 2;

        public void onScrollStateChanged(AbsListView view, int scrollState);

        public void onScroll(AbsListView view, int firstVisibleItem, int visibleItemCount,
                int totalItemCount);
    }


里一个内部接口类是 OnXScrollListener:

	public interface IXListViewListener {
		public void onRefresh();
		public void onLoadMore();
	}

关于内部实现

	public interface OnXScrollListener extends OnScrollListener {
		public void onXScrolling(View view);
	}
	
这个方法需要探究一下：在代码中多次用到

	private void invokeOnScrolling() {
		if (mScrollListener instanceof OnXScrollListener) {
			OnXScrollListener l = (OnXScrollListener) mScrollListener;
			l.onXScrolling(this);
		}
	}
	
**当我尝试删除不必要的代码**  

+ 在`onTouchEvent`和`computeScroll`中删除`invokeOnScrolling()`,  
暂时没有任何影响

+ 在`initWithContext`中删除 `getViewTreeObserver().removeGlobalOnLayoutListener()`  
暂时没有任何影响
  
+ 尝试删除 `mHeaderView.getViewTreeObserver().addOnGlobalLayoutListener{}`  
出错：下拉刷新不会有停留时间

+ 尝试删除 `computeScroll()`中的代码  
出错：下拉上拉无法调整高度

+ 尝试删除`interface OnXScrollListener extends OnScrollListener`  
可以去掉，影响不大 

+ 尝试删除`onScrollStateChanged()`中的代码  
暂时没有任何影响，但决定先保留着
