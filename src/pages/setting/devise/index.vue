<template>
    <div>
    <div class="i-layout-page-header">
        <div class="i-layout-page-header">
            <router-link :to="{path:'/admin/setting/pages/devise'}"><Button icon="ios-arrow-back" size="small"  class="mr20">返回</Button></router-link>
            <span class="ivu-page-header-title mr20">页面设计</span>
        </div>
    </div>
    <Card :bordered="false" dis-hover class="ivu-mt">
    <div class="flex-wrapper">
        <!-- :src="iframeUrl" -->
        <iframe class="iframe-box" :src="iframeUrl" frameborder="0" ref="iframe"></iframe>
        <div>
            <div class="content">
                <rightConfig :name="configName" :pageId="pageId"></rightConfig>
            </div>
        </div>
    </div>
    </Card>
    </div>
</template>

<script>
    import { diyGetInfo, storeStatus } from  '@/api/diy'
    import { mapMutations } from 'vuex'
    import rightConfig from '@/components/rightConfig/index'
    export default {
        name: "index",
        components:{
            rightConfig
        },
        data(){
            return {
                configName:'',
                iframeUrl:'',
                setConfig:'',
                updataConfig:'',
                pageId:0
            }
        },
        created() {
            let that = this;
            let pageId = this.$route.query.id;
            let names = this.$route.query.name;
            this.setConfig = names +'/setConfig';
            this.updataConfig = names +'/updataConfig';
            this.pageId = parseInt(pageId);
            this.iframeUrl = `${location.origin}?type=iframe`;
            storeStatus().then(res=>{
                let status = parseInt(res.data.store_status);
                diyGetInfo(parseInt(pageId)).then(datas=>{
                    let data = datas.data.info.value;
                    //periphery = 1是不显示周边；
                    if(data.z_tabBar && !this.$store.state[names].periphery){
                        if(status){
                            if(data.z_tabBar.tabBarList.list.length === 4){
                                data.z_tabBar.tabBarList.list.splice(2,0,{
                                    name:'周边',
                                    selectedIconPath: 'http://v4.crmeb.net/uploads/attach/2020/09/20200902/e86de57090c0bbb9783e0ada6e1aa8f4.png',
                                    iconPath: 'http://v4.crmeb.net/uploads/attach/2020/09/20200902/35fb14033d5cb7d03f34f46450c18d99.png',
                                    pagePath: '/pages/storeList/index'
                                })
                            }
                            // this.upData(data);
                        }else {
                            if(data.z_tabBar.tabBarList.list.length === 5){
                                data.z_tabBar.tabBarList.list.splice(2,1)
                            }
                        }
                    }
                    this.upData(data);
                })
            })
        },
        mounted() {
            //监听子页面给当前页面传值
            window.addEventListener("message", this.handleMessage,false)
        },
        methods:{
            //接收iframe值
            handleMessage (event) {
                if(event.data.name){
                    this.configName = event.data.name
                    this.add(event.data.name)
                }
            },
            add (data) {
                this.$store.commit(this.setConfig, data);
            },
            upData (data) {
                this.$store.commit(this.updataConfig, data);
            }
            // ...mapMutations({
            //     add: 'diy/setConfig',
            //     upData:'diy/updataConfig'
            // })
        }
    }
</script>

<style scoped lang="stylus">
    .flex-wrapper
        display flex
    .iframe-box
        width: 375px;
        height: 700px;
        /*border: 1px solid #ddd;*/
        border-radius: 4px;
        box-shadow : 0 0 7px #cccccc;
    .right-box
        width 400px
        margin-left 50px
        border:1px solid #ddd;
        border-radius 4px
        .title-bar
            width 100%
            height 38px
            line-height 38px
            padding-left 24px
            color #333
            border-radius 4px
            border-bottom 1px solid #eee

</style>
