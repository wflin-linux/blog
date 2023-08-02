---
title: JavaScript 常用函数
date: 2023-2-18
author: wflin
tags:
  - vue
  - javascript
top: false
cover: false
toc: true
mathjax: true
summary: "vue"
categories:
  - javascript
---

## JavaScript 下拉加载防抖节流函数

## 防抖函数

```javascript
debounce(fn, delay) {
    let timer = null;
    return function() {
        const context = this;
        const args = arguments;
        clearTimeout(timer);
        timer = setTimeout(function() {
            fn.apply(context, args);
        }, delay);
    };
},
```

## 节流函数

```javascript
throttle(fn, delay) {
    let timer = null;
    let lastTime = 0;
    return function() {
        const context = this;
        const args = arguments;
        const nowTime = new Date().getTime();
        if (nowTime - lastTime >= delay) {
            fn.apply(context, args);
            lastTime = nowTime;
        }
    };
},
```

## 触底函数

```javascript
handleScroll() {
    const scrollTop =
          document.documentElement.scrollTop || document.body.scrollTop;
    const scrollHeight = document.documentElement.scrollHeight;
    const clientHeight = document.documentElement.clientHeight;
    if (
        scrollTop + clientHeight >= scrollHeight - 5 &&
        !this.isLoading &&
        !this.isFinished
    ) {
        this.loadData();
    }
},
```

## vue中使用以上函数进行滚动加载

```javascript
loadData() {
    this.isLoading = true;
    // 调用 API 获取数据
    const params = {
        page: this.currentPage,
        size: this.pageSize,
    };
    fetchData(params).then((res) => {
        if (res.data.length) {
            this.dataList = this.dataList.concat(res.data);
            this.currentPage++;
        } else {
            this.isFinished = true;
        }
        this.isLoading = false;
    });
},
},
```









```javascript
export default {
  data() {
    return {
      isLoading: false, // 是否正在加载数据
      isFinished: false, // 是否已经加载完成所有数据
      currentPage: 1, // 当前页码
      pageSize: 10, // 每页数据条数
      dataList: [], // 加载的数据列表
    };
  },

  methods: {
    // 防抖函数，防止用户频繁触发滚动事件
    debounce(fn, delay) {
      let timer = null;
      return function() {
        const context = this;
        const args = arguments;
        clearTimeout(timer);
        timer = setTimeout(function() {
          fn.apply(context, args);
        }, delay);
      };
    },

    // 节流函数，限制函数执行频率
    throttle(fn, delay) {
      let timer = null;
      let lastTime = 0;
      return function() {
        const context = this;
        const args = arguments;
        const nowTime = new Date().getTime();
        if (nowTime - lastTime >= delay) {
          fn.apply(context, args);
          lastTime = nowTime;
        }
      };
    },

    // 滚动事件处理函数
    handleScroll() {
      const scrollTop =
        document.documentElement.scrollTop || document.body.scrollTop;
      const scrollHeight = document.documentElement.scrollHeight;
      const clientHeight = document.documentElement.clientHeight;
      if (
        scrollTop + clientHeight >= scrollHeight - 5 &&
        !this.isLoading &&
        !this.isFinished
      ) {
        this.loadData();
      }
    },

    // 加载数据
    loadData() {
      this.isLoading = true;
      // 调用 API 获取数据
      const params = {
        page: this.currentPage,
        size: this.pageSize,
      };
      fetchData(params).then((res) => {
        if (res.data.length) {
          this.dataList = this.dataList.concat(res.data);
          this.currentPage++;
        } else {
          this.isFinished = true;
        }
        this.isLoading = false;
      });
    },
  },

  mounted() {
    // 监听滚动事件
    const debouncedScroll = this.debounce(this.handleScroll, 300);
    const throttledScroll = this.throttle(debouncedScroll, 500);
    window.addEventListener("scroll", throttledScroll);
  },

  beforeDestroy() {
    // 取消滚动事件监听
    window.removeEventListener("scroll", this.handleScroll);
  },
};

```

