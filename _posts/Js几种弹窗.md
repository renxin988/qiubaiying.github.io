---
layout:     post
title:      JS
subtitle:   几种弹窗
date:       2018-04-08
author:     RX
header-img: img/post-bg-e2e-ux.jpg
catalog: true
tags:
    - java
---

# js的几种弹窗

>alert() 弹出个提示框 （确定）

>confirm() 弹出个确认框 （确定，取消）

>prompt() 弹出个输入框 让你输入东西

使用消息框 
使用警告、提示和确认 
可以使用警告、确认和提示消息框来获得用户的输入。这些消息框是 window 对象的接口方法。由于 window 对象位于对象层次的顶层，因此实际应用中不必使用这些消息框的全名（例如 "window.alert()"），不过采用全名是一个好注意，这样有助于您记住这些消息框属于哪个对象。 

警告消息框 
alert 方法有一个参数，即希望对用户显示的文本字符串。该字符串不是 HTML 格式。该消息框提供了一个“确定”按钮让用户关闭该消息框，并且该消息框是模式对话框，也就是说，用户必须先关闭该消息框然后才能继续进行操作。 

window.alert("欢迎！请按“确定”继续。"); 

确认消息框 

使用确认消息框可向用户问一个“是-或-否”问题，并且用户可以选择单击“确定”按钮或者单击“取消”按钮。confirm 方法的返回值为 true 或 false。该消息框也是模式对话框：用户必须在响应该对话框（单击一个按钮）将其关闭后，才能进行下一步操作。 

    var truthBeTold = window.confirm("单击“确定”继续。单击“取消”停止。"); 

    if (truthBeTold) { 

    window.alert("欢迎访问我们的 Web 页！"); 

    } else window.alert("再见啦！"); 

提示消息框 

提示消息框提供了一个文本字段，用户可以在此字段输入一个答案来响应您的提示。该消息框有一个“确定”按钮和一个“取消”按钮。如果您提供了一个辅助字符串参数，则提示消息框将在文本字段显示该辅助字符串作为默认响应。否则，默认文本为 "<undefined>"。 

与alert( ) 和 confirm( ) 方法类似，prompt 方法也将显示一个模式消息框。用户在继续操作之前必须先关闭该消息框 

var theResponse = window.prompt("欢迎？","请在此输入您的姓名。");

window.confirm 参数就只有一个.显示提示框的信息. 

按确定,返回true; 按取消返回false. 

    <script> 

    var bln = window.confirm("确定吗?");

    alert(bln) 

    </script> 


window.alert参数,只有一个,显示警告框的信息; 

无返回值. 
    <script> 
    window.alert("确定.") 
    </script> 

window.prompt参数,有两个, 
第一个参数,显示提示输入框的信息. 
第二个参数,用于显示输入框的默认值. 
返回,用户输入的值. 

    <script> 
    var str = window.prompt("请输入密码","password") 
    alert(str); 
    </script>  

    <html>
    <head>
    <title></title>
    </head>
    <script language="javascript" type="text/javascript">
    var yanzhengma = window.prompt("输入验证码", "")
    if( yanzhengma == 123 )
    {
        alert("ok");
    }
    else
    {
        alert("false");
    }
    </script>
    <body>
    </body>
    </html>


