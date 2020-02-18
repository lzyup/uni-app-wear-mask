<template>
    <view>
        <canvas class="myCanvas" canvas-id="myCanvas" style="height:300px;width:100%;" />
        <button type="primary" @click="savePic">保存到相册</button>
    </view>
</template>

<script>
const app = getApp();
export default {
    data() {
        return {
            bgPic: ""
        };
    },
    onLoad() {
        uni.getImageInfo({
            src: app.globalData.bgPic,
            success: res => {
                this.bgPic = res.path;
                this.draw();
            }
        });
    },
    onReady() {},

    methods: {
        draw() {
            let scale = app.globalData.scale;
            let rotate = app.globalData.rotate;
            let hat_center_x = app.globalData.hatCenterX;
            let hat_center_y = app.globalData.hatCenterY;
            let currentHatId = app.globalData.currentHatId;
            const pc = uni.createCanvasContext("myCanvas");
            const windowWidth = wx.getSystemInfoSync().windowWidth;
            const hatSize = 100 * scale;
            pc.clearRect(0, 0, windowWidth, 300);
            pc.drawImage(this.bgPic, windowWidth / 2 - 150, 0, 300, 300);
            pc.translate(hat_center_x, hat_center_y);
            pc.rotate((rotate * Math.PI) / 180);
            pc.drawImage(
                "../../static/" + currentHatId + ".png",
                -hatSize / 2,
                -hatSize / 2,
                hatSize,
                hatSize
            );
            pc.draw();
        },
        savePic() {
            const windowWidth = uni.getSystemInfoSync().windowWidth;
            uni.canvasToTempFilePath({
                x: windowWidth / 2 - 150,
                y: 0,
                height: 300,
                width: 300,
                canvasId: "myCanvas",
                success: res => {
                    app.globalData.successPic = res.tempFilePath;
                    uni.saveImageToPhotosAlbum({
                        filePath: res.tempFilePath,
                        success: res => {
                            // uni.navigateTo({
                            //     url:"../share/share.vue",
                            //     success:function(res){},
                            //     fail:function(res){},
                            //     complete:function(res) {
                            //     }
                            // })
                        },
                        fail(e) {
                            console.log("err:" + e);
                        }
                    });
                }
            });
        }
    }
};
</script>

<style>
button {
    margin: 10px 20px 0px 20px;
}
</style>
