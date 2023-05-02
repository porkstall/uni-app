<template>
  <view>
<<<<<<< Updated upstream
    cate
=======
    <!-- 使用自定义组件 搜索框-->
    <my-search @myclick="gotoSearch"></my-search>
    <view class="scroll-view-container">
      <!-- 左侧的滚动视图区域 -->
      <scroll-view class="left-scroll-view" scroll-y :style="{height: wh + 'px'}">
        <block v-for="(item,index) in cateList" :key="index">
          <view :class="['left-scroll-view-item', index === active?'active':'']" @click="activeChanged(index)">
            {{item.cat_name}}
          </view>
        </block>
      </scroll-view>
      <!-- 右侧的滚动视图区域 -->
      <scroll-view class="right-scroll-view" scroll-y :style="{height: wh + 'px'}" :scroll-top="scrollTop">
        <block v-for="(item2,index) in cateLevel2" :key="index">
          <view class="cate-lv2-title">/ {{item2.cat_name}} /</view>
          <!-- 动态渲染三级分类的列表数据 -->
          <view class="cate-lv3-list">
            <!-- 三级分类 Item 项 -->
            <view class="cate-lv3-item" v-for="(item3, i3) in item2.children" :key="i3" @click="gotoGoodsList(item3)">
              <!-- 图片 -->
              <image :src="item3.cat_icon"></image>
              <!-- 文本 -->
              <text>{{item3.cat_name}}</text>
            </view>
          </view>
        </block>
      </scroll-view>
    </view>
>>>>>>> Stashed changes
  </view>
</template>

<script>
  export default {
    data() {
      return {

      };
<<<<<<< Updated upstream
=======
    },
    onLoad() {
      // 获取当前系统的信息
      const sysInfo = uni.getSystemInfoSync()
      // 为 wh 窗口可用高度动态赋值
      this.wh = sysInfo.windowHeight - 50
      this.getCateList()
    },
    methods: {
      // 获取左侧滑动数据
      async getCateList() {
        const {
          data: result
        } = await uni.$http.get('/api/public/v1/categories')
        if (result.meta.status !== 200) return uni.$showMsg()
        this.cateList = result.message
        // 为二级分类赋值
        this.cateLevel2 = result.message[0].children
      },
      // 点击添加红色条子
      activeChanged(index) {
        this.active = index
        // 为二级分类列表重新赋值
        this.cateLevel2 = this.cateList[index].children
        // 让 scrollTop 的值在 0 与 1 之间切换
        this.scrollTop = this.scrollTop === 0 ? 1 : 0
      },
      // 点击三级分类项跳转到商品列表页面
      gotoGoodsList(item3) {
        uni.navigateTo({
          url: '/subpkg/goods_list/goods_list?cid=' + item3.cat_id
        })
      },
      // 子传父点击搜索跳转
      gotoSearch() {
        uni.navigateTo({
          url: '/subpkg/search/search'
        })
      }
>>>>>>> Stashed changes
    }
  }
</script>

<style lang="scss">

</style>