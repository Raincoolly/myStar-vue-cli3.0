<template>
    <div id="app">
        <video
                id="webcam"
                :style="videoStyle"
                :width="videoWidth"
                :height="videoHeight"
                loop
                preload
                @click="initVideo"
        >
        </video>
        <div   id="webcam1"
               :style="videoStyle"
               :width="videoWidth"
               :height="videoHeight"
               loop
               preload
               @click="initVideo">

        </div>

        <input type="file" @change="onChange" accept="image/*">
        <input type="file" @change="onChange" accept="image/*" capture="camera">
    </div>
</template>

<script>

export default {
    name: 'upload',
    data() {
        return {
            videoStyle:"",
            videoWidth:"",
            videoHeight:"",
        }
    },
    components: {

    },
    methods: {
        initVideo() {
            let that = this;
            this.video = document.getElementById("webcam");
            setTimeout(() => {
                if (
                    navigator.mediaDevices.getUserMedia ||
                    navigator.getUserMedia ||
                    navigator.webkitGetUserMedia ||
                    navigator.mozGetUserMedia
                ) {
                    //调用用户媒体设备, 访问摄像头
                    this.getUserMedia(
                        {
                            video: {
                                width: {
                                    ideal: that.videoWidth,
                                    max: that.videoWidth
                                },
                                height: {
                                    ideal: that.videoHeight,
                                    max: that.videoHeight
                                },
                                facingMode: "user",    //前置摄像头
                                frameRate: {
                                    ideal: 30,
                                    min: 10
                                }
                            }
                        },
                        this.videoSuccess,
                        this.videoError
                    );
                } else {
                    alert("摄像头打开失败,请检查权限设置!===========");
                }
            }, 300);
        },
        getUserMedia(constraints, success, error) {
            if (navigator.mediaDevices.getUserMedia) {
                //最新的标准API
                navigator.mediaDevices
                    .getUserMedia(constraints)
                    .then(success)
                    .catch(error);
            } else if (navigator.webkitGetUserMedia) {
                //webkit核心浏览器
                navigator.webkitGetUserMedia(constraints, success, error);
            } else if (navigator.mozGetUserMedia) {
                //firfox浏览器
                navigator.mozGetUserMedia(constraints, success, error);
            } else if (navigator.getUserMedia) {
                //旧版API
                navigator.getUserMedia(constraints, success, error);
            }
        },
        videoSuccess(stream) {
            this.mediaStreamTrack = stream;
            this.video.srcObject = stream;
            this.video.play();
        },
        videoError(error) {
            console.error(error);
            alert("摄像头打开失败,请检查权限设置!");
        },

        //
        onChange(){
            var reader = new FileReader();
            reader.onload = function (e) {
                compress(this.result);
            };
            reader.readAsDataURL(this.files[0]);
        },
        compress:function (res) {
            var img = new Image(),
                maxH = 160;
            img.onload = function () {
                var cvs = document.createElement('canvas'),
                    ctx = cvs.getContext('2d');
                if(img.height > maxH) {
                    img.width *= maxH / img.height;
                    img.height = maxH;
                }
                cvs.width = img.width;
                cvs.height = img.height;
                ctx.clearRect(0, 0, cvs.width, cvs.height);
                ctx.drawImage(img, 0, 0, img.width, img.height);
                var dataUrl = cvs.toDataURL('image/jpeg', 0.6);
// 上传略
            }
            img.src = res;
        }
    },
}

</script>

<style>
    #webcam{
        border:1px solid #000;
    }
    #webcam1{
        border:1px solid #000;
        height: 7em;
    }
</style>
