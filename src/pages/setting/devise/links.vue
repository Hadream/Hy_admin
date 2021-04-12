<template>
    <div>
        <div class="i-layout-page-header">
            <PageHeader class="product_tabs" :title="$route.meta.title" hidden-breadcrumb></PageHeader>
        </div>
        <Card :bordered="false" dis-hover class="ivu-mt">
            <div class="list-box">
                <div class="link-item" v-for="(item,index) in list" :key="index">
                    <div class="title">{{item.name}}</div>
                    <div class="txt">
                        <span>地址：</span>{{item.url}}
                    </div>
                    <div class="txt" v-if="item.parameter">
                        <p><span>参数：</span></p>
                        <p v-html="item.parameter"></p>
                    </div>
                    <div class="tips">例如：{{item.example}}
                        <!--<Button size="small" style="margin-left: 10px" v-clipboard:copy="item.example"-->
                                <!--v-clipboard:success="onCopy"-->
                                <!--v-clipboard:error="onError">复制</Button>-->
                        <span class="copy copy-data" :data-clipboard-text="item.example">复制</span
                        >
                    </div>
                </div>
            </div>
        </Card>
    </div>
</template>

<script>
    import ClipboardJS from "clipboard";
    import { getUrl } from '@/api/diy'
    export default {
        name: 'links',
        data () {
            return {
                list: [
                    {
                        name: '商城首页',
                        url: '/pages/users/order_list/index',
                        parameter: [
                            {

                            }
                        ],
                        example: '/pages/activity/bargain/index'

                    },
                    {
                        name: '商城首页',
                        url: '/pages/users/order_list/index',
                        parameter: [
                            {

                            }
                        ],
                        example: '/pages/activity/bargain/index'

                    }
                ]
            }
        },
        created () {
            getUrl().then(res => {
                this.list = res.data.url
            })
        },
        mounted: function() {
            this.$nextTick(function() {
                let copybtn = document.getElementsByClassName("copy-data");
                const clipboard = new ClipboardJS(copybtn);
                clipboard.on("success", () => {
                    this.$Message.success('复制成功');
                });
            });
        },
        methods: {
            // onCopy () {
            //     this.$Message.success('复制成功');
            // },
            // onError () {
            //     this.$Message.error('复制成功');
            // }
        }
    }
</script>

<style scoped lang="stylus">
.list-box
    .link-item
        padding 10px 0
        border-bottom 1px solid #f5f5f5
        .title
            font-size: 14px;
            color: #2d8cf0;
        .txt
            margin 5px 0
            font-size 12px
            span
                color #333
            p
                display inline-block
                color #19be6b
                margin-right 10px
                span
                    color #333
                &.red
                    color #f00
        .tips
            font-size 12px
            color #999
            .copy
                padding 3px 5px
                border 1px solid #cccccc
                border-radius 5px
                color #333
                cursor pointer
                margin-left 5px
                &:hover
                     border-color #2d8cf0
                     color #2d8cf0

</style>
