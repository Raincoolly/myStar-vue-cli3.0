<template>
    <div id="app">
        <div class="title flex flex-column"></div>
        <div class="wrap flex flex-row">
            <div @click="control" class="control flex flex-column">
                <p class="open">开启摄像头</p>
                <p class="recognition">显示到Canvas</p>
                <p class="close">关闭摄像头</p>
            </div>
            <div class="scan reference">
                <div class="strainer"></div>
                <video class="capture" width="320" height="456" src=""></video>
            </div>

        </div>
    </div>
</template>
<script>
    export default {
        name: 'upload2',
        data() {
            return {
                buffer: null,
                oCapture: document.querySelector(".capture"),
                open: document.querySelector(".open"),
                recognition: document.querySelector(".recognition"),
                close: document.querySelector(".close"),

            }
        },
        mounted() {
            this.requestAnimFrame();
            var control = document.querySelector(".control");
        },
        methods: {
            invokingCarera() {
                if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
                    navigator.mediaDevices.getUserMedia({
                        'audio': true,
                        'video': {'facingMode': "user"}//调用前置摄像头，后置摄像头使用video: { facingMode: { exact: "environment" } }
                    })
                        .then(function (mediaStream) {
                            console.log(555);
                            this.getVideoStream(mediaStream)
                        })
                        .catch(function (error) {
                            console.log(666);
                            console.log(error)
                        })
                } else if (navigator.getUserMedia) {
                    navigator.getUserMedia({
                        'video': true,
                        'audio': true
                    }, this.getVideoStream, this.getFail)
                } else {
                    alert('不支持摄像头调用！')
                }
            },
            getFail() {
                alert(111);
            },
            getVideoStream(stream) {
                buffer = stream;
                if (this.oCapture.mozSrcObject !== undefined) {
                    this.oCapture.mozSrcObject = this.buffer;
                } else {
                    this.oCapture.src = window.URL && window.URL.createObjectURL(this.buffer);
                }
                this.oCapture.play();
            },
            control(e) {
                e = e || window.event;
                var className = e.target.className;
                switch (className) {
                    case 'open':
                        this.invokingCarera();
                        break;
                    case 'close':
                        this.closeCamera();
                        break;
                    case 'recognition':
                        this.screenShot();
                        break;
                    default:
                        break;
                }
            },
            closeCamera() {
                this.buffer && this.buffer.getTracks()[1].stop();//关闭摄像头
            },
            requestAnimFrame() {
                return window.requestAnimationFrame ||
                    window.webkitRequestAnimationFrame ||
                    window.mozRequestAnimationFrame ||
                    function (callback) {
                        window.setTimeout(callback, 1000 / 60);
                    };
            },
            screenShot() {
                let self = this;
                var canvas = document.createElement('canvas');
                canvas.width = 320, canvas.height = 456;
                document.querySelector(".wrap").appendChild(canvas);
                var ctx = canvas.getContext('2d');

                function drawVideo() {
                    ctx.drawImage(self.oCapture, 0, 0, 320, 456);
                    ctx.font = "30px sans-serif";
                    ctx.fillStyle = "blue";
                    ctx.fillText("请眨眼", 50, 50);
                    requestAnimationFrame(drawVideo);
                }

                window.requestAnimationFrame(drawVideo);
            },
        }
    }
</script>
<style>
    * {
        margin: 0;
        padding: 0;
    }

    .flex {
        display: flex;
    }

    .flex-row {
        flex-direction: row;
        justify-content: space-around;
        align-items: center;
    }

    .flex-column {
        flex-direction: column;
        justify-content: flex-start;
        align-items: center;
    }

    body {
        overflow: auto;
        background: #fff;
    }

    .title {
        width: 920px;
        padding: 30px;
        align-items: flex-end;
    }

    .title h1 {
        padding-bottom: 20px;
        font-size: 38px;
        color: #ffffff;
        text-shadow: 0 1px 3px #222222;
    }

    .title p {
        font-size: 18px;
        color: #f5f5f5;
        text-shadow: 0 1px 3px #565656;
    }

    .wrap {
        width: 1220px;
    }

    .wrap .reference {
        position: relative;
        padding: 10px;
        background-color: rgba(255, 255, 255, 0);
        border-radius: 10px;
        box-shadow: 0 0 20px #a1a19f;
    }

    .wrap .reference img.face {
        display: block;
        width: 320px;
        height: auto;
        border-radius: 10px;
    }

    .wrap .reference img.toggle {
        position: absolute;
        right: 10px;
        top: 10px;
        width: 50px;
        height: 50px;
    }

    .wrap .scan video {
        background-color: rgba(0, 0, 0, .8);
        border-radius: 10px;
    }

    .wrap .control {
        justify-content: space-around;
        height: 456px;
        padding: 0 20px;
    }

    .wrap .control p {
        width: 160px;
        height: 60px;
        background-color: #f9f9f9;
        text-align: center;
        line-height: 60px;
        color: #ffffff;
        font-size: 24px;
        border-radius: 8px;
        cursor: pointer;
        box-shadow: -8px -8px 150px -8px #b2b3b5 inset, 0 0 5px #222222;
        text-shadow: 0 0 1px #222222;
        transition: .5s;
    }

    .wrap .control p:hover {
        box-shadow: -8px -8px 150px -8px #50c4f1 inset, 0 0 5px #ffffff;
    }

    .wrap .scan {
        position: relative;
        overflow: hidden;
    }

    .wrap .scan .strainer {
        position: absolute;
        top: 10px;
        width: 320px;
        z-index: 999;
        height: 3px;
    }

    .wrap .scan .capture {
        width: 320px;
        height: 456px;
    }

    .wrap .scan .strainer.on {
        background: linear-gradient(to left, transparent, #0bffb2, transparent);
        animation: scan 1s linear infinite;
    }

    @keyframes scan {
        0% {
            top: 10px;
        }
        50% {
            top: 456px;
        }
        100% {
            top: 10px;
        }
    }
</style>
