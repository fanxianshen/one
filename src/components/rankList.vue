<template>
  <div class="rank">
    <div class="rank_list" v-show="showRank">
      <div class="rank_header">
        <P class="rank_header_title">&lt;榜單前50名均能享受儲值返點福利&gt;</P>
        <img
          class="rank_header_img"
          @click="showRank = !showRank"
          src="../assets/img/reward_header.png"
        >
      </div>
      <div id="rank"></div>
      <div class="list_content">
        <div class="list_hd">
          <span>排名</span>
          <span>頭像/暱稱</span>
          <span>儲值金幣</span>
        </div>
        <div class="list_bd">
          <div class="list_con">
            <!--S item -->
            <listItem
              v-for="(item, index) in list"
              :item="item"
              :itemNo="item.sort"
              :cls="getCls(index)"
              :key="`list_${index}`"
            />
            <!--E item -->
          </div>
          <loading :isCenter="true" v-show="isLoading"/>
          <div class="mod_empty" v-show="isEmpty">暫無數據</div>
          <div class="pagination" v-if="list.length>0">
            <div class="pagination_btn first" @click="handleChangePage('home')">首頁</div>
            <div class="pagination_btn prev" @click="handleChangePage('up')">上一頁</div>
            <div class="pagination_btn next" @click="handleChangePage('down')">下一頁</div>
          </div>
        </div>
        <a id="toTop" href="#rank"></a>
        <div class="rank_list_bg2"></div>
      </div>
    </div>
    <div class="rank_rule" v-show="!showRank">
      <div class="rule_header">
        <div class="rule_header_img" @click="showRank = !showRank">
          <img src="../assets/img/return_rank.png">
        </div>
        <div class="rule_rank">
          <div class="rule_num_content">
            <div>
              <p class="opc7">當前排名</p>
              <p class="rank_num">100</p>
            </div>
            <div>
              <p class="opc7">可領取返還金幣</p>
              <p class="rank_num">300</p>
            </div>
          </div>
        </div>
        <p class="rank_notice">榜單每小時刷新一次，您還沒有上榜，再接再厲哦！</p>
      </div>
      <div class="rule_text">
        <img src="../assets/img/rule_content.jpg">
      </div>
      <div class="rule_reward">
        <img src="../assets/img/rule_reward01.jpg">
        <img src="../assets/img/rule_reward02.jpg">
        <img src="../assets/img/rule_reward03.jpg">
      </div>
    </div>
  </div>
</template>

<script>
import listItem from "@/components/common/listItem";
import loading from "@/components/common/loading";
import axios from "axios";
import { setTimeout } from "timers";
export default {
  name: "RankList",
  components: {
    listItem,
    loading,
  },
  data() {
    return {
      showRank: true,
      list: [],
      isLoading: false,
      isEmpty: false,
      pager: {
        page: 1,
        pageSize: 20,
        total: 0,
      },
    };
  },
  created() {
    this.getRankData();
  },
  methods: {
    // 获取样式名
    getCls(index) {
      let itemNo = this.getItemNo(index);

      return itemNo < 4 ? `top${itemNo}` : "";
    },
    // 获取样式名
    getMyCls(index) {
      index = index + 1;
      let len = 4;

      return index < len ? `top${index}` : "";
    },
    // 序号
    getItemNo(num) {
      let { page, pageSize } = this.pager;

      return num + 1 + (page - 1) * pageSize;
    },
    // 分页切换
    handleChangePage(to) {
      if (to == "home") {
        this.pager.page = 1;
      } else if (to == "up") {
        if (this.pager.page == 1) {
          return;
        }
        this.pager.page = this.pager.page - 1;
      } else if (to == "down") {
        if (
          this.pager.page >= 5 ||
          this.pager.page == Math.ceil(this.pager.total / this.pager.pageSize)
        ) {
          return;
        } else {
          this.pager.page++;
        }
      }
      this.list = [];
      document.getElementById("toTop").click();
      this.getRankData();
    },
    getRankData() {
      this.isLoading = true;
      this.$api.rank.list(this.pager).then((res) => {
        // 执行某些操作
        if (this.pager.page == 1 && res.data.length == 0) {
          this.isEmpty = true;
          return;
        }
        setTimeout(() => {
          this.isLoading = false;
          this.list = res.data.list;
        }, 1000);
      });
    },
  },
};
</script>

<style scoped lang="scss">
.rank {
  .rank_list {
    .rank_header {
      background: url(../assets/img/reward_bg.jpg) no-repeat;
      background-size: 100% 100%;
      height: 260px;
      width: 100%;
      padding-top: 20px;
      text-align: center;
      .rank_header_title {
        color: #8aaeff;
        font-size: 28px;
      }
      .rank_header_btn {
        color: #ff93f1;
        font-size: 28px;
        display: inline-block;
      }
      .rank_header_img {
        width: 702px;
        margin: 50px auto 0;
      }
    }
    #rank {
      opacity: 0;
      height: 0;
    }
    .list_content {
      width: 100%;
      box-sizing: border-box;
      position: relative;
      padding: 50px 65px 45px;
      background: url(../assets/img/reward_content_bg01.jpg) no-repeat;
      background-size: 100% 1218px;
      width: 100%;
      height: 2698px;
      .rank_list_bg2 {
        background: url(../assets/img/reward_content_bg02.jpg) no-repeat;
        background-size: 100% 1480px;
        width: 100%;
        height: 1480px;
        position: absolute;
        bottom: 0;
        left: 0;
        z-index: -1;
      }
      .list_hd {
        display: flex;
        align-items: center;
        height: 52px;
        background: rgba(125, 159, 255, 1);
        border-radius: 26px;
        color: #000;
        span {
          display: flex;
          align-items: center;
          font-size: 30px;
          height: 100%;
          box-sizing: border-box;
          flex: 0 0 auto;
          &:nth-child(1) {
            padding-left: 30px;
            min-width: 74px;
          }
          &:nth-child(2) {
            padding-left: 100px;
            min-width: 260px;
          }
          &:nth-child(3) {
            flex: 1 1 auto;
            padding-right: 30px;
            justify-content: flex-end;
          }
        }
      }
      .list_bd {
        .my_info {
          border-top: 1px solid #e6e6e6;
          border-bottom: 1px solid #e6e6e6;
        }
        .mod_empty {
          text-align: center;
          height: 426px;
          box-sizing: border-box;
          background: url(../assets/img/empty_mizhua.png) 50% 0 no-repeat;
          background-size: auto 426px;
          padding-top: 330px;
          color: #ffffff;
          font-size: 30px;
        }
      }
      // 分页
      .pagination {
        margin-top: 28px;
        display: flex;
        justify-content: space-around;
        height: 4vw;
        padding: 4vw 0;
        font-size: 4vw;
        .pagination_btn {
          width: 126px;
          height: 50px;
          line-height: 50px;
          text-align: center;
          padding: 0;
          color: #222222;
          font-weight: 600;
          border-radius: 10px;
          font-size: 26px;
          cursor: pointer;
          background-image: url(../assets/img/page_bg.png);
          background-size: contain;
          background-repeat: no-repeat;
          &:nth-child(2) {
            margin: 0 28px;
          }
        }
      }
    }
  }

  .rank_rule {
    .rule_header {
      background: url(../assets/img/rule_bg.jpg) no-repeat;
      background-size: 100% 100%;
      height: 433px;
      width: 100%;
      .rule_header_img {
        width: 702px;
        margin: 0 auto;
        img {
          width: 100%;
        }
      }
      .rule_rank {
        background: url(../assets/img/rule_rank.png) no-repeat;
        background-size: 100% 100%;
        height: 203px;
        width: 704px;
        margin: 0 auto;
        .rule_num_content {
          display: flex;
          text-align: center;
          padding-top: 50px;
          color: #ffffff;
          font-size: 28px;
          & > div {
            flex: 1;
          }
          .rank_num {
            font-size: 40px;
            font-weight: 700;
            margin-top: 20px;
          }
          .opc7 {
            opacity: 0.78;
          }
        }
      }
      .rank_notice {
        margin-top: 15px;
        font-size: 24px;
        color: #81a5f6;
        text-align: center;
      }
    }

    .rule_text {
      font-size: 0;
      line-height: 0;
      img {
        height: 941px;
        width: 100%;
      }
    }
    .rule_reward {
      font-size: 0;
      line-height: 0;
      img {
        width: 100%;
      }
    }
  }
}
</style>
