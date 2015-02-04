Android原理——下拉刷新的多种实现
--------
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
	
分别在`onTouchEvent`和`computeScroll`中
当我注释掉`invokeOnScrolling()`，确实出现了异常。  
异常情况：上拉刷新后，列表前一部分item消失，而后面会出现新的item，item显示的下标相应增加。  
异常原因：暂时不清楚。