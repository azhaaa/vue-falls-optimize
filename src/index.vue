<template>
  <div class="falls" :style="{ width: width ? width + 'px' : '100%' }">
    <div class="falls_items" v-for="(item, index) in final_photoList" :key="index">
      <div class="item_h" ref="item_h">
        <div v-for="(o, z) in item.img_list" :key="z" @click="toFather(o)">
          <div class="item" :style="itemWidth" v-if="o.isShow">
            <img :src="o.imgUrl" alt="加载错误" draggable="false" />
            <img v-if="o.type==2" src="./images/bf.png" style="width: 20px;position: absolute;top: 0.4rem;right: 0.5rem;" alt="">
            <div class="item-body">
              <div class="item-desc">{{o.title}}</div>
              <div class="item-footer">
                <img v-if="o.portrait != null" class="avatar" :src="o.portrait" alt />
                <img
                  class="avatar"
                  src="./images/roundlogo.png"
                  alt
                  v-else
                />
                <div class="name">{{o.user_name}}</div>
                <div class="like" @click.stop="toFatherLove(o)">
                  <img src="./images/like.png" v-if="o.give_state == 1" class="likeindeximg" />
                  <img src="./images/nolike.png" v-else-if="o.give_state == 0" class="likeindeximg" />
                  <div class="like-total">{{o.give}}</div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: 'fallsOptimize',
  props: {
    // 图片列表
    photoList: {
      type: Array,
      default: () => {
        return []
      },
    },
    // 分成几列
    row: {
      type: Number,
      default: 2,
    },
    width: {
      type: String,
    },
  },
  data() {
    return {
      row1: 1,
      final_photoList: [],
      itemWidth:"width: 100%;",
    }
  },
  watch: {
    // row
    row(val) {
      if (typeof val === 'number') {
        this.final_photoList = []
        this.pre_loade(this.photoList)
      }
    },
    photoList(n, o) {
      if (JSON.stringify(n) === JSON.stringify(o)) return false
      this.final_photoList = []
      this.pre_loade(n)
    },
  },
  methods: {
    //这个方法很吊
    veryd(len){
      if (len < this.row) {
        let ww = (100/this.row);
        this.itemWidth = "width: "+ww+"%;"
      }else{
        this.itemWidth = "width: 100%;"
      }
      
    },
    // 图片的预加载  由于图片加载需要时间，在获取最低高度的时候，加载时长不好控制，导致高度获取不准
    pre_loade(photoList) {
      if (!this.row || this.photoList.length === 0) return false
      let that = this
      // 加载成功的图片数量
      let suc_count = 0
      // 加载失败的图片数量
      let los_count = 0
      // 图片总数量
      let len = photoList.length
      // 当加载成功的数量加上失败的数量等于图片总数量的时候表示全部加载完毕，再执行取高度等操作
      if (len) {
        photoList.forEach((item) => {
          console.log((item.video+"?vframe/jpg/offset/1"))
          let imgObj = new Image() //创建图片对象
          let url = item.type==1 ? item.image[0] : item.video+"?vframe/jpg/offset/1"
          imgObj.src = url
          imgObj.addEventListener('load', function () {
            item.isShow = true
            item.imgUrl = url;
            suc_count++
            if (suc_count + los_count === len) {
              that.$nextTick(() => {
                that.init_img()
                that.add_img()
              })
            }
          })
          imgObj.addEventListener('error', function () {
            los_count++
            item.isShow = false
            item.imgUrl = null;
            if (suc_count + los_count === len) {
              that.$nextTick(() => {
                that.init_img()
                that.add_img()
              })
            }
          })
        })
      }
    },
    // 点击图片的回调
    toFather(item) {
      delete item.isShow
      delete item.imgUrl
      this.$emit('onClick', item)
    },
    // 点击爱心的回调
    toFatherLove(item) {
      delete item.isShow
      delete item.imgUrl
      this.$emit('clickLove', item)
    },
    // 初始化图片 在created中进行 取photoList中的前row项
    init_img() {
      let _ = this
      let row1 = _.row
      // 如果图片张数小于需要分的行数 取图片的总张数
      if (_.photoList.length < row1) {
        // 子组件不能直接修改父组件传递过来的值
        row1 = _.photoList.length
        this.veryd(_.photoList.length)
      }
      // if (_.photoList.length <= 2) {
      //   row1 = 1
      // }
      this.row1 = row1

      // 定义存储图片的数据结构
      for (var i = 0; i < row1; i++) {
        let temp_item = this.back_item(_.photoList[i])
        _.final_photoList.push(temp_item)
      }
    },
    //定义存储图片的数据结构
    back_item(obj) {
      if (JSON.stringify(obj) === '{}') return false
      let item = {
        img_list: [obj],
      }
      return item
    },
    // 获取盒子的高度 返回最小高度的index
    get_height() {
      let hs = []
      this.$refs.item_h.forEach((i) => {
        hs.push(i.offsetHeight)
      })
      // hs = [5, 3, 1]
      // 获取最小值
      let min = Math.min(...hs)
      let min_index = hs.findIndex((i) => {
        return i === min
      })
      return min_index
    },
    // 获取最短的列 插入图片
    add_img() {
      let temp_arr = this.photoList.slice(this.row1)
      if (!temp_arr.length) return false
      temp_arr.forEach((item) => {
        // 转成异步
        setTimeout(() => {
          let index = this.get_height()
          this.final_photoList[index].img_list.push(item)
        })
      })
    },
  },
  created() {
    this.pre_loade(this.photoList)
  },

  mounted() {},
}
</script>

<style scoped>
.falls {
  width: 100%;
  display: flex;
  flex-wrap: nowrap;
  box-sizing: border-box;
  /* padding: 8px 4px; */
}
.falls_items {
  flex: 1;
}
.item {
  width: 100%;
  padding: 0px 4px;
  padding-top: 0px;
  padding-bottom: 0px;
  box-sizing: border-box;
  margin: 4px 0px 8px 0px;
  position: relative;
}
img {
  display: block;
  width: 100%;
}
.likeindeximg {
  width: 1.2rem;
  margin-right: 0.2rem;
}
.item-body {
  background: #fff;
  padding: 12px;
}
.item-desc {
  font-size: 15px;
  color: #333333;
  line-height: 15px;
  font-weight: bold;
}
.item-footer {
  margin-top: 22px;
  position: relative;
  display: flex;
  align-items: center;
}
.avatar {
  width: 30px;
  height: 30px;
  border-radius: 50%;
  background-repeat: no-repeat;
  background-size: contain;
}
.name {
  max-width: 83px;
  margin-left: 0.5rem;
  font-size: 0.6rem;
  letter-spacing: 0;
  color: #999999;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}
.like {
  position: absolute;
  right: 0;
  display: flex;
  align-items: center;
}
.like-total {
  font-size: 12px;
  color: #999999;
}
</style>
