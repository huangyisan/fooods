<template>
  <view class="foods-wrapper" id="ccc">
    <scroll-view scroll-y="true" class="side-left-wrapper">
      <block v-for="(item,index) in foodsCategory" :key="index">
        <block v-for="(des,title) in item" :key="title">
          <!-- 第一个元素展现样式 -->
          <view v-if="index === 0"
            :class='["side-left-item", "active", "inner_" + index]'
            :data-index="index"
			:data-toprpx="toprpx"
            @click="leftItem.btnClick"
          >
            <text>{{title.trim()}}</text>
          </view>

          <view v-else
            :class='["side-left-item", "inner_" + index]'
            :data-index="index"
			:data-toprpx="toprpx"
            @click="leftItem.btnClick"
          >
            <text>{{title.trim()}}</text>
          </view>
        </block>
      </block>
    </scroll-view>

    <scroll-view
      scroll-y="true"
      class="side-right-wrapper"
      :scroll-into-view="scrollTopId"
      :scroll-top="scrollTop"
      :data-toprpx="toprpx"
      scroll-with-animation="true"
      @scroll="leftItem.scrollInfo"
    >
      <block v-for="(item,index) in foodsCategory" :key="index">
        <block v-for="(des,title) in item" :key="title">
          <view class="item-title" :id='"item_" + index'>
						<text class="title-content">{{title + ' '}}</text>
						<text class="title-des">{{des}}</text>
					</view>
          <block v-for="(iten, indey) in foods[title]" :key="indey">
						<view class="food-info-wrapper">
						<image class="item-pic" :src="iten.img" alt="" lazy-load=true @load="ifLoad">
						<view class="food-info">
            <text class="item-name">{{iten.name}}</text>
            <text class="item-des">{{iten.description.trim()}}</text>
            <text class="item-rate">月售{{iten.month_sales + '份' + ' ' + '好评率' + iten.satisfy_rate + '%'}}</text>
            <!-- <text class="item-materials">{{iten.materials}}</text> -->
            <!-- <text class="item-satisfy-rate">{{iten.satisfy_rate}}</text> -->
						<view class='price-cart-add'>
            <text class="item-lowest-price">{{'￥' + iten.lowest_price}}</text>
            <!-- 点击后追加购物车 -->
						<image class="item-cart-add-icon" src="../../static/img/icon/cart_add.svg" alt="" :data-info='[iten.name, iten.item_id, iten.lowest_price]' @click="foodAdd">		
						</view>
						</view>
						</view>
          </block>
        </block>
      </block>
    </scroll-view>
  </view>
</template>

<script module="leftItem" lang="wxs">

  function btnClick(event, ins) {
    var owner = ins.selectAllComponents('.side-left-item')
    for (var i = 0; i < owner.length; i++) {
      owner[i].removeClass('active');
    }
    var instance = ins.selectComponent('.inner_' + event.currentTarget.dataset.index)
    instance.addClass('active')
	
	// 计算之后的高度用来对比
	var dataset = event.instance.getDataset();
	var wxsToprpx = dataset.toprpx
	var index = dataset.index
	if(index === 0){
		ins.callMethod("setScrollTopId", "item_0")
	}else if(index === 1){
		ins.callMethod("setScrollTopId", "item_1")
	}else if (index === 2){
		ins.callMethod("setScrollTopId", "item_2")
	}
	var newWxsToprpx = []
	var firstToprpx = wxsToprpx[0]
	for(var i=0; i<wxsToprpx.length; i++){
		newWxsToprpx.push(wxsToprpx[i] - firstToprpx)
	}
	
	var n1 = newWxsToprpx[0]
	var n2 = newWxsToprpx[1]
	var n3 = newWxsToprpx[2]
	
  }



  function scrollInfo(event,ins) {
    // toprpx = ins.callMethod("reToprpx")
	var dataset = event.instance.getDataset();
	// 当前滚动距离
	var wxsScrollTop = event.detail.scrollTop
	var wxsToprpx = dataset.toprpx
	var firstToprpx = wxsToprpx[0]
	// 计算之后的高度用来对比
	var newWxsToprpx = []
	for(var i=0; i<wxsToprpx.length; i++){
		newWxsToprpx.push(wxsToprpx[i] - firstToprpx - 1)
	}
	var n1 = newWxsToprpx[0]
	var n2 = newWxsToprpx[1]
    var n3 = newWxsToprpx[2]
  
  var youhui = ins.selectComponent('.inner_0')
  var renqi = ins.selectComponent('.inner_1')
  var rexiao = ins.selectComponent('.inner_2')
	
	if (wxsScrollTop >= 0 && wxsScrollTop < n2){
    var youhui = ins.selectComponent('.inner_0')
    youhui.addClass('active')
    renqi.removeClass('active')
    rexiao.removeClass('active')
	}else if(wxsScrollTop >= n2 && wxsScrollTop < n3){
    youhui.removeClass('active')
    renqi.addClass('active')
    rexiao.removeClass('active')
  }else if(wxsScrollTop >= n3){
    youhui.removeClass('active')
    renqi.removeClass('active')
    rexiao.addClass('active')
  }
  }
  module.exports = {
    btnClick: btnClick,
    scrollInfo: scrollInfo
  }



</script>

<script>

export default {
  components: {
  },
  props: {
    foodsCategory: {
      type: Array,
      default() {
        return [];
      }
    },
    foods: {
      type: Object,
      default() {
        return {};
      }
    },
    testMessage: ""
  },
  data() {
    return {
      currentIndex: 0,
      scrollTop: 0,
      // 存放组件自身高度
      toprpx: [],
      scrollTopId: "item_0",
      cartList: {},
    };
  },
  methods: {
    // 左侧点击, 改变样式, 并且同步右侧内容
    itemClick(index) {
      // console.log(this.currentIndex)
      this.currentIndex = index;
      // this.scrollTop = -400
      switch (index) {
        case 0:
          this.scrollTopId = "vegetables";
          break;
        case 1:
          this.scrollTopId = "fruit";
          break;
        case 2:
          this.scrollTopId = "wine";
      }
    },
    // + 图片点击追加
    foodAdd(event) {
        // let num = 0
        let itemName, itemId, itemPrice, itemNum, isFood
        [itemName, itemId, itemPrice, itemNum] = event.currentTarget.dataset.info
        isFood = this.cartList[itemId]
        console.log(this.cartList[itemId])
        if (isFood){
          itemNum = isFood.item_num
          itemNum += 1
        }else{
          itemNum = 1
        }
        this.cartList[itemId] = {item_name: itemName, item_price: itemPrice, item_num:itemNum, item_id: itemId}
        console.log(this.cartList)
    },

    // resetCurrentIndex(){
    //   this.currentIndex = null
    // },

    getToprpx(selector) {
      if (this.toprpx.length === 0){
      const titleTopArray = []
      const query = uni.createSelectorQuery().in(this);
      query.selectAll(selector).boundingClientRect(data => {
        for(let i = 0; i<data.length; i++){
          const top = data[i].top
          if (top > 0) {
            titleTopArray.push(top)
          }
        }
      }).exec();
	  // titleTopArray = titleTopArray.map(x => x-firstTitleTopArray)
      this.toprpx = titleTopArray
      // console.log(this.toprpx)
      }
    },
	
	setScrollTopId(id) {
		this.scrollTopId = id
	},

    reToprpx(){
      return this.toprpx
    },

    // 监听滚动
    scrollInfo(position) {
      // console.log(this.toprpx);
      let gapOne = this.toprpx[1] - this.toprpx[0];
      let gapTow = this.toprpx[2] - this.toprpx[0];
      if (position.target.scrollTop < gapOne) {
        this.currentIndex = 0;
        this.scrollTopId = "default";
      } else if (
        position.target.scrollTop > gapOne &&
        position.target.scrollTop < gapTow
      ) {
        this.currentIndex = 1;
        this.scrollTopId = "default";
      } else if (position.target.scrollTop > gapTow) {
        this.currentIndex = 2;
        this.scrollTopId = "default";
      }
    },

    ifLoad(){
      this.getToprpx(".item-title");
    }
  },

  mounted() {
    // this.$nextTick(() => {
    //   // this.getToprpx("#title");
    //   this.getToprpx("#热销");
    // })
    // setTimeout(() => {
    //   this.getToprpx("#title");
    // },2000)
    
    
    // this.getToprpx("#人气搭配 誉村套餐");
    // 

    // console.log(this.foodsCategory);
    // console.log(this.testMessage);
    // console.log(this.toprpx);
  },
};
</script>

<style scoped>


.foods-wrapper {
  display: flex;
  flex-direction: row;
  position: absolute;
  /* background-color: #C03189; */
  width: 100%;
  top: 350rpx;
  bottom: 88rpx;
}

.side-left-wrapper {

  /* background-color: #F0AD4E; */
  flex: 1;
}

.side-right-wrapper {
  /* background-color: #829fff; */
  flex: 4;
  box-shadow: 0 1px 1px rgba(100,100,100,.1);
}

.side-left-item {
/* 用flex让text文字居中 */
  display: flex;
  flex-direction: column;
  justify-content: center;
  position: relative;
  background: #f8f8f8;
  height: 60rpx;
  padding: 20rpx 0;
  font-size: 22rpx;
  color: #999;
  /* 换行 */
  word-break: break-all;
  white-space: normal;
  text-overflow: ellipsis;
}

.side-left-item text{
  position: absolute;
  left:10rpx;
  right:10rpx;

}

.active {
  color: #333;
  background-color: #ffffff;
}

.mytext {
  display: inline-block;
  background-color: #007aff;
}

/* 右侧栏标题 */
.item-title {
	padding: 2vw 8vw 2vw 0;
	margin-left: 2.666667vw;
}

.title-content {
  color: #666;
	font-weight: 700;
	font-size: 24rpx;
}

.title-des {
    /* flex: 1; */
    color: #999;
    font-size: 20rpx;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
}

/* foods style */
  .food-info-wrapper {
		
    padding: 2.666667vw 0;
    padding-left: 2.666667vw;

    margin-bottom: .133333vw;

    display: flex;

    min-height: 27.466667vw;
    position: relative;
  }
	.item-pic {
		width: 140rpx;
		height: 140rpx;
		margin-right: 20rpx;
		vertical-align: middle;
		border-radius: .533333vw;
		flex: 1
	}

	.food-info {
		display: flex;
		flex-direction: column;
		flex: 3
	}

	.item-name {
		font-weight: 700;
    overflow: hidden;
    font-size: 24rpx;
    white-space: nowrap;
    /* width: 40vw; */
    text-overflow: ellipsis;
	}
	.item-des {

    margin: 1.333333vw 0;
    font-size: 22rpx;
    color: #999;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;

    width: 42.666667vw;
	}

	.item-rate {
		margin: 1.333333vw 0;
    font-size: 22rpx;
    color: #999;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;

    width: 42.666667vw;
	}

	.item-lowest-price {
		color: #ff5339;
		font-size: 26rpx;
	}

	/* 物品添加图标 */
	.price-cart-add {
		width: 100%;
		position: relative;
	}
  .item-cart-add-icon {
    display: inline-block;
		width: 40rpx;
		height: 40rpx;
		position: absolute;
		right: 50rpx;
		bottom: 4rpx;
  }
/*  */
</style>
