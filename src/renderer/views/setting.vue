<template>
    <div v-if="setting" class="setting view">
        <div class="leftMenu">
            <div @click="menuIndex = index" :class="['item',index == menuIndex?'selected':'']"
                v-for="(item,index) in menuItems">
                {{item}}
            </div>
            <div class="other"></div>
        </div>
        <div v-show="menuIndex == 0" class="content">
            <div class="formItem">
                <div>自动保存文章的时间间隔：</div>
                <div>
                    <input @change="autoSaveIntervalSeconds" v-model="setting.autosave_interval" type="number" />
                </div>
                <div>
                    秒
                </div>
            </div>
            <div class="formItem">
                <div>文章内图片长超过</div>
                <div>
                    <input @change="compressHeight" v-model="setting.img_h" type="number" />
                </div>
                <div>
                    px，且宽超过
                </div>
                <div>
                    <input @change="compressWidth" v-model="setting.img_w" type="number" />
                </div>
                <div>
                    px，将启用图片等比例压缩（任一值设置为-1将禁用图片压缩）
                </div>
            </div>
            <div class="formItem">
                默认编辑器：
                <div @click="setting.editor_type='html'" class="rdBtn">
                    <i :class="['iconfont',setting.editor_type=='html'?'icon-xuanzhong':'icon-weixuanzhong']"></i>
                    HTML
                </div>
                <div @click="setting.editor_type='markdown'" class="rdBtn">
                    <i :class="['iconfont',setting.editor_type=='markdown'?'icon-xuanzhong':'icon-weixuanzhong']"></i>
                    MarkDown
                </div>
            </div>
            <div class="formItem">
                <div @click="save" class="btn" style="margin-left: 0px;">保存系统设置</div>
            </div>
        </div>
        <div v-show="menuIndex == 1" class="content">
            此处内容尚未开发
        </div>
        <div v-show="menuIndex == 2" class="content">
            您通过“想学吗”编辑的知识，以及知识内部的图片、个人设置等数据均保存在本地；
            <br /> 您的知识，可以自由的发布到“微信公众号”、“简书”、“博客园”、“开源中国”等知名网站（需拥有相应网站的账号）；
            <br /> 发布知识时，知识内部的图片也会上传到对应的网站上；
            <br /> 修改知识后，再次发布该知识，不会导致图片重复上传；
            <br /> 在文章中删除图片，本地目录中的图片也会被删除，不会留有垃圾数据；
            <br /> 到目前为止“想学吗”不会保存您的任何账号数据；
            <br /> 文章发布到目标平台不夹带任何“尾巴”
        </div>
        <div v-show="menuIndex == 3" class="content">
            此处内容尚未开发
        </div>
    </div>
</template>
<script>
    import swal from 'sweetalert';
    export default {
        data() {
            return {
                setting: null,
                menuIndex: 0,
                menuItems: ['系统设置', '用户信息', '系统说明', '更新说明']
            }
        },
        mounted() {
            this.db("settings").select("*").then(rows => {
                this.setting = rows[0];
            })
        },
        methods: {
            save() {
                this.db("settings")
                    .update(this.setting)
                    .where("id", this.setting.id)
                    .then(() => {
                        this.alert("保存成功，将刷新应用", "success").then(val => {
                            window.location.reload();
                        })
                    });
            },
            alert(str, iconType = 'error') {
                return swal({
                    icon: iconType,
                    text: str,
                });
            },
            autoSaveIntervalSeconds() {
                if (this.setting.autosave_interval < 3) {
                    this.setting.autosave_interval = 3;
                    this.alert("自动保存时间间隔不能小于3秒");
                }
            },
            compressWidth() {
                if (this.setting.img_w < 500 && this.setting.img_w > 0) {
                    this.setting.img_w = 500;
                    this.alert("最小宽度不能小于500");
                }
            },
            compressHeight() {
                if (this.setting.img_h < 300 && this.setting.img_h > 0) {
                    this.setting.img_h = 300;
                    this.alert("最小高度不能小于300");
                }
            }
        }
    }
</script>
<style>
    .setting {
        display: flex;
        background: #fff;
        height: calc(100% - 32px);
        margin: 4px 12px 12px 12px;
        box-shadow: 0 1px 3px rgba(26, 26, 26, 0.2);
        border-radius: 3px;
        overflow: hidden;
    }

    .content {
        flex: 1;
        padding: 8px 18px 8px 18px;
        color: #666;
        line-height: 32px;
    }

    .leftMenu {
        width: 130px;
        line-height: 46px;
        text-align: center;
        display: flex;
        flex-direction: column;
    }

    .leftMenu .item {
        height: 46px;
        cursor: pointer;
        border-right: 2px solid #eee;
    }

    .leftMenu .item:hover {
        background: #f9f9f9;
    }

    .leftMenu .selected {
        background: #f0faff !important;
        border-right: 2px solid #1787fb;
    }

    .leftMenu .other {
        flex: 1;
        border-right: 2px solid #eee;
    }

    .formItem {
        height: 42px;
        line-height: 42px;
        color: #363636;
    }

    .formItem input {
        border: 1px solid #eee;
        border-radius: 3px;
        line-height: 22px;
        height: 22px;
        outline: none;
        text-align: center;
        width: 50px;
        font-size: 12px;
    }

    .formItem div {
        display: inline-block;
    }

    .formItem div:last-child {
        padding-left: 8px;
    }

    .rdBtn {
        cursor: pointer;
    }

    .icon-xuanzhong {
        color: #1787fb !important;
    }

    .icon-weixuanzhong {
        color: #aaa !important;
    }
</style>