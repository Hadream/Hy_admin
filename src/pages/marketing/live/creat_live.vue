<template>
    <div>
        <div class="i-layout-page-header">
            <router-link :to="{path:'/admin/marketing/live/live_goods'}"><Button icon="ios-arrow-back" size="small"  class="mr20">返回</Button></router-link>
            <span class="ivu-page-header-title">{{$route.meta.title}}</span>
        </div>
        <Card :bordered="false" dis-hover class="ivu-mt">
            <Form ref="formValidate" :model="formValidate" :label-width="labelWidth" :label-position="labelPosition" class="tabform" @submit.native.prevent>
                <Row :gutter="24" type="flex">
                    <Col span="24">
                        <FormItem label="直播间名称：">
                            <Input enter-button  placeholder="请输入直播间名称" element-id="name" v-model="formValidate.name" style="width: 60%;"/>
                        </FormItem>
                    </Col>
                    <Col span="24">
                        <div style="display: flex">
                            <FormItem label="背景图：">
                                <div @click="modalPicTap(0)" class="box">
                                    <img :src="formValidate.cover_img" alt="" v-if="formValidate.cover_img">
                                    <div class="upload-box" v-else><Icon type="ios-camera-outline" size="36" /></div>
                                </div>
                            </FormItem>
                            <span style="margin-left: 20px;color:#b0bac5;">尺寸：1080*1920px</span>
                        </div>

                    </Col>
                    <Col span="24">
                        <div style="display: flex">
                            <FormItem label="分享图：">
                                <div @click="modalPicTap(1)" class="box">
                                    <img :src="formValidate.share_img" alt="" v-if="formValidate.share_img">
                                    <div class="upload-box" v-else><Icon type="ios-camera-outline" size="36" /></div>
                                </div>
                            </FormItem>
                            <span style="margin-left: 20px;color:#b0bac5;">尺寸：800*640px</span>
                        </div>
                    </Col>
                    <Col span="24">
                        <FormItem label="主播昵称：">
                            <Input enter-button  placeholder="请输入主播昵称" element-id="anchor_name" v-model="formValidate.anchor_name" style="width: 60%;"/>
                        </FormItem>
                    </Col>
                    <Col span="24">
                        <FormItem label="主播微信号：">
                            <Input enter-button  placeholder="请输入主播微信号" element-id="anchor_wechat" v-model="formValidate.anchor_wechat" style="width: 60%;"/>
                        </FormItem>
                    </Col>
                    <Col span="24">
                        <FormItem label="联系电话：">
                            <Input enter-button  placeholder="请输入主播联系电话" element-id="phone" v-model="formValidate.phone" style="width: 40%;"/>
                        </FormItem>
                    </Col>
                    <Col span="24">
                        <FormItem label="直播时间：">
                            <DatePicker type="datetimerange" format="yyyy-MM-dd HH:mm" placeholder="请选择直播时间" style="width: 300px" :value="timeVal" @on-change="selectDate"></DatePicker>
                        </FormItem>
                    </Col>
                    <Col span="24">
                        <FormItem label="排序：">
                            <Input type="number" enter-button  placeholder="0" element-id="phone" v-model="formValidate.sort" style="width: 40%;"/>
                        </FormItem>
                    </Col>
                    <Col span="24">
                        <FormItem label="显示样式：">
                            <RadioGroup v-model="formValidate.screen_type">
                                <Radio :label="item.label" v-for="item in screen_type">
                                    <span>{{item.value}}</span>
                                </Radio>
                            </RadioGroup>
                        </FormItem>
                    </Col>
                    <Col span="24">
                        <FormItem label="直播间类型：">
                            <RadioGroup v-model="formValidate.type">
                                <Radio :label="item.label" v-for="item in type">
                                    <span>{{item.value}}</span>
                                </Radio>
                            </RadioGroup>
                        </FormItem>
                    </Col>
                    <Col span="24">
                        <FormItem label="直播间点赞：">
                            <RadioGroup v-model="formValidate.close_like">
                                <Radio :label="item.label" v-for="item in close_like">
                                    <span>{{item.value}}</span>
                                </Radio>
                            </RadioGroup>
                        </FormItem>
                    </Col>
                    <Col span="24">
                        <FormItem label="直播卖货：">
                            <RadioGroup v-model="formValidate.close_goods">
                                <Radio :label="item.label" v-for="item in close_goods">
                                    <span>{{item.value}}</span>
                                </Radio>
                            </RadioGroup>
                        </FormItem>
                    </Col>
                    <Col span="24">
                        <FormItem label="直播间评论：">
                            <RadioGroup v-model="formValidate.close_comment">
                                <Radio :label="item.label" v-for="item in close_comment">
                                    <span>{{item.value}}</span>
                                </Radio>
                            </RadioGroup>
                        </FormItem>
                    </Col>
                </Row>
                <Row :gutter="24" type="flex">
                    <Col v-bind="grid" span="24">
                        <Button type="primary" @click="handleSubmit('formItem')" style="width:19%;margin-left: 99px;">提交</Button>
                    </Col>
                </Row>
            </Form>
        </Card>
        <div>
            <Modal v-model="modalPic" width="60%" scrollable  footer-hide closable title='上传商品图' :mask-closable="false" :z-index="1">
                <uploadPictures :isChoice="isChoice" @getPic="getPic" :gridBtn="gridBtn" :gridPic="gridPic" v-if="modalPic"></uploadPictures>
            </Modal>
        </div>
    </div>
</template>

<script>
    import {mapState} from "vuex";
    import uploadPictures from '@/components/uploadPictures';
    import { liveAdd } from '@/api/live'
    export default {
        name: "creat_live",
        components: {
            uploadPictures
        },
        computed: {
            ...mapState('media', [
                'isMobile'
            ]),
            labelWidth () {
                return this.isMobile ? undefined : 100;
            },
            labelPosition () {
                return this.isMobile ? 'top' : 'right';
            }
        },
        data(){
            return {
                gridBtn: {
                    xl: 4,
                    lg: 8,
                    md: 8,
                    sm: 8,
                    xs: 8
                },
                gridPic: {
                    xl: 6,
                    lg: 8,
                    md: 12,
                    sm: 12,
                    xs: 12
                },
                grid: {
                    xl: 10,
                    lg: 16,
                    md: 18,
                    sm: 24,
                    xs: 24
                },
                formValidate:{
                    name:'',
                    anchor_name:'',
                    anchor_wechat:'',
                    phone:'',
                    screen_type:0,
                    close_like:1,
                    close_goods:1,
                    close_comment:1,
                    cover_img:'',
                    share_img:'',
                    sort:0,
                    type:0,
                    start_time:''
                },
                screen_type:[
                    {
                        value:'竖屏',
                        label:0
                    },
                    {
                        value:'横屏',
                        label:1
                    }
                ],
                type:[
                    // {
                    //     value:'推流',
                    //     label:1
                    // },
                    {
                        value:'手机直播',
                        label:0
                    }
                ],
                close_like:[
                    {
                        value:'开启',
                        label:1
                    },
                    {
                        value:'关闭',
                        label:0
                    }
                ],
                close_goods:[
                    {
                        value:'开启',
                        label:1
                    },
                    {
                        value:'关闭',
                        label:0
                    }
                ],
                close_comment:[
                    {
                        value:'开启',
                        label:1
                    },
                    {
                        value:'关闭',
                        label:0
                    }
                ],
                timeVal:'',
                modalPic: false,
                isChoice: '单选',
                activeIndex:0
            }
        },
        methods:{
            // 点击图文封面
            modalPicTap (type) {
                this.activeIndex = type
                this.modalPic = true;
            },
            // 选择日期
            selectDate(e){
                this.formValidate.start_time = e
            },
            // 获取图片信息
            getPic (pc) {
                this.$nextTick(() => {
                    if(this.activeIndex == 0){
                        this.formValidate.cover_img = pc.att_dir;
                    }else{
                        this.formValidate.share_img = pc.att_dir;
                    }
                    this.modalPic = false;
                })
            },
            // 保存
            handleSubmit(name){
                console.log('name',this.formValidate)
                liveAdd(this.formValidate).then(res=>{
                    console.log(res,'res')
                }).then(res=>{
                    this.$Message.success('添加成功');
                    setTimeout(() => {
                        this.$router.push({ path: '/admin/marketing/live/live_room' });
                    }, 500);
                }).catch(error=>{
                    this.$Message.error(error.msg)
                })
            }
        }
    }
</script>

<style scoped lang="stylus">
    .upload-box
        display flex
        align-items center
        justify-content center
        width 60px
        height 60px
        background #ccc
    .box
        width 60px
        height 60px
        margin-bottom 10px
        img
            width 100%
            height 100%
</style>
