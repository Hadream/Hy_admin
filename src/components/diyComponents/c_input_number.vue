<template>
    <div class="numbox" v-if="datas[name]">
        <div class="c_row-item">
            <Col class="label" span="4">
                <span>数量</span>
            </Col>
            <Col span="19" class="slider-box">
                <Input v-model="datas[name].val" type="number" placeholder="请输入数量" style="text-align: right;"/>
            </Col>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'c_input_number',
        props: {
            name: {
                type: String
            },
            configData:{
                type:null
            }
        },
        data () {
            return {
                defaults: {},
                sliderWidth: 0,
                datas: {}
            }
        },
        mounted () {
            this.$nextTick(() => {
                this.datas = this.configData
            })
        },
        watch: {
             configData: {
                handler (nVal, oVal) {
                    this.datas = nVal
                },
                immediate: true,
                deep: true
            }
        }
    }
</script>

<style scoped lang="stylus">
    .numbox
        display flex
        align-items center
        margin 20px 0 10px 0
        span
            width 80px
            color #999
    /*font-size 12px*/
    .c_row-item
        width 100%
</style>
