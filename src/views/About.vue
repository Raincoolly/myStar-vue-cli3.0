<template>
    <div class="file_box ">
			<span class='upload'>
				<input type="file" name="" id="" value="" name="saveFile" @change="onChange"/>
         	    <img :src="form.imgURL" alt=""/>
				<i v-show="!form.img">添加图片</i>
			</span>
        <i class='title_dec' style="position: absolute;left: 4rem;top:5rem;">您可以上传1张图片</i>
    </div>
</template>
<script>
    import axios from 'axios'

    export default {
        components: {},
        data() {
            return {
                form: {
                    imgURL: "",
                    img: "",
                }
            }
        },
        methods: {
			onChange(){
				var reader = new FileReader();
				reader.onload = function (e) {
					compress(this.result);
				};
				// reader.readAsDataURL(this.files[0]);
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
					this.tirggerFile();
				}
				img.src = res;
			},
            tirggerFile(event) {
                let self = this;
                let file = event.target.files[0]
                let param = new FormData() // 创建form对象
                param.append('file', file, file.name) // 通过append向form对象添加数据
                param.append('type', '1') // 添加form表单中其他数据
                console.log(param.get('file')) // FormData私有类对象，访问不到，可以通过get判断值是否传进去
                let config = {
                    headers: {'Content-Type': 'multipart/form-data'}
                }
                // 添加请求头
                axios.post('upload/upImg', param, config)
                    .then(response => {
                        if (response.data.status == 200) {
                            self.form.img = response.data.data.img;
                            self.form.imgURL = 'http://www.baidu.com/' + response.data.data.img;
                        }
                    })
            },
        }
    }
</script>
<style>
    .upload[data-v-ddcf96a0] {
        width: 3rem;
        height: 3rem;
        display: inline-block;
        border-radius: 0.2rem;
        position: relative;
        margin-left: 1.12rem;
        margin-top: 0.78rem;
        /*background: #E5E5E5 url(‘1.png’) center center no-repeat;*/
        background-size: 0.72rem 0.72rem;
    }

    .upload input {
		position: absolute;
		width: 3rem;
		height: 3rem;
		top: 3rem;
		left: 0;
		opacity: 0;
		border: 1px solid #000;
		background: #000;
    }

    .file_box img {
        width: 3rem;
        height: 3rem;
        line-height: 3rem;
        display: block;
        float: left;
        border: 0.02rem solid #E5E5E5;
	  	margin-top:2rem;
    }

    .upload i {
        position: absolute;
        bottom: 0.36rem;
        left: 0.72rem;
        color: #FFFFFF;
        font-size: 0.4rem;
    }
</style>

