<template>
    <view class="root">
        <view class="content">
            <image class="bgPic" v-if="bgPicUrl" :src="bgPicUrl" />
            <view class="emptyBg" v-else></view>
        </view>
        <view class="btnContainer">
            <button type="default" open-type="getUserInfo" @getuserinfo="bindGetUserInfo">使用头像</button>
            <button type="default" @click="chooseImage('camera')">使用相机</button>
            <button type="default" @click="chooseImage('album')">相册选择</button>
            <button type="primary" @click="nextPage" :disabled="picChoosed">下一步</button>
        </view>
    </view>
</template>

<script>
const app = getApp()
export default {
    data() {
        return {
            title: "Hello",
            bgPicUrl: "",
            picChoosed: true
        };
    },
    onLoad() {
    },
    methods: {
        bindGetUserInfo(e) {
            if (e.detail.userInfo) {
                this.bgPicUrl = e.detail.userInfo.avatarUrl;
            }
            if (this.bgPicUrl) {
                this.picChoosed = false;
            } else {
                this.picChoosed = true;
            }
            app.globalData.imageType = '0';
            app.globalData.bgPic = this.bgPicUrl;
        },
        chooseImage(from) {
            app.globalData.imageType = '1';
            uni.chooseImage({
                count: 1,
                sizeType: ["original", "compressed"],
                sourceType: [from],
                success: res => {
                    let tempFilePaths = res.tempFilePaths;
                    console.log('测试chooseImage---->',tempFilePaths);
                    this.bgPicUrl = tempFilePaths[0];
                    if (this.bgPicUrl) {
                        app.globalData.bgPic = this.bgPicUrl;
                        this.picChoosed = false;
                    } else {
                        this.picChoosed = true;
                    }
                }
            });
        },
        nextPage(){
            uni.navigateTo({
                url:'../imageeditor/imageeditor?'
            })
        }
    }
};
</script>

<style>
.root {
    width: 100%;
}
.content {
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 100%;
    height: 300px;
}

.bgPic,
.emptyBg {
    height: 300px;
    width: 300px;
    border: 2px solid;
    border-color: #eeeeee;
}

.emptyBg {
    border: 2px solid;
    border-color: #eeeeee;
}

button {
    width: 300px;
    margin-top: 10px;
}
</style>
