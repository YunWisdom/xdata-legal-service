<template>
  <div id="reward-home" style="background-color:#f0f0f0;width:100%;height:auto;">
      <div style="background-color:#f0f0f0;width:100%;height:auto;">
      <a-row :gutter="24">

        <keep-alive>
          <a-col :xl="24" :lg="24" :md="24" :sm="24" :xs="24" style="position:relative;">

            <div style="position:absolute;left:0px width:80px;" >
              <van-sidebar v-model="activeTabKey">
                <van-sidebar-item style="display:block;" title="审批" :to="`/legal/message`" />
                <van-sidebar-item style="display:none;" title="云文档" :to="`/legal/netdisk`" />
                <van-sidebar-item style="display:none;" title="联系人" :to="`/legal/contact`" />
                <van-sidebar-item style="display:block;" title="工作台" :to="`/legal/workspace`" />
                <van-sidebar-item style="display:none;" title="收藏" :to="`/legal/collect`" />
                <van-sidebar-item style="display:none;" title="设置" :to="`/legal/setup`" />
              </van-sidebar>
            </div>

            <div style="position:absolute; left:80px; width:900px;" >
              <template v-for="(pane,index) in paneflows"  >
                <a-card  :key="pane.id"  :title="pane.title"  class="pane-flow-card" >
                  <template v-for="item in pane.taskflows"  >
                    <a-card-grid :key="item.href" style="width:25%;textAlign:'center'">
                      <a-card-meta>
                        <div slot="title" class="card-title pane-flow-card-meta" @click="item.click" >
                          <div class="pane-flow-card-meta-icon">
                            <a-avatar size="large"  :src="item.avatar" />
                          </div>
                          <div class="pane-flow-card-meta-title" >
                            <a class="pane-flow-card-meta-tname" >{{ item.name }}</a>
                            <div class="pane-flow-card-meta-description" > {{ item.description }} </div>
                          </div>
                        </div>
                      </a-card-meta>
                    </a-card-grid>
                  </template>
                </a-card>
                <a-card v-if="index <= 1" :key="pane.id + pane.title" :title="' '" class="pane-flow-card-middle" >
                </a-card>
              </template>
            </div>

            <div style="position:absolute; left:1000px; width: 300px;">
                <a-card
                  title="便捷导航"
                  style="margin-bottom: 24px"
                  :bordered="false"
                  :body-style="{padding: 0}"
                >
                  <div class="item-group">
                    <div class="pane-right-item-group" >
                      <template v-for=" item in quicktags">
                        <a-tag class="pane-right-item-group-tag" :key="item.name" :color="item.color" @click="item.click" >{{item.name}}</a-tag>
                      </template>
                    </div>
                  </div>
                </a-card>
                <a-card :loading="loading" title="奖罚流程" :bordered="false" style="margin-top:20px;">
                  <a-tag
                    color="blue"
                    @click="handleWriteBlog()"
                    style="margin-bottom:10px;position:absolute;top:18px;right:20px;display:none;"
                  ></a-tag>
                  <div class="members">
                    <a-row>
                      <a-col :span="12" v-for="item in wflows" :key="item.href">
                        <a @click="item.click">
                          <a-avatar class="pane-right-avatar" size="small" :src="item.avatar" />
                          <span class="member">{{ item.name }}</span>
                        </a>
                      </a-col>
                    </a-row>
                  </div>
                </a-card>
            </div>

          </a-col>
        </keep-alive>

      </a-row>
    </div>
  </div>
</template>
<script>

import * as task from '@/request/task';
import * as query from '@/request/query';
import * as constant from '@/request/constant';
import * as workflow from '@/request/workflow';
import * as manageAPI from '@/request/manage';
import * as wflowprocess from '@/request/wflow.process';
import * as workconfig from '@/request/workconfig';

export default {
  mixins: [window.mixin],
  data() {
    const { $router } = this;
    return {
      pageName: "案件管理",
      momentNewMsg: true,
      activeTabKey: 3,
      iswechat:'',
      paneflows: workconfig.reward($router),
      wflows: workconfig.getRewardWflow($router),
      quicktags: workconfig.getRewardQuickTag($router),
      userinfo:null,
    };
  },
  activated() {
    this.queryInfo();
  },
  mounted() {
    this.queryInfo();
  },
  methods: {
    // 查询初始化信息
    async queryInfo() {
      try {
        this.iswechat = Betools.tools.isWechat(); //查询当前是否微信端
        this.userinfo = await this.weworkLogin(); //查询当前登录用户
      } catch (error) {
        console.log(error);
      }
    },
    // 企业微信登录处理函数
    async weworkLogin(){
      try {
        return await query.queryWeworkUser();
      } catch (error) {
        console.log(error);
      }
    },
  },
};
</script>
<style scoped >
    @import "../../assets/css/reward.home.css";
</style>
