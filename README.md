
# multer/express图片上传实例
### 前端
```html

<form enctype="multipart/form-data" method="post">
    <input type="file" id="avatar" name="avatar" />
    <button>提交</button>
</form>
<script>
    $('button').click(function () {
        var files = $('#avatar').prop('files');
        var data = new FormData();
        data.append('avatar', files[0]);
        $.ajax({
            url: 'http://localhost:3000/uploadImg',
            type: 'POST',
            data: data,
            cache: false,
            processData: false,
            contentType: false
        });
        return false;
    });
</script>
```
### 后台
/router/uploadImg.js
```js
let multer = require('multer')

let storage = multer.diskStorage({
    destination : (req,file,cb)=>{
        //保存在public文件夹的upload文件夹里
        cb(null,path.join(__dirname, '../public/upload/'))
    },
    filename:(req,file,cb)=>{
        cb(null,file.originalname)
    }
})


let upload = multer({ storage: storage })
//单文件上传
router.post('/', upload.single('avatar'), function (req, res, next) {
    res.header("Access-Control-Allow-Origin", "*");
    res.send({
        code: 1, message: 'successs'
    })
})
//多文件上传，配置信息是跟单个是一样的，只是到逻辑这里不一样。
router.post('/solutionPic', upload.array('avatar'), function (req, res) {
    res.header('Access-Control-Allow-Origin', '*');
    try{
        let files = req.files;
        //记录图片地址数组
        let arr = [];
        for(let i= 0;i<files.length;i++){
            arr.push('/upload/solution/'+files[i].filename);
        }
        res.send({
            code: 0, message: 'successs', data: arr   
        });
    }catch(e){
        console.log(e)
        red.send({
            code:9,message:e
        })
    }
});
```
