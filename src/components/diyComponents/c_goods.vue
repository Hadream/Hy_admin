<template>
    <div class="goods-box" v-if="defaults.goodsList">
        <div class="wrapper">
            <draggable
                    class="dragArea list-group"
                    :list="defaults.goodsList.list"
                    group="peoples"
            >
                <div class="item" v-for="(goods,index) in defaults.goodsList.list" :key="index" v-if="defaults.goodsList.list.length">
                    <img :src="goods.image" alt="">
                    <span class="iconfont-diy icondel_1" @click.stop="bindDelete(index)"></span>
                </div>
                <div class="add-item item" @click="modals = true"><span class="iconfont-diy iconaddto"></span></div>
            </draggable>

        </div>

        <Modal v-model="modals" title="商品列表"  class="paymentFooter" scrollable width="900" @on-cancel="cancel" @on-ok="ok">
            <goods-list ref="goodslist"  @getProductId="getProductId" v-if="modals"></goods-list>
        </Modal>
    </div>
</template>

<script>
    import vuedraggable from 'vuedraggable'
    import goodsList from '@/components/goodsList'
    export default {
        name: 'c_goods',
        props: {
            name: {
                type: String
            },
            configData:{
                type:null
            }
        },
        components: {
            goodsList,
            draggable: vuedraggable
        },
        watch: {
            configData: {
                handler (nVal, oVal) {
                    this.defaults = nVal
                },
                immediate: true,
                deep: true
            }
        },
        data () {
            return {
                modals: false,
                goodsList: [],
                tempGoods: {},
                defaults:{}
            }
        },
        created () {
            this.defaults = this.configData
        },
        methods: {
            getProductId (data) {
                this.tempGoods = data
            },
            cancel () {
                console.log('cancel')
                this.tempGoods = {}
            },
            //对象数组去重；
            unique(arr) {
                const res = new Map();
                return arr.filter((arr) => !res.has(arr.id) && res.set(arr.id, 1))
            },
            ok () {
                let list = this.defaults.goodsList.list;
                list.push(this.tempGoods);
                let picList = this.unique(list);
                this.defaults.goodsList.list= picList;
                // this.defaults.goodsList.list.push(this.tempGoods);
            },
            bindDelete (index) {
                this.defaults.goodsList.list.splice(index, 1)
            }
        }
    }
</script>

<style scoped lang="stylus">
    .goods-box
        padding 16px 0
        margin-bottom 16px
        border-top 1px solid rgba(0,0,0,0.05)
        border-bottom 1px solid rgba(0,0,0,0.05)
        .wrapper,.list-group
            display flex
            flex-wrap wrap
        .add-item
            display flex
            align-items center
            justify-content center
            width 80px
            height 80px
            margin-bottom 10px
            background #F7F7F7
            .iconfont-diy
                font-size 20px
                color #D8D8D8
        .item
            position relative
            width 80px
            height 80px
            margin-bottom 20px
            margin-right 12px
            img
                width 100%
                height 100%
            .icondel_1
                position absolute
                right -10px
                top -16px
                color #999999
                font-size 28px
                cursor pointer
</style>

