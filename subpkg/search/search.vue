<template>
  <view>
    <view class="search-box">
      <!-- 使用 uni-ui 提供的搜索组件 -->
      <uni-search-bar @input="input" :radius="100" cancelButton="none" placeholder='请输入搜索内容'
        :focus="true"></uni-search-bar>
    </view>

    <!-- 搜索历史 -->
    <view class="history-box" v-if="searchResults.length === 0">
      <!-- 标题区域 -->
      <view class="history-title">
        <text>搜索历史</text>
        <uni-icons type="trash" size="17" @click="cleanHistory"></uni-icons>
      </view>
      <!-- 列表区域 -->
      <view class="history-list">
        <uni-tag :text="item" v-for="(item, i) in historyList" :key="i" circle=true
          @click="gotoGoodsList(item)"></uni-tag>
      </view>
    </view>

    <!-- 搜索建议列表 -->
    <view class="sugg-list" v-else>
      <view class="sugg-item" v-for="(item, i) in searchResults" :key="i" @click="gotoDetail(item.goods_id)">
        <view class="goods-name">{{item.goods_name}}</view>
        <uni-icons type="arrowright" size="16"></uni-icons>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        // 延时器的 timerId
        timer: null,
        // 搜索关键词
        kw: '',
        // 搜索结果列表
        searchResults: [],
        // 搜索关键词的历史记录
        historyList: []
      };
    },
    onLoad() {
      this.historyList = JSON.parse(uni.getStorageSync('kw') || '[]')
    },
    computed: {
      // historys() {
      //   return [...this.historyList].reverse()
      // }
    },
    methods: {
      input(e) {
        // 清除 timer 对应的延时器
        clearTimeout(this.timer)
        // 重新启动一个延时器，并把 timerId 赋值给 this.timer
        this.timer = setTimeout(() => {
          // 如果 500 毫秒内，没有触发新的输入事件，则为搜索关键词赋值
          this.kw = e.trim()
          // 根据关键词，查询搜索建议列表
          this.getSearchList()
        }, 500)
      },
      // 获取搜索是提示的建议
      async getSearchList() {
        // 判断关键词是否为空
        if (this.kw === '') {
          this.searchResults = []
          return
        }
        const {
          data: result
        } = await uni.$http.get('/api/public/v1/goods/qsearch', {
          query: this.kw
        })
        if (result.meta.status !== 200) return uni.$showMsg()
        this.searchResults = result.message
        // 调用保存方法
        this.saveSearchHistory()
      },
      // 点击建议跳转
      gotoDetail(goods_id) {
        uni.navigateTo({
          url: '/subpkg/goods_detail/goods_detail?goods_id=' + goods_id
        })
      },
      // 点击跳转到商品列表页面
      gotoGoodsList(kw) {
        uni.navigateTo({
          url: '/subpkg/goods_list/goods_list?query=' + kw
        })
      },
      // 保存搜索关键字的方法
      saveSearchHistory() {
        // set方法
        // const set = new Set(this.historyList)
        // set.delete(this.kw)
        // set.add(this.kw)
        // this.historyList = Array.from(set)
        if (!this.historyList.includes(this.kw)) this.historyList.unshift(this.kw)
        // 调用 uni.setStorageSync(key, value) 将搜索历史记录持久化存储到本地
        uni.setStorageSync('kw', JSON.stringify(this.historyList))
      },
      // 清楚所有的搜索记录 
      cleanHistory() {
        // 清空 data 中保存的搜索历史
        this.historyList = []
        // 清空本地存储中记录的搜索历史
        uni.removeStorageSync('kw')
      }
    }
  }
</script>

<style lang="scss">
  .search-box {
    position: sticky;
    top: 0;
    z-index: 999;
    background-color: #c00000;
  }

  .sugg-list {
    padding: 5px 10px 0;

    .sugg-item {
      display: flex;
      align-items: center;
      justify-content: space-between;
      font-size: 12px;
      padding: 13px 0;
      border-bottom: 1px solid #efefef;

      .goods-name {
        // 文字不允许换行（单行文本）
        white-space: nowrap;
        // 溢出部分隐藏
        overflow: hidden;
        // 文本溢出后，使用 ... 代替
        text-overflow: ellipsis;
        margin-right: 5px;
      }
    }
  }

  .history-box {
    margin: 0 10px;

    .history-title {
      display: flex;
      align-items: center;
      justify-content: space-between;
      height: 40px;
      font-size: 14px;
      border-bottom: 1px solid #efefef;
    }

    .history-list {
      display: flex;
      flex-wrap: wrap;

      uni-tag {
        margin: 10px 8px 0 0;
        background-color: #efefef;
      }
    }
  }
</style>