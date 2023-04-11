FROM amazonlinux:2

RUN yum update -y && \
    yum install -y httpd && \
    yum clean all && \
    rm -rf /var/cache/yum/*

COPY index.html /var/www/html/
RUN echo "<!doctype html>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>转账汇款</title>
 
    <script type="text/javascript" src="js/jquery-3.4.1.min.js"></script>
 
 
    <style type="text/css">
        body {
            background-color:#00b38a;
            text-align:center;
        }
 
        .lp-login {
            position:absolute;
            width:500px;
            height:300px;
            top:50%;
            left:50%;
            margin-top:-250px;
            margin-left:-250px;
            background: #fff;
            border-radius: 4px;
            box-shadow: 0 0 10px #12a591;
            padding: 57px 50px 35px;
            box-sizing: border-box
        }
 
 
        .lp-login .submitBtn {
            display:block;
            text-decoration:none;
            height: 48px;
            width: 150px;
            line-height: 48px;
            font-size: 16px;
            color: #fff;
            text-align: center;
            background-image: -webkit-gradient(linear, left top, right top, from(#09cb9d), to(#02b389));
            background-image: linear-gradient(90deg, #09cb9d, #02b389);
            border-radius: 3px
        }
 
 
        input[type='text'] {
            height:30px;
            width:250px;
        }
 
        span {
            font-style: normal;
            font-variant-ligatures: normal;
            font-variant-caps: normal;
            font-variant-numeric: normal;
            font-variant-east-asian: normal;
            font-weight: normal;
            font-stretch: normal;
            font-size: 14px;
            line-height: 22px;
            font-family: "Hiragino Sans GB", "Microsoft Yahei", SimSun, Arial, "Helvetica Neue", Helvetica;
        }
 
    </style>
    <script type="text/javascript">
        $(function(){
            $(".submitBtn").bind("click",function(){
                var fromAccount = $("#fromAccount").val();
                var toAccount = $("#toAccount").val();
                var money = $("#money").val();
 
                if(money == null || $.trim(money).length == 0){
                    alert("sorry,必须输入转账金额~");
                    return;
                }
 
                $.ajax({
                    url:'/transferServlet',
                    type:'POST',    //GET
                    async:false,    //或false,是否异步
                    data:{
                        fromCardNo:fromAccount.split(' ')[1],
                        toCardNo:toAccount.split(' ')[1],
                        money:money
                    },
                    timeout:5000,    //超时时间
                    dataType:'json', //返回的数据格式：json/xml/html/script/jsonp/text
                    success:function(data){
                        if("200" == data.status){
                            alert("转账成功~~~");
                        }else{
                            alert("转账失败~~~,message:" + data.message);
                        }
                    }
                })
            })
        })
 
        //检查输入值是否为整数
        function checkFormat(obj){
            var reg = /^[0-9]+[0-9]*]*$/;
            if($.trim($(obj).val()).length>0){
                if(!reg.test($(obj).val())){
                    alert("输入格式错误！请输整数！");
                    $(obj).val("");
                }else{
                    $(obj).val(parseInt($(obj).val()));
                }
            }
        }
    </script>
</head>
<body>
 
 
<form>
    <table class="lp-login">
        <tr>
            <td align="right"><span>收款账户</span></td>
            <td align="center">
                <input type="text" id="toAccount" value="韩梅梅 6029621011001" disabled></input>
            </td>
        </tr>
        <tr>
            <td align="right"><span>付款账户</span></td>
            <td align="center">
                <input type="text" id="fromAccount" value="李大雷 6029621011000" disabled></input>
            </td>
        </tr>
        <tr>
            <td align="right"><span>转账金额</span></td>
            <td align="center">
                <input type="text" id="money" onblur="checkFormat(this)"></input>
            </td>
        </tr>
        <tr align="center">
            <td colspan="2">
                <a href="javasrcipt:void(0)" class="submitBtn"><span>转 出</span></a>
            </td>
        </tr>
    </table>
</form>
 
</body>
</html>" > /var/www/html/index.html 
EXPOSE 80

CMD ["/usr/sbin/httpd", "-D", "FOREGROUND"]
