<template>
  <view class="container">
    <image class="page-bg" :src="bgUrl" mode="widthFix"/>
    <div style="height: 230px"></div>
    <div class="content-wrap">
      <nut-tabs v-model="tabIndex" title-scroll="true" title-gutter="5" class="panel" >
        <nut-tabpane v-for="item in categoryList" :title="item.categoryName">
          <div style="white-space: nowrap;overflow-y: scroll;margin: 0 5px">
            <image v-for="img in item.list" :src="img.url" mode="widthFix" class="img-item"/>
          </div>
        </nut-tabpane>
      </nut-tabs>
    </div>
  </view>
</template>

<script>
import './index.sass'
import { reactive, toRefs} from 'vue';
export default {
  name: 'Index',
  components: {
    
  },
  setup(){
    function tabSwitch(item, index) {
      console.log(item, index);
    }
    const state = reactive({
      bgUrl:require('../assets/img/index_bg.jpg'),
      categoryList:[
        {
          id:1,
          categoryName:"虎年最火",
          list:[
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            }
          ]
        },
        {
          id:2,
          categoryName:"金钱豹",
          list:[
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            }
          ]
        },
        {
          id:3,
          categoryName:"呆萌虎",
          list:[
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            }
          ]
        },
        {
          id:4,
          categoryName:"简约可爱",
          list:[
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            }
          ]
        },
        {
          id:5,
          categoryName:"2022",
          list:[
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            }
          ]
        },
        {
          id:6,
          categoryName:"国庆",
          list:[
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            }
          ]
        },
        {
          id:7,
          categoryName:"中秋",
          list:[
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            },
            {
              url:"https://niu.kaixin520.top/2022/01/1642609168-a7bb57ada697646.png"
            }
          ]
        }
      ],
      tabIndex:0
    });

    const handleClick = (type, msg, cover = false) => {
      state.show = true;
      state.msg2 = msg;
      state.type = type;
      state.cover = cover;
    };

    return {
      tabSwitch,
      ...toRefs(state),
      handleClick,
    }
  }
}
</script>

<style lang="scss">
.index {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
}
</style>
