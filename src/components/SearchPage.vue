<template>
  <div>
      <el-header class="container hc_header">
        <el-row :gutter="20" style="z-index: 10;">
          <div class="hc_logo">
            <img src="static/images/logo/HeroC.png" style="width: 100%"/>
          </div>
          <el-col :span="10" style="margin-top: 10px">
            <el-input id="searchInput" v-model="wd" @keyup.enter.native="submitWd">
              <el-button slot="append"  @click="submitWd">搜一下</el-button>
            </el-input>
          </el-col>
          <el-col :span="8" :offset="3" class="hc_hotwd">
            <span style="color: black">热门搜索：</span>
            <span v-for="(hwd,index) in hotwd" :key="index">
              <a class="hot" href="#" :id="index" @click="submitHotWd(index)">{{hwd}}</a> &nbsp;&nbsp;
            </span>
          </el-col>
        </el-row>
      </el-header>

    <el-container style="padding-top: 60px;">
      <el-main>
        <div style="height: 220px" v-if="info.length==0?true:false"></div>

        <el-row v-for="(result,index) in info" :key="index">
        <el-col :span="22" :offset="2" style="padding-left: 30px; margin-bottom: 10px">
          <h3 class="t">
            <a target="_blank" style="float: left" :href="result.href" v-html="result.title"></a>
          </h3>
          <div style="width: 100%; padding-top: 5px; clear: both;">
            <div style="width: 600px; float: left">
              <div style="font-size: 13px; text-align: left">
                {{result.content}}
              </div>
              <div class="hc_contentfooter">
                <span style="color: #666666; text-decoration: underline">HeroC获取结果</span>
              </div>
            </div>
          </div>
        </el-col>
      </el-row>

      </el-main>
    </el-container>

    <div class="block hc_page" v-if="showPage">
      <el-pagination
        background
        :page-size="10"
        :page-count="pageCount"
        :current-page.sync="currentPage"
        layout="prev, pager, next"
        @current-change="submitPage">
      </el-pagination>
    </div>

    <div class="hc_footer">
      <span>本搜索引擎仅供学习使用，不得非法使用 :免责声明</span><br/>
      <span>HeroC (herocheung@foxmail.com) :开发者</span>
    </div>

    <div v-loading="loading" :style="loadingBox" v-if="loading==true?true:false"></div>
    </div>

</template>

<script>
    export default {
      name: "SearchPage",
      mounted() {
        /*if(this.flag){
          document.getElementById("searchInput").focus()
        }*/
      },
      data(){
          return{
            hotwd:["Java","SSM","CSDN","Redis","ElasticSearch","Linux"],
            wd:"",
            getWd:"",
            pageCount: 100,
            currentPage: 0,
            showPage: false,
            info:[],
            loading:false,
            loadingBox:{
              position: "fixed",
              top: "65px",
              width:"100%",
              height:"",
            }
            // flag:true // 判定是否是初次加载
          }
      },
      created(){
        this.getHeight()
      },
      methods:{
        getHeight(){
          this.loadingBox.height=window.innerHeight-65+'px';
        },
        submitWd(){
          this.goTop();
          if(this.wd==""){
            this.info=[];
            this.getWd = "";
            this.showPage = false;
          }else {
            this.getWd = this.wd;
            this.loading = true;
            this.currentPage = 0;
            this.axios({
              url: '/api/search/'+this.wd,
              //url: '/hc/search/'+this.wd,
              method: "get",
              /* data: JSON.stringify({
                 userName: param.userid,
                 password: param.pwd
               }),*/
              headers: {
                'Content-Type': 'application/json; charset=utf-8'
              }
              // responseType: 'text'
            }).then((response)=>{
              if(response.data.status == "200"){
                if((response.data.count)%10!=0){
                  this.pageCount = Math.floor(response.data.count/10)+1;
                  this.showPage = true;
                }else {
                  this.pageCount = (response.data.count/10);
                  this.showPage = true;
                }
                this.info = JSON.parse(response.data.info);
                this.loading = false;
              }else if(response.data.status == "404"){
                this.info = [];
                this.showPage = false;
                this.loading = false;
              }
            })
          }
        },
        submitPage(val){
          if(this.wd==""){
            if(this.getWd!=""){
              this.wd = this.getWd;
              this.submitRealForPage(val);
            }else {
              this.info = [];
            }
          }else {
            this.submitRealForPage(val);
          }
        },
        submitRealForPage(val){
          this.loading = true;
          this.axios({
            url: '/api/search/'+this.wd+"/"+val,
            //url: '/hc/search/'+this.wd+"/"+val,
            method: "get",
            headers: {
              'Content-Type': 'application/json; charset=utf-8'
            }
          }).then((response)=>{
            if(response.data.status == "200"){
              this.info = JSON.parse(response.data.info);
              this.loading = false;
            }else if(response.data.status == "404"){
              this.info = [];
              this.showPage = false;
              this.loading = false;
            }
            this.goTop();
          })
        },
        submitHotWd(index){
          this.wd = document.getElementById(index).innerText;
          this.submitWd();
        },
        goTop() {         // 回到顶部
          document.body.scrollTop = document.documentElement.scrollTop = 0;
        },
      }
    }
</script>

<style>
  .container {
    width: 100%;
    height: 100%;
    position: fixed;
    z-index: 1;
  }
  .el-input__inner{
    border-bottom:2px #CCCCCC solid;
    border-top:2px #CCCCCC solid;
    border-left:2px #CCCCCC solid;
    border-right: none;
    border-bottom-left-radius: 10px;
    border-top-left-radius: 10px;
  }
  .el-input__inner:focus{
    border-bottom:2px #4e6ef2 solid;
    border-top:2px #4e6ef2 solid;
    border-left:2px #4e6ef2 solid;
  }
  .el-input-group__append{
    border: none!important;
    background-color: white;
  }
  .el-button{
    color: white!important;
    border-left:none!important;
    border-bottom-left-radius: 0px;
    border-top-left-radius: 0px;
    border-top-right-radius: 10px;
    border-bottom-right-radius: 10px;
    border:1px #4e6ef2 solid!important;
    background-color: #4e6ef2!important;
  }
  .el-button:hover{
    background-color: #4662d9!important;
  }

  .t{
    font-weight: 400;
    font-size: medium;
    margin-bottom: 1px;
    line-height: 1.54;
  }

  .hot{
    color: #666666;
  }
  .hot:hover{
    color: blue;
  }
  em{
    color: brown;
  }
  .hc_header{
    box-shadow: 0 2px 4px rgba(0, 0, 0, .12);
    height: 65px;
    background-color: white;
  }
  .hc_logo{
    width: 100px;
    float: left;
    margin-top: -7px;
    padding-left: 10px;
    padding-right: 25px;
  }
  .hc_hotwd{
    text-align: right;
    padding-top: 35px;
    font-size: 13px;
  }
  .hc_contentfooter{
    font-size: 13px;
    text-align: left;
    margin-top: 10px;
  }
  .hc_page{
    float:left;
    margin-bottom: 40px;
    margin-top: 10px;
    margin-left: 140px;
  }
  .hc_footer{
    width: 100%;
    background: rgba(0,0,0,0.1);
    height: 40px;
    clear: both;
    color: #666;
    padding-top: 10px;
    font-size: 13px;
  }
</style>
