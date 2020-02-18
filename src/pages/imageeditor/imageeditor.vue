<template>
    <view>
        <view class="container" id="container" @touchstart="touchStart" @touchend="touchEnd" @touchmove="touchMove">
            <image class="bg" :src="bgPic" />
            <icon type="cancel" class="cancel" id="cancel" :style="{top:cancelCenterY-10+'px',left:cancelCenterX-10+'px'}"></icon>
            <icon type="waiting" class="handle" id="handle" color="green" :style="{top:handleCenterY-10+'px',left:handleCenterX-10+'px'}"></icon>
            <image class="hat" id="hat" :src="`../../static/${currentHatId}.png`" :style="{top:hatCenterY-hatSize/2-2+'px',left:hatCenterX-hatSize/2-2+'px',transform:`rotate(${rotate}deg) scale(${scale})`}" />
        </view>
        <button @click="combinePic" type="primary">确定</button>
        <scroll-view class="scrollView" scroll-x="true">
            <image class="imgList" v-for="(img,index) in imgList" :key="index" :src="`../../static/${index+1}.png`" @click="chooseImage(index)" />
        </scroll-view>
    </view>
</template>

<script>
const app = getApp();
export default {
    data() {
        return {
            bgPic: "",
            imgList: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
            currentHatId: 1,

            hatCenterX: uni.getSystemInfoSync().windowWidth / 2,
            hatCenterY: 150,
            cancelCenterX: uni.getSystemInfoSync().windowWidth / 2 - 50 - 2,
            cancelCenterY: 100,
            handleCenterX: wx.getSystemInfoSync().windowWidth / 2 + 50 - 2,
            handleCenterY: 200,

            hat_center_x: "",
            hat_center_y: "",
            cancel_center_x: "",
            cancel_center_y: "",
            handle_center_x: "",
            handle_center_y: "",

            hatSize: 100,

            scale: 1,
            rotate: 0,

            scaleFinal: "",
            rotateFinal: "",
            touchTarget: "",
            startX: "",
            startY: ""
        };
    },
    onLoad(option) {
        console.log("测试option---->", JSON.stringify(option));
        console.log("测试globalData----->", JSON.stringify(app.globalData));
        console.log("测试globalData的bgPic---->", app.globalData.bgPic);
        this.bgPic = app.globalData.bgPic;
    },
    onReady() {
        this.hat_center_x = this.hatCenterX;
        this.hat_center_y = this.hatCenterY;
        this.cancel_center_x = this.cancelCenterX
        this.cancel_center_y = this.cancelCenterY
        this.handle_center_x = this.handleCenterX
        this.handle_center_y = this.handleCenterY
        this.scaleFinal = this.scale;
        this.rotateFinal = this.rotate;

        this.startX = 0;
        this.startY = 0;
    },
    methods: {
        chooseImage(index) {
            console.log("测试index------>", index);
            this.currentHatId = index + 1;
        },
        touchStart(e) {
            if (e.target.id == "hat") {
                this.touchTarget = "hat";
            } else if (e.target.id == "handle") {
                this.touchTarget = "handle";
            } else {
                this.touchTarget = "";
            }
            if (this.touchTarget) {
                this.startX = e.touches[0].clientX;
                this.startY = e.touches[0].clientY;
            }
        },
        touchEnd(e) {
            console.log("测试touchEnd");
            this.hat_center_x = this.hatCenterX
            this.hat_center_y = this.hatCenterY
            this.cancel_center_x = this.cancelCenterX
            this.cancel_center_y = this.cancelCenterY;
            this.handle_center_x = this.handleCenterX;
            this.handle_center_y = this.handleCenterY;
            this.touchTarget = "";
            this.scaleFinal = this.scale;
            this.rotateFinal = this.rotate;
        },
        touchMove(e) {
            let currentX = e.touches[0].clientX;
            let currentY = e.touches[0].clientY;
            let movedX = currentX - this.startX;
            let movedY = currentY - this.startY;
            if (this.touchTarget == "hat") {
                this.hatCenterX = this.hatCenterX + movedX;
                this.hatCenterY = this.hatCenterY + movedY;
                this.cancelCenterX = this.cancelCenterX + movedX;
                this.cancelCenterY = this.cancelCenterY + movedY;
                this.handleCenterX = this.handleCenterX + movedX;
                this.handleCenterY = this.handleCenterY + movedY;
            }
            if (this.touchTarget == "handle") {
                this.handleCenterX = this.handleCenterX + movedX;
                this.handleCenterY = this.handleCenterY + movedY;
                this.cancelCenterX =
                    2 * this.hatCenterX - this.handleCenterX;
                this.cancelCenterY =
                    2 * this.hatCenterY - this.handleCenterY;

                let diffXBefore = this.handle_center_x - this.hat_center_x;
                let diffYBefore = this.handle_center_y - this.hat_center_y;
                let diffXAfter = this.handleCenterX - this.hat_center_x;
                let diffYAfter = this.handleCenterY - this.hat_center_y;

                let distanceBefore = Math.sqrt(
                    diffXBefore * diffXBefore + diffYBefore * diffYBefore
                );
                let distanceAfter = Math.sqrt(
                    diffXAfter * diffXAfter + diffYAfter * diffYAfter
                );
                console.log("测试distanceBefore---->", distanceBefore);
                console.log("测试distanceAfter---->", distanceAfter);
                let angleBefore =
                    (Math.atan2(diffYBefore, diffXBefore) / Math.PI) * 180;
                let angleAfter =
                    (Math.atan2(diffYAfter, diffXAfter) / Math.PI) * 180;
                this.scale = (distanceAfter / distanceBefore) * this.scaleFinal;
                this.rotate = angleAfter - angleBefore + this.rotateFinal;
                console.log("测试scale------>", this.scale);
                console.log("测试rotate----->", this.rotate);
            }
            this.startX = currentX;
            this.startY = currentY;
        },
        combinePic() {
            app.globalData.scale = this.scaleFinal;
            app.globalData.rorate = this.rotateFinal;
            app.globalData.hatCenterX = this.hat_center_x;
            app.globalData.hatCenterY = this.hat_center_y;
            app.globalData.currentHatId = this.currentHatId;
            console.log(
                "测试合成globalData.hatCenterX--->",
                app.globalData.hatCenterX
            );
            console.log(
                "测试合成globalData.hatCenterY--->",
                app.globalData.hatCenterY
            );
            uni.navigateTo({
                url: "../combine/combine"
            });
        }
    }
};
</script>

<style>
.container {
    height: 300px;
    width: 100%;
}

.bg {
    position: absolute;
    z-index: 0;
    height: 300px;
    width: 300px;
    left: 50%;
    transform: translate(-50%, 0);
}
button {
    margin: 10px 20px 0px 20px;
}
.hat {
    height: 100px;
    width: 100px;
    position: absolute;
    border: dashed 2px yellow;
    top: 100px;
}
.handle,
.cancel {
    position: absolute;
    z-index: 1;
    width: 20px;
    height: 20px;
}
.scrollView {
    width: 100%;
    position: absolute;
    bottom: 5px;
    white-space: nowrap;
}

.imgList {
    height: 70px;
    width: 70px;
    border: 2px solid;
    border-color: #eeeeee;
    margin: 5px;
}
</style>
